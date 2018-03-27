---
title: Configuration
visible: true
---

The necessary configuration has to be defined in a simple JSON file. An example that imports the Magento 2 
sample data in a Magento 2 CE version 2.1.2 can be found in our [examples](projects/sample-data/ce/212/conf/techdivision-import.json).

If **NO** configuration file (option `--configuration`) has been specified, the default one, defined by the
executed command will be used. In case of the `import:products`, the configuration file provided by the
library `techdivision/import-product` in the directory `etc/techdivision-import.json` will be used. This
default configuration contains **NO** database configuration and **NO** image directory.

The database configuration can be specified by the commandline options, but if images should be imported.

> If images should be imported, a custom configuration file with the paths to the image files has to be specified.

The import command itself supports a argument as well as several options.

## Arguments

The following configuration arguments are available:

| Argument             | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| operation            | Specify the operation name to execute, either one of add-update, replace or delete | n/a |

## Options

The following configuration options are available:

| Option               | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| --configuration      | Specify the pathname to the configuration file to use | `./vendor/techdivision/import-product/etc/techdivision-import.json` |
| --pid-filename       | The explicit PID filename to use | `<system-temp-dir>/importer.pid` |
| --system-name        | The system name to be used (will added to the mail subject, if mails are configured) | The hostname |
| --installation-dir   | The Magento installation directory to which the files has to be imported | The actual working directory |
| --entity-type-code   | The Magento entity type code, **MUST** be one of `catalog_product` or `catalog_category`  | n/a |
| --source-dir         | The directory that has to be watched for new files | n/a |
| --target-dir         | The target directory with the files that has been imported | n/a |
| --archive-dir        | The directory with the archived files that has been imported | n/a |
| --archive-artefacts  | The flag to activate the artefact archiving functionality | `true` |
| --magento-edition    | The Magento edition to be used, either one of CE or EE | n/a |
| --magento-version    | The Magento version to be used, e. g. 2.1.2 | n/a |
| --source-date-format | The date format used in the CSV file(s) | n/a |
| --use-db-id          | The ID of the database to use, if not specified, the database with the default flag will be used | n/a |
| --db-pdo-dsn         | The DSN used to connect to the Magento database where the data has to be imported, e. g. `mysql:host=127.0.0.1;dbname=magento` | n/a |
| --db-username        | The username used to connect to the Magento database | n/a |
| --db-password        | The password used to connect to the Magento database | n/a |
| --debug-mode         | The flag to activate the debug mode | `false` |
| --log-level          | The log level to use (see Monolog documentation for further information) | `info` |
| --single-transaction | The flag to wrap the import process into a single transaction | `false` |

Beside the `configuration` option, all options can and **SHOULD** be defined in the configuration file. The 
commandline options should only be used to override these values in some circumstances.

If the `configuration` option has **NOT** been specified, the system tries to locate the Magento Edition, based
on the specified `installation-dir` option. If the `installation-dir` option **IS** specified explictly, and the
directory is a valid Magento root directory, the application tries to load database credentials from the 
`app/etc/env.php` script, so it is **NOT** necessary to specify a database configuration, nor in the configuration 
file or as commandline parameter.

## Configuration File

The configuration file **MUST** be in JSON format. Beside itself, all necessary configuration options/arguments
that can be passed on on the commandline, can and **SHOULD** be defined in the configuration file, instead.

The structure is separated into a general configuration section, the database configuration and the configuration
for the available operations.

```json
{
  "magento-edition": "CE",
  "magento-version": "2.1.2",
  "operation-name" : "replace",
  "installation-dir" : "/var/www/magento",
  "databases" : [ ... ],
  "loggers" : [ ... ],
  "operations" : { ... }
}
```
