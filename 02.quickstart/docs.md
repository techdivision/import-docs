---
title: Quickstart
redirect: /quickstart/installation
visible: true
---

To install the Magnento 2 Import Framework, composer is necessary. The framework itself is a set of components that provide import functionality for Magento 2. This repository, based on Symfony Console, uses the the package [M2IF](https://github.com/techdivision/import) and provides a command line tool with import functionality for Magento 2 standard CSV files.

### Install as Composer Project

To install the package as a new project, assuming composer is available, open a console and enter

```sh
$ composer create-project techdivision/import-cli-simple --no-dev --stability=alpha
```

This will clone the repository from the internal Gitlab and install the M2IF, that's all.

### Install as Composer Library

The second option will be the installation as a Composer library. For example, if you want to deliver it with your Magento 2 project, simply add

```json
{
  "require": {
    "techdivision/import-cli-simple" : "1.0.0-beta3"
  }
}
```

to your Magento 2 composer.json file. Then run

```sh
$ composer update
```

from your Magento 2 root directory and your're all setup.

### Use as PHAR

The last, but for sure not the worst installation option, is to download the latest PHAR from our [Github](https://github.com/techdivision/import-cli-simple/releases) release page, e. g. with `wget`

```sh
$ wget https://github.com/techdivision/import-cli-simple/releases/download/1.0.0-alpha56/import-cli-simple.phar
```

To install globally put `import-cli-simple.phar` in `/usr/bin`, e. g.

```sh
$ sudo chmod +x import-cli-simple.phar && mv import-cli-simple.phar /usr/bin/import-cli-simple
```

Now you can use it just like `import-cli-simple`.