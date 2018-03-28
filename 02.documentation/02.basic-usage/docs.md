---
title: 'Basic Usage'
visible: true
---

### Operations

As well as the Magento 2 standard import functionality, M2IF will provide 3 different import operations:

| Operation                 | Description
|:--------------------------|:-----------------------------------------------------------------------------------|
| add-update                | New product data is added to the existing product data for the existing entries in the database. All fields except sku can be updated. New tax classes that are specified in the import data are created automatically. New SKUs that are specified in the import file are created automatically. |
| replace                   | The existing product data is replaced with new data. If a SKU in the import data matches the SKU of an existing entity, all fields, including the SKU are deleted, and a new record is created using the CSV data. An error occurs if the CSV file references a SKU that does not exist in the database. |
| delete                    | Any entities in the import data that already exist in the database are deleted from the database. Delete ignores all columns in the import data, except for SKU. You can disregard all other attributes in the data. An error occurs if the CSV file references a SKU that does not exist in the database. |

> Exercise caution when replacing data because the existing product data will be completely cleared and all references in the system will be lost.

### Preparation

To run the example import, it is necessary to get a copy of the Magento 2 sample data, that can be cloned from Github, assumed you're in the root folder of this repository, by invoking

```sh
$ git clone https://github.com/magento/magento2-sample-data.git projects/sample-data/magento2-sample-data
```

on the command line.

### Bunches

The import is able to handle bunches. In general this is a functionality that will only make sense in a multithreaded or multiprocessed environment where the bunches can be imported in parallel. In this case, it should only give the developer an idea, on how a multiprocessed functionality can be implemented.

A bunch is a CSV file which is only a part of a complete import. It doesn't matter, what a kind of data a bunch contains, as the importer handles the data in the necessary order. This means, that the first step is to import all simple products found in a bunch. After that, information like the created entity IDs related with the imported SKUs, which is necessary to import all other product data (Bunches, Configurables, Images, Related etc.) will be shared, so it'll be possible to import these data step-by-step, but each step also in parallel.

To split a import into multiple bunches, the bunched files **MUST** follow these pattern:

1. The prefix has to equal, e. g. `magento-product-import`
2. The prefix has to be followed by an underscore `_`
3. A random number of alphanumeric charaters has to follow
4. These characters has also to be followed by an underscore `_`
5. Finally, each bunch **MUST** have a sequential number, followed by `.csv`

For example, the following files will be imported as a bunch:

* `tmp/magento-import_20170203-1234_01.csv`
* `tmp/magento-import_20170203-1234_02.csv`
* `tmp/magento-import_20170203-1234_03.csv`
* `tmp/magento-import_20170203-1234_04.csv`

When starting the import process by invoking the apropriate command, these files will be imported like one file. It is **NOT** necessary to invoke the importer four times.

### Running the Import

The command doesn't implement any directory clean-up or archiving functionality, what means that the files have to be copied to the source directory specified for the subjects. Assuming a Magento 2 CE 2.1.2 instance, with sample data installed, is available under `/var/www/magento` the configuration file, as well as the CSV files, can be found under `projects/sample-data/ce/212`.

The command to re-import the sample data including the images, would look like this:

```sh
$ sudo rm -rf projects/sample-data/tmp \ 
    && sudo mkdir projects/sample-data/tmp \
    && sudo cp projects/sample-data/ce/212/data/replace/*.csv projects/sample-data/tmp \
    && sudo bin/import-simple import:products \
       --installation-dir=/var/www/magento \
       --configuration=projects/sample-data/ce/212/conf/techdivision-import.json
```

To make sure, that all old import files will be removed, we'll delete and re-create the directory that contains the import files `projects/sample-data/tmp`, before.

The import process only starts, when an OK flagfile is available in the same directory where the CSV files are located. The naming convention for the OK flagfile **MUST** follow one of these naming conventions

* `<IMPORT-DIRECTORY>/<PREFIX>.ok`
* `<IMPORT-DIRECTORY>/<PREFIX>_<FILENAME>.ok`
* `<IMPORT-DIRECTORY>/<PREFIX>_<FILENAME>_<COUNTER>.ok`

which results in one of

* `projects/sample-data/tmp/magento-import.ok`
* `projects/sample-data/tmp/magento-import_20170203.ok`
* `projects/sample-data/tmp/magento-import_20170203_01.ok`

The flagfile **MUST** contain the name of the CSV files that have to be imported within the next iterations. If the flagfile would be named `projects/sample-data/tmp/magento-import_20170203-1234.ok` for example and contains the following lines

```sh
magento-import_20170203-1234_01.csv
magento-import_20170203-1234_02.csv
magento-import_20170203-1234_03.csv
magento-import_20170203-1234_04.csv
```

the importer has to be invoked four times (because the example above is **NO** bunch), whereas on each invovation, the next file will be imported and removed from the flagfile.

Have a look in subdirectories of `project/sample-data/*` for a working example.

### Debug Mode

The debug mode provides a more detailed logging output, by automatically setting the Monolog log level to `LogLevel::DEBUG` if **NOT** overwritten with the commandline option `--log-level`. Additionally it ignores

* product category relations to categories that not exists 
* product links (related, upsell, crosssell, etc.) for SKUs which are **NOT** available
* configurable products for SKUs which are **NOT** available or available more than one time

but logs these issues as warnings to the console.

When the debug mode has been enabled, missing attribute option values will **NOT** throw an exception, instead they will logged and put on an internal stack. If the [MissingOptionValuesPlugin](https://github.com/techdivision/import#missing-option-values) has been enabled, a CSV file with the missing option values will be created in the temporary import folder. If a Swift Mailer has been enabled by the plugin configuration, the CSV file will be sent to the given mail addresses.

This will help developers to test imports with partially invalid CSV files which do **NOT** break data consistency.

### Running Parallel Imports

To avoid unwanted behaviour, only one import process can be started at a time. To make sure, that only one process is running, a PID file in the system's temporary directory (`sys_get_temp_dir()`) is created which contains the UUID of the actual import process. After the import process has been finished, the file will be deleted and a new process can be started.