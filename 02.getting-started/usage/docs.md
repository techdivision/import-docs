---
title: Usage
visible: true
---

After installation, the importer is ready-to-run. When you're in the root of your Magento 2 installation, you do **NOT** need to specify the configuration for the Workflow Engine, because The importer parses the installation's configuration under `app/etc/env.php` and loads the Magento Edition/Version as well as the database configuration. By default, the importer searches for CSV files in the directory `var/importexport`. The files **MUST** have the prefix `product-import`, followed by a date/timestamp like `20180403-190920`, a consecutive number like `01` and the file suffix `.csv`.

Assuming, your CSV file `var/importexport/product-import_20180403-190920_01.csv` is ready to be imported and you're using the PHAR, you can start the importer with 

```
bin/import-cli-simple.phar import:create:ok-file && bin/import-cli-simple.phar import:products
```

whereas the first command creates the mandatory `.OK` file that signals, that all import artefacts are on-place and the second command finally starts the import with the [add-update](#operations) operation on your Magento 2 installation. 

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