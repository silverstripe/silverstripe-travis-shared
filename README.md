# Silverstripe TravisCI configs

This repository contains the [reusable](https://docs.travis-ci.com/user/build-config-imports/) configuration scripts
for TravisCI.

*Warning*: this work is experimental and we do not provide any guarantees and do not promise any support for it.
We may introduce breaking changes with no warning.

## Example module .travis.yml files

### Minimal config for a standard module with a range of recipe versions

```yml
version: ~> 1.0

import:
  - silverstripe/silverstripe-travis-shared:config/provision/standard-jobs-range.yml
```

### Use a generic jobs matrix on a standard module with a range of recipe versions

This includes a minimum recipe version of 4.6.x-dev to match a composer requirement of a required core module of ^4.6

```yml
version: ~> 1.0

env:
  global:
    - COMPOSER_ROOT_VERSION=1.6.x-dev
    - REQUIRE_RECIPE=4.6.x-dev

import:
  - silverstripe/silverstripe-travis-shared:config/provision/standard-jobs-range.yml
```

### Use a generic jobs matrix with a fixed recipe version of 4.x-dev and with behat and npm on a CWP module

```yml
version: ~> 1.0

env:
  global:
    - COMPOSER_ROOT_VERSION=2.x-dev
    - REQUIRE_EXTRA="silverstripe/fulltextsearch:^3 silverstripe/spellcheck:^2"
    - REQUIRE_RECIPE=4.x-dev
    - BEHAT_SUITE="@magical-content"

import:
  - silverstripe/silverstripe-travis-shared:config/provision/cwp-jobs-fixed-behat-npm.yml
```

### Define a custom jobs matrix on a standard module and use silverstripe/recipe-core

```yml
version: ~> 1.0

env:
  global:
    - COMPOSER_ROOT_VERSION=4.6.x-dev
    - BEHAT_SUITE="@magical-content"

import:
  - silverstripe/silverstripe-travis-shared:config/provision/core.yml

jobs:
  include:
    - php: 5.6
      env:
        - DB=MYSQL
        - REQUIRE_CORE=~4.4.0@dev
        - PHPUNIT_TEST=1
    - php: 7.3
      env:
        - DB=MYSQL
        - REQUIRE_CORE=4.x-dev
        - BEHAT_TEST=1
```
