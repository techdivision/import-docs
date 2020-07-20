---
title: 'Usage as PHAR'
---

Using Pacemaker Community as PHAR usually has no impact.

### Path within the PAHR file

Given the case, it is necessary to specify the path to a custom configuration file, e. g. when using the command to create an OK file, it'll be helpful when the path inside the PHAR file can be used. Inside Pacemaker Community, streams are used to load the configuration file. As has a native PHP support for reading data from [PHAR](https://www.php.net/manual/de/phar.using.intro.php) archives, this is possible by the notation `phar://<path-to-the-phar-file/<path-to-the-file-inside-the-phar-file>`. If for example, the configuration file for the attibute import should be specfified and the PHAR file is in the directory `./dist/import-cli-simple.phar`, this can be done with 

```sh
import-cli-simple.phar import:create:ok-file --configuration=phar://dist/import-cli-simple.phar/vendor/techdivision/import-attribute/etc/techdivision-import.json
```
