# Silverstripe TravisCI configs

This repository contains the [reusable](https://docs.travis-ci.com/user/build-config-imports/) configuration scripts
for TravisCI.

*Warning*: this work is experimental and we do not provide any guarantees and do not promise any support for it.
We may introduce breaking changes with no warning.

## Provisions

Choose a provision from the provisions directory that matches your requirements.  Provisions that include the word
"jobs" will include a generic jobs matrix.

Any extra configuration in the modules .travis.yml will be merged with the provisions config.  For instance you can
define additional jobs that will be merged with the generic jobs matrix.
For example https://github.com/silverstripe/silverstripe-admin/blob/1/.travis.yml

### Provision types:
- standard: Use this for most modules. Base recipe is silverstripe/recipe-cms
- cwp: Use this for CWP modules. Base recipe is cwp/cwp-recipe-cms and will require CWP starter theme
- self: Uses this for modules that are themselves recipes. Will not require any additional recipes.
- cwp-self: Use this for modules that are themselves CWP recipes. Will not require any additional recipes.

### Job matrix types:
- range: Include a variety of recipe versions e.g. 4.5.x-dev, 4.6.x-dev, 4.7.x-dev, 4.x-dev
- fixed: Used a fixed recipe version which is defined in the modules .travis.yml e.g. REQUIRE_RECIPE="4.x-dev"
         The use case for this core modules that are in lockstep such as silverstripe-admin

### Additional jobs:
- behat: Run behat tests. Requires that $BEHAT_SUITE is defined in the modules .travis.yml
- npm: Run npm tests i.e. yarn test

## Example module .travis.yml files

### Minimal config for a standard module with a range of recipe versions

```yml
version: ~> 1.0

import:
  - silverstripe/silverstripe-travis-shared:config/provision/standard-jobs-range.yml
```

### Use a generic jobs matrix with a fixed recipe version of 4.x-dev and with behat and npm on a CWP module

Note: Omit `@` for behat suite, so use `BEHAT_SUITE="asset-admin"` instead of BEHAT_SUITE="@asset-admin"
otherwise the yml will not be parsed correctly.

```yml
version: ~> 1.0

import:
  - silverstripe/silverstripe-travis-shared:config/provision/cwp-jobs-fixed-behat-npm.yml

env:
  global:
    - COMPOSER_ROOT_VERSION="3.x-dev"
    - REQUIRE_RECIPE="2.x-dev"
    - REQUIRE_EXTRA="silverstripe/fulltextsearch:^3 silverstripe/spellcheck:^2"
    - BEHAT_SUITE="asset-admin"
```

### Define a custom jobs matrix on a standard module

```yml
version: ~> 1.0

import:
  - silverstripe/silverstripe-travis-shared:config/provision/standard.yml

env:
  global:
    - COMPOSER_ROOT_VERSION="4.6.x-dev"
    - BEHAT_SUITE="asset-admin"

jobs:
  include:
    - php: 5.6
      env:
        - DB=MYSQL
        - REQUIRE_INSTALLER=~4.4.0@dev
        - PHPUNIT_TEST=1
    - php: 7.3
      env:
        - DB=MYSQL
        - REQUIRE_INSTALLER=4.x-dev
        - BEHAT_TEST=1
```

### Use a generic jobs matrix on a recipe module

Use the 'self' provision for recipes modules.  The 'self' provision does not define a recipe to install, as the module itself is the recipe.

```yml
version: ~> 1.0

import:
  - silverstripe/silverstripe-travis-shared:config/provision/self-jobs-range.yml

env:
  global:
    - COMPOSER_ROOT_VERSION="4.x-dev"
```

## Use a range provsion and define a minimum recipe version to match a minimum version in composer.json

This includes a minimum recipe version of 4.6.x-dev to match a composer requirement of a required core module of ^4.6

NOTE: Appending ` || ` to `REQUIRE_RECIPE` on a range matrix is necessary to produce a valid Composer version requirement.

```yml
version: ~> 1.0

env:
  global:
    - COMPOSER_ROOT_VERSION="1.6.x-dev"
    - REQUIRE_RECIPE="4.6.x-dev || "

import:
  - silverstripe/silverstripe-travis-shared:config/provision/standard-jobs-range.yml
```