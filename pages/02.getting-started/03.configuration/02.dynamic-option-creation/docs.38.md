---
title: 'Dynamic Option Creation'
taxonomy:
    category:
        - docs
visible: true
---

Up from version 3.8.0 missing product option values for the `admin` store will be created by default. To disable this, override the [default](https://github.com/techdivision/import-product/blob/19.x/etc/configuration/shortcuts.json) `add-update` shortcut, by adding a snippet, e. g. <custom-configuration-dir>/shortcuts.json` and remove the `eav_attribute` related operations

```json
{
  "shortcuts": {
    "ce": {
      "catalog_product": {
        "add-update": [
          ...
          "general/eav_attribute/convert",
          "general/eav_attribute/add-update.options",
          "general/eav_attribute/add-update.option-values",
          "general/eav_attribute/add-update.swatch-values",
          ...
        ]
      }
    }
  }
}
```
    
This should result a file that looks like

```json
{
  "shortcuts": {
    "ce": {
      "catalog_product": {
        "add-update": [
          "general/general/global-data",
          "general/general/move-files",
          "general/catalog_product/collect-data",
          "general/catalog_category/convert",
          "ce/catalog_category/sort",
          "ce/catalog_category/add-update",
          "ce/catalog_category/add-update.path",
          "ce/catalog_category/add-update.url-rewrite",
          "general/catalog_category/children-count",
          "general/catalog_product/validate",
          "ce/catalog_product/add-update",
          "ce/catalog_product/add-update.variants",
          "ce/catalog_product/add-update.bundles",
          "ce/catalog_product/add-update.links",
          "ce/catalog_product/add-update.grouped",
          "ce/catalog_product/add-update.media",
          "general/catalog_product/add-update.msi",
          "general/catalog_product/add-update.url-rewrites"
        ]
      }
    }
  }
}
```

! Please be aware, that it is **NOT** possible to create translations for the other stores other than the `admin` store itself. This is, because when creating option values dynamically, the option values taken from the column `additional_attributes` always has to be the values for the `admin` store. Translations can be done in the Magento backend or with the [attribute import](/file-structure/attributes).