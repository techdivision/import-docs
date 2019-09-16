---
title: 'Usage as PHAR'
---

Using M2IF as PHAR usually has no impact.

### Path within the PAHR file

Given the case, it is necessary to specify the path to a custom configuration file, e. g. when using the command to create an OK file, it'll be helpful when the path inside the PHAR file can be used. This can be done by 

```sh
import-cli-simple.phar import:create:ok-file --configuration=phar://dist/import-cli-simple.phar/vendor/techdivision/import-attribute/etc/techdivision-import.json
```
