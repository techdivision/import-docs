---
title: Quickstart
visible: true
---

To install the Magnento 2 Import Framework, composer is necessary. The framework itself is a set of components that provide import functionality for Magento 2. This repository, based on Symfony Console, uses the the package [M2IF](https://github.com/techdivision/import) and provides a command line tool with import functionality for Magento 2 standard CSV files.

### Install as Composer Project

To install the package as a new project, assuming composer is available, open a console and enter

```sh
composer create-project techdivision/import-cli-simple --no-dev --stability=alpha
```

This will clone the repository from the internal Gitlab and install the M2IF, that's all.

### Install as Composer Library

The second option, and in most Magento 2 projects the preferred way, will be the installation as a Composer library. For example, if you want to deliver it with your Magento 2 project, simply add

```json
{
  "require": {
    "techdivision/import-cli-simple" : "1.0.0-beta3"
  }
}
```

to your Magento 2 `composer.json` file. Then run

```sh
composer update
```

from your Magento 2 root directory and your're all setup.

### Use as PHAR

The last, but for sure not the worst installation option, is to download the latest PHAR from our [Github](https://github.com/techdivision/import-cli-simple/releases) release page and make it executable, e. g. with `wget`

```sh
wget https://github.com/techdivision/import-cli-simple/releases/download/1.0.0/import-cli-simple.phar && sudo chmod +x import-cli-simple.phar
```

To install the PHAR in your actual Magento 2 installation, move it to `<MAGENTO-ROOT>/bin/import-cli-simple.phar` or, to install it globally, to `/usr/bin/import-cli-simple.phar`. Now you ready to use it.

### Add Missing Indexes

As the M2IF functionality differs from the Magento 2 standard, for performance reasons, it is necessary to manually add a missing index.

To do that, open a MySQL command line and enter the following SQL statement
 
```sql
ALTER TABLE `eav_attribute_option_value` ADD INDEX `EAV_ATTRIBUTE_OPTION_VALUE_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_int` ADD INDEX `CATALOG_PRODUCT_ENTITY_INT_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_varchar` ADD INDEX `CATALOG_PRODUCT_ENTITY_VARCHAR_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_decimal` ADD INDEX `CATALOG_PRODUCT_ENTITY_DECIMAL_VALUE` (`value` ASC);
ALTER TABLE `catalog_product_entity_datetime` ADD INDEX `CATALOG_PRODUCT_ENTITY_DATETIME_VALUE` (`value` ASC);
ALTER TABLE `url_rewrite` ADD INDEX `URL_REWRITE_ENTITY_ID` (`entity_id` ASC);
ALTER TABLE `url_rewrite` ADD INDEX `URL_REWRIRE_ENTITY_TYPE_ENTITY_ID` (`entity_id` ASC, `entity_type` ASC);
```

> This also improves performance of the Magento 2 standard import functionality, but not at
> same level as for M2IF.

### Running the import

After installation, the importer is ready-to-run. When you're in the root of your Magento 2 installation, you do **NOT** need to specify the configuration for the Workflow Engine, because The importer parses the installation's configuration under `app/etc/env.php` and loads the Magento Edition/Version as well as the database configuration. By default, the importer searches for CSV files in the directory `var/importexport`. The files **MUST** have the prefix `product-import`, followed by a date/timestamp like `20180403-190920`, a consecutive number like `01` and the file suffix `.csv`.

Assuming, your CSV file `var/importexport/product-import_20180403-190920_01.csv` is ready to be imported and you're using the PHAR, you can start the importer with `bin/import-cli-simple.phar import:products`.

This invoke's the `add-update` operation with the file from above on your Magento 2 installation. 

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