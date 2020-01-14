---
title: Installation
taxonomy:
    category:
        - docs
visible: true
---

Depending on the requirements different installation options are available.

### Install as Composer Project

To install the package as a new project, assuming composer is available, open a console and enter

```sh
composer create-project techdivision/import-cli-simple --no-dev
```

This will clone the repository from the internal Gitlab and install the M2IF, that's all.

### Install as Composer Library

The second option, and in most Magento 2 projects the preferred way, will be the installation as a Composer library. For example, if you want to deliver it with your Magento 2 project, simply add

```json
{
  "require": {
    "techdivision/import-cli-simple" : "3.7.*"
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
wget https://github.com/techdivision/import-cli-simple/releases/download/3.7.9/import-cli-simple.phar \
  && sudo chmod +x import-cli-simple.phar
```

To install the PHAR in your actual Magento 2 installation, move it to `<MAGENTO-ROOT>/bin/import-cli-simple.phar` or, to install it globally, to `/usr/bin/import-cli-simple.phar`. Now you ready to use it.

### Add Missing Indexes

As the M2IF functionality differs from the Magento 2 standard, for performance reasons, it is necessary to manually add some necessary indexes. To do that, open a MySQL command line, connect to your MySQL server instance and enter the following SQL statement
 
```sql
ALTER TABLE `eav_attribute_option_value` ADD INDEX `EAV_ATTRIBUTE_OPTION_VALUE_VALUE` (`value` ASC); \
  ALTER TABLE `catalog_product_entity_int` ADD INDEX `CATALOG_PRODUCT_ENTITY_INT_VALUE` (`value` ASC); \
  ALTER TABLE `catalog_product_entity_varchar` ADD INDEX `CATALOG_PRODUCT_ENTITY_VARCHAR_VALUE` (`value` ASC); \
  ALTER TABLE `catalog_product_entity_decimal` ADD INDEX `CATALOG_PRODUCT_ENTITY_DECIMAL_VALUE` (`value` ASC); \
  ALTER TABLE `catalog_product_entity_datetime` ADD INDEX `CATALOG_PRODUCT_ENTITY_DATETIME_VALUE` (`value` ASC); \
  ALTER TABLE `url_rewrite` ADD INDEX `URL_REWRITE_ENTITY_ID` (`entity_id` ASC); \
  ALTER TABLE `url_rewrite` ADD INDEX `URL_REWRIRE_ENTITY_TYPE_ENTITY_ID` (`entity_id` ASC, `entity_type` ASC); \
  ALTER TABLE `catalog_product_entity_media_gallery` ADD INDEX `CATALOG_PRODUCT_ENTITY_MEDIA_GALLERY_VALUE` (`value`);
```

When using the Magic 360 [component](/components/3rd-party-components), addtionally the following indexes has to be added
