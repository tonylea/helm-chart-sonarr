# Changelog

All notable changes to this project will be documented in this file. See [commit-and-tag-version](https://github.com/absolute-version/commit-and-tag-version) for commit guidelines.

## [3.1.0](https://github.com/tonylea/helm-chart-sonarr/compare/v3.0.1...v3.1.0) (2026-11-26)

### Features

* make the liveness and readiness probe timeouts configurable ([0177c79](https://github.com/tonylea/helm-chart-sonarr/commit/0177c79a0799f45cd2d2b1287a19833b1ff845b8))

## [3.0.1](https://github.com/tonylea/helm-chart-sonarr/compare/v3.0.0...v3.0.1) (2026-11-21)

### Bug Fixes

* let Sonarr's first-run screen set the login ([e33602e](https://github.com/tonylea/helm-chart-sonarr/commit/e33602eb06a0dbd8d616f469f8e2e109a3771020))

### Documentation

* say the kind test is a fresh install ([c167d27](https://github.com/tonylea/helm-chart-sonarr/commit/c167d273bc77f811a0f7620bc51c285731d66ce8))

## [3.0.0](https://github.com/tonylea/helm-chart-sonarr/compare/v2.0.0...v3.0.0) (2026-11-21)

### ⚠ BREAKING CHANGES

* persistence.data is now a list of named mounts, each at
  /data/<name>, with at least one required.

### Features

* mount each data volume at /data/<name> ([1bb35c8](https://github.com/tonylea/helm-chart-sonarr/commit/1bb35c8632619132b3571fac65d3b139d0ad014d))

### Documentation

* show the chart's download count ([b10f63b](https://github.com/tonylea/helm-chart-sonarr/commit/b10f63bd19f686c7859c83c97b936736ff71a6d5))

## [2.0.0](https://github.com/tonylea/helm-chart-sonarr/compare/v1.0.0...v2.0.0) (2026-11-21)

### ⚠ BREAKING CHANGES

* postgres.database is now postgres.databases.main,
  and Sonarr needs a log database, postgres.databases.log.

### Features

* keep Sonarr's log in its own Postgres database ([9b25b2a](https://github.com/tonylea/helm-chart-sonarr/commit/9b25b2ad17db92a407b0ecc0caafef34dbabe2b0))

### Bug Fixes

* leave resource requests to whoever deploys the chart ([15c1dfb](https://github.com/tonylea/helm-chart-sonarr/commit/15c1dfb3c25670614074d8a003312639a250da6f))

### Documentation

* keep only the comments that record a decision ([7ec98c7](https://github.com/tonylea/helm-chart-sonarr/commit/7ec98c7c38c5632494eb3f340112ed2302ec1851))
* keep the README to the fixed settings and what the chart needs ([102e38c](https://github.com/tonylea/helm-chart-sonarr/commit/102e38c221dfd7bba7b4efbf047070547cfd03b9))
* link the Servarr wiki for how to lay out /data ([314cd74](https://github.com/tonylea/helm-chart-sonarr/commit/314cd744e237c09a0ecad2ae855aade29488cfda))

## 1.0.0 (2026-11-21)

### Features

* deploy Sonarr on Postgres, with a fixed API key, a required login and claims for /config and /data ([61db916](https://github.com/tonylea/helm-chart-sonarr/commit/61db91676187e7f38f5b6d854f5611437c7f21a1))

### Documentation

* describe the chart, its values and how to contribute ([0d6eeda](https://github.com/tonylea/helm-chart-sonarr/commit/0d6eedadb5edcfeca2a4c2fc398e768616f2d92f))
