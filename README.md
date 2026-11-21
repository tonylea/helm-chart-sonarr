# Sonarr Helm Chart

Deploys [Sonarr](https://sonarr.tv/) from LinuxServer's image, [`lscr.io/linuxserver/sonarr`](https://github.com/linuxserver/docker-sonarr), the one Sonarr's docs recommend.
Sonarr watches for new episodes of the series it's given, sends them to a download client, and imports and renames them into the library.

The chart is opinionated: Sonarr keeps its data in Postgres, uses a fixed API key, and always asks for a login.
It runs one replica:

- A Deployment that stops the old pod before starting the new one, since two copies of Sonarr can't share one database.
- A volume for `/config` (`config.xml`, logs, backups and the artwork cache) and one for `/data` (the library and the download folders), each an existing claim or a claim the chart creates.
- A Service on Sonarr's port, 8989, and an optional Gateway API `HTTPRoute` to it.
- A start-up, liveness and readiness probe on `/ping`, which needs no login.
  Start-up gets 5 minutes, since the first start creates Sonarr's schema in Postgres.

## Values

| Value | Default |
| --- | --- |
| `image.tag` | the chart's `appVersion` |
| `puid` | `1000` |
| `pgid` | `1000` |
| `timezone` | `UTC` |
| `apiKey.secretKeyRef.name` | none; required |
| `apiKey.secretKeyRef.key` | `api-key` |
| `postgres.host` | none; required |
| `postgres.port` | `5432` |
| `postgres.databases.main` | `sonarr-main` |
| `postgres.databases.log` | `sonarr-log` |
| `postgres.username.secretKeyRef.name` | none; required |
| `postgres.username.secretKeyRef.key` | `username` |
| `postgres.password.secretKeyRef.name` | none; required |
| `postgres.password.secretKeyRef.key` | `password` |
| `resources` | none |
| `persistence.config.existingClaim` | none; the chart creates a claim |
| `persistence.config.storageClassName` | the cluster's default class |
| `persistence.config.size` | `2Gi` |
| `persistence.data.existingClaim` | none; the chart creates a claim |
| `persistence.data.storageClassName` | the cluster's default class |
| `persistence.data.size` | none; required unless `existingClaim` is set |
| `httpRoute.enabled` | `false` |
| `httpRoute.parentRefs` | none; required when enabled |
| `httpRoute.hostnames` | none |

### What the chart sets

These are set as environment variables, which override `config.xml`, so they can't be changed in the web UI.

| Setting | Value | Why |
| --- | --- | --- |
| API key | from `apiKey`'s Secret | A fixed key survives `/config` being rebuilt, and anything that talks to Sonarr can be given it up front. |
| Authentication method | `Forms` | A login page with a session. |
| Authentication required | `Enabled` | Behind a gateway or ingress every request comes from a private address, so `DisabledForLocalAddresses` would turn the login off for everyone. |
| Postgres | from `postgres` | Sonarr's main and log databases. |

The login's user and password are made in the web UI on first start.
Everything else (root folders, quality profiles, indexers, download clients) is kept in the database and set in the web UI.

### Postgres

Sonarr needs two databases, `postgres.databases.main` and `.log`, owned by the user in the Secret; Sonarr creates its tables in them.
The user and password can come from one Secret or two.
Their keys default to those of a `kubernetes.io/basic-auth` Secret, which is also how CloudNativePG names them.

### User and group

LinuxServer's image starts as root, gives `/config` to `puid` and `pgid`, then runs Sonarr as them.
They need to be able to write to `/data` as well.

### Storage

`/data` holds the library and the download folders together, as the [Servarr wiki](https://wiki.servarr.com/docker-guide) recommends.
An import is then a hardlink or a rename rather than a copy, which only works when both are on one filesystem.
Point Sonarr's root folder and the download client's folder at paths under `/data`.

A claim the chart creates is `ReadWriteOnce`.
To share `/data` with a download client, give an existing claim that both mount.

## Requirements

- A Postgres server, with the database and user made beforehand.
- A Secret holding the API key, and one or two holding the Postgres user and password.
- A storage class for the claims the chart creates, the cluster's default unless named.
- The Gateway API's `HTTPRoute` CRD, when `httpRoute.enabled` is set.

## Usage

```sh
helm repo add sonarr https://tonylea.github.io/helm-chart-sonarr
helm install sonarr sonarr/sonarr --values my-values.yaml
```

```yaml
timezone: Europe/London
apiKey:
  secretKeyRef:
    name: sonarr-api-key
postgres:
  host: sonarr-postgres-rw
  username:
    secretKeyRef:
      name: sonarr-postgres-app
  password:
    secretKeyRef:
      name: sonarr-postgres-app
persistence:
  data:
    existingClaim: media
```

## Development

Pull requests run:

- `helm lint --strict`, and the rendered chart checked by kubeconform against Kubernetes 1.37.
- helm-unittest's tests in `tests/`.
- An install and upgrade with chart-testing in a kind cluster, against a throwaway Postgres from `ci/kind`.

Commits follow Conventional Commits.
Releases are versioned and tagged automatically from `main`, and published to the Helm repository on this repo's GitHub Pages.
