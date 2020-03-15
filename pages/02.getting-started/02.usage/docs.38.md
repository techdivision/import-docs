---
title: Usage
taxonomy:
    category:
        - docs
visible: true
---

!! ATTENTION: As of version 3.8.0, the structure of the configuration has changed considerably and the previous configuration files can no longer be used. In order to avoid complex adjustments of the configuration, version 3.8.0 merged the configuration for all entities into one, but dedicated overwriting of individual settings is now possible, e. g. for the log level.

After installation, the importer is ready-to-run. When you're in the root of your Magento 2 installation, you do **NOT** need to specify the configuration for the Workflow Engine, because The importer parses the installation's configuration under `app/etc/env.php` and loads the Magento Edition/Version as well as the database configuration. By default, the importer searches for CSV files in the directory `var/importexport`. The files **MUST** have the prefix `product-import`, followed by a date/timestamp like `20180403-190920`, a consecutive number like `01` and the file suffix `.csv`.

Assuming, your CSV file `var/importexport/product-import_20180403-190920_01.csv` is ready to be imported and you're using the PHAR, you can start the importer with 

```
bin/import-cli-simple.phar import:create:ok-file && bin/import-cli-simple.phar import:products
```
whereas the first command creates the mandatory `.OK` file that signals, that all import artefacts are on-place and the second command finally starts the import with the [add-update](#operations) operation on your Magento 2 installation. The import commands supports an argument as well as several options.

### Commands

Beside the import commands several other more or less helper commands are available. The following commands for importing the entities are available:

| Argument                      | Description                                                     | Format |
|:------------------------------|:----------------------------------------------------------------|:-----------------|
| import:categories             | Starts importing categories | [Customer + Customer Address Import](/file-structure/category-import) |
| import:customers              | Starts importing customers | [Customer + Customer Address Import](/file-structure/customer-and-customer-address-import) |
| import:customers:address      | Starts importing customer addresses, expects that the customers are available | [Customer + Customer Address Import](/file-structure/customer-and-customer-address-import) |
| import:attributes:set         | Starts importing attribute sets and their groups | [Attribute Set + Group Import](/file-structure/attributes-set-and-group-import) |
| import:attributes             | Starts importing attributes, expects that the referenced attribute sets + groups are available | [Attribute Import](/file-structure/attributes) |
| import:products               | Starts the product import, expects that the referenced attributes as well as the attribute sets and groups are available | [Product Import](/file-structure/product-import) |
| import:products:inventory     | Starts importing product inventory, expects that the products are available | [Product Import](/file-structure/product-import) |
| import:products:inventory:msi | Starts importing product MSI inventory, expects that the products are available | [Product Import // MSI](/file-structure/product-import-msi) |
| import:products:price         | Starts importing product prices, expects that the products are available | [Product Import](/file-structure/product-import) |
| import:products:price:tier    | Starts importing product tier prices, expects that the products are available | [Product Import // Tier Price](/file-structure/product-import-tier-price) |
| import:products:url           | Starts importing product product URLs, expects that the products are available | [Product Import // URL Rewrites](/file-structure/product-url-rewrites) |

By default, if no other source directory has been configured, either as command line option or in the configuration file, all commands are searching for the CSV files and the matching OK file in the `var/importexport` directory of your Magento installation.

### Arguments

For the [commands](#commands) described above, the following configuration arguments are available:

| Argument             | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| shortcut             | Specify the shortcut name to execute, either one of validate, convert, add-update, replace or delete (for further information look at the next [section](#shortcuts))| n/a |

### Shortcuts

In contrast to the Magento 2 standard import functionality, up from version 3.8.0, M2IF will provide 2 additional import shortcuts (formerly operations):

| Shortcut                  | Description
|:--------------------------|:-----------------------------------------------------------------------------------|
| <nobr>validate</nobr>     | Performs an explicit validation of the data in the respective CSV file (available for **ALL** entities)  |
| <nobr>convert</nobr>      | Extracts the attributes or categories from a given CSV files with products (available for **ATTRIBUTES** and **CATEGORIES**)  |
| <nobr>add-update</nobr>   | New product data is added to the existing product data for the existing entries in the database. All fields except sku can be updated. New tax classes that are specified in the import data are created automatically. New SKUs that are specified in the import file are created automatically. |
| <nobr>replace</nobr>      | The existing product data is replaced with new data. If a SKU in the import data matches the SKU of an existing entity, all fields, including the SKU are deleted, and a new record is created using the CSV data. An error occurs if the CSV file references a SKU that does not exist in the database. |
| <nobr>delete</nobr>       | Any entities in the import data that already exist in the database are deleted from the database. Delete ignores all columns in the import data, except for SKU. You can disregard all other attributes in the data. An error occurs if the CSV file references a SKU that does not exist in the database. |

!! Exercise caution when replacing data because the existing product data will be completely cleared and all references in the system will be lost.

### Options

The following configuration options are available:

| Option               | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| <nobr>--serial</nobr>             | Specify the unique identifier of this import process which will also be name of the temporary import directory | Some UUID |
| <nobr>--configuration</nobr>      | Specify the pathname to the configuration file to use | n/a |
| <nobr>--custom-configuration-dir</nobr>      | Specify the path to the custom configuration directory containing snippets to override the default values with | n/a |
| <nobr>--pid-filename</nobr>       | The explicit PID filename to use | `<system-temp-dir>/importer.pid` |
| <nobr>--system-name</nobr>        | The system name to be used (will added to the mail subject, if mails are configured) | The hostname |
| <nobr>--installation-dir</nobr>   | The Magento installation directory to which the files has to be imported | The actual working directory |
| <nobr>--source-dir</nobr>         | The directory that has to be watched for new files | n/a |
| <nobr>--target-dir</nobr>         | The target directory with the files that has been imported | n/a |
| <nobr>--archive-dir</nobr>        | The directory with the archived files that has been imported | n/a |
| <nobr>--archive-artefacts</nobr>  | The flag to activate the artefact archiving functionality | `true` |
| <nobr>--magento-edition</nobr>    | The Magento edition to be used, either one of CE or EE | n/a |
| <nobr>--magento-version</nobr>    | The Magento version to be used, e. g. 2.1.2 | n/a |
| <nobr>--use-db-id</nobr>          | The ID of the database to use, if not specified, the database with the default flag will be used | n/a |
| <nobr>--db-pdo-dsn</nobr>         | The DSN used to connect to the Magento database where the data has to be imported, e. g. `mysql:host=127.0.0.1;dbname=magento` | n/a |
| <nobr>--db-username</nobr>        | The username used to connect to the Magento database | n/a |
| <nobr>--db-password</nobr>        | The password used to connect to the Magento database | n/a |
| <nobr>--db-table-prefix</nobr>    | The table prefix used by the Magento database | n/a |
| <nobr>--debug-mode</nobr>         | The flag to activate the debug mode | `false` |
| <nobr>--log-level</nobr>          | The log level to use (see Monolog documentation for further information) | `info` |
| <nobr>--single-transaction</nobr> | The flag to wrap the import process into a single transaction | `false` |
| <nobr>--params</nobr>             | A JSON encoded string that'll be merged with the params from the configuration file (has to be in the same format) | n/a |
| <nobr>--params-file</nobr>        | The path to a file with the JSON encoded params that will be merged with the params from the configuration file  (has to be in the same format)| n/a |
| <nobr>--cache-enabled</nobr>      | Whether or not the cache functionality for cache with the type `cache.configurable` should be enabled | `true` |
| <nobr>--move-files-prefix</nobr>  | Prefix of the files that should be imported and moved to the temporary directory of the import | Defaults to the prefix of the first of the first plugin subject |

Beside the `configuration` option, all options can and **SHOULD** be defined in the configuration file. The commandline options should only be used to override these values in some circumstances.

!!!! If the `configuration` option has **NOT** been specified, the system tries to locate the Magento Edition, based on the specified `installation-dir` option. If the `installation-dir` option **IS** specified explictly, and the directory is a valid Magento root directory, the application tries to load database credentials from the `app/etc/env.php` script, so it is **NOT** necessary to specify a database configuration, nor in the configuration file or as commandline parameter.