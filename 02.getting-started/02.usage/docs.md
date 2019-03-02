---
title: Usage
visible: true
---

After installation, the importer is ready-to-run. When you're in the root of your Magento 2 installation, you do **NOT** need to specify the configuration for the Workflow Engine, because The importer parses the installation's configuration under `app/etc/env.php` and loads the Magento Edition/Version as well as the database configuration. By default, the importer searches for CSV files in the directory `var/importexport`. The files **MUST** have the prefix `product-import`, followed by a date/timestamp like `20180403-190920`, a consecutive number like `01` and the file suffix `.csv`.

Assuming, your CSV file `var/importexport/product-import_20180403-190920_01.csv` is ready to be imported and you're using the PHAR, you can start the importer with 

```
bin/import-cli-simple.phar import:create:ok-file && bin/import-cli-simple.phar import:products
```
whereas the first command creates the mandatory `.OK` file that signals, that all import artefacts are on-place and the second command finally starts the import with the [add-update](#operations) operation on your Magento 2 installation. The import commands supports an argument as well as several options.

### Commands

Beside the import commands several other more or less helper commands are available. The following commands for importing the entities are available:

| Argument                  | Description                                                     | Format |
|:--------------------------|:----------------------------------------------------------------|:-----------------|
| import:categories         | Starts importing categories | Category Import |
| import:customers          | Starts importing customers | Customer Import |
| import:customers:address  | Starts importing customer addresses, expects that the customers are available | add-update |
| import:attributes:set     | Starts importing attribute sets and their groups | Attribute Set + Group Import |
| import:attributes         | Starts importing attributes, expects that the referenced attribute sets + groups are available | Attribute Import |
| import:products           | Starts the product import, expects that the referenced attributes as well as the attribute sets and groups are available | Product Import |
| import:products:inventory | Starts importing product inventory, expects that the products are available | Product Import |
| import:products:price     | Starts importing product prices, expects that the productds are available | Product Import |

By default, if no other `source-dir` has been configured, either as command line option or in the configuration file, all commands are searching for the CSV files and the matching OK file in the `var/importexport` directory of your Magento installation.

### Arguments

The following configuration arguments are available:

| Argument             | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| operation            | Specify the operation name to execute, either one of add-update, replace or delete | n/a |

### Options

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
| --params             | A JSON encoded string that'll be merged with the params from the configuration file (has to be in the same format) | n/a |
| --params-file        | The path to a file with the JSON encoded params that will be merged with the params from the configuration file  (has to be in the same format)| n/a |

Beside the `configuration` option, all options can and **SHOULD** be defined in the configuration file. The commandline options should only be used to override these values in some circumstances.

If the `configuration` option has **NOT** been specified, the system tries to locate the Magento Edition, based on the specified `installation-dir` option. If the `installation-dir` option **IS** specified explictly, and the directory is a valid Magento root directory, the application tries to load database credentials from the `app/etc/env.php` script, so it is **NOT** necessary to specify a database configuration, nor in the configuration file or as commandline parameter.

### Operations

As well as the Magento 2 standard import functionality, M2IF will provide 3 different import operations:

| Operation                 | Description
|:--------------------------|:-----------------------------------------------------------------------------------|
| add-update                | New product data is added to the existing product data for the existing entries in the database. All fields except sku can be updated. New tax classes that are specified in the import data are created automatically. New SKUs that are specified in the import file are created automatically. |
| replace                   | The existing product data is replaced with new data. If a SKU in the import data matches the SKU of an existing entity, all fields, including the SKU are deleted, and a new record is created using the CSV data. An error occurs if the CSV file references a SKU that does not exist in the database. |
| delete                    | Any entities in the import data that already exist in the database are deleted from the database. Delete ignores all columns in the import data, except for SKU. You can disregard all other attributes in the data. An error occurs if the CSV file references a SKU that does not exist in the database. |

> Exercise caution when replacing data because the existing product data will be completely cleared and all references in the system will be lost.

### Debug Mode

The debug mode provides a more detailed logging output, by automatically setting the Monolog log level to `LogLevel::DEBUG` if **NOT** overwritten with the commandline option `--log-level`. Additionally it ignores

* product category relations to categories that not exists 
* product links (related, upsell, crosssell, etc.) for SKUs which are **NOT** available
* configurable products for SKUs which are **NOT** available or available more than one time

but logs these issues as warnings to the console.

When the debug mode has been enabled, missing attribute option values will **NOT** throw an exception, instead they will logged and put on an internal stack. If the [MissingOptionValuesPlugin](/framework/plug-ins?#missing-option-values) has been enabled, a CSV file with the missing option values will be created in the temporary import folder. If a Swift Mailer has been enabled by the plugin configuration, the CSV file will be sent to the given mail addresses.

This will help developers to test imports with partially invalid CSV files which do **NOT** break data consistency.

### Running Parallel Imports

To avoid unwanted behaviour, only one import process can be started at a time. To make sure, that only one process is running, a PID file in the system's temporary directory (`sys_get_temp_dir()`) is created which contains the UUID of the actual import process. After the import process has been finished, the file will be deleted and a new process can be started.