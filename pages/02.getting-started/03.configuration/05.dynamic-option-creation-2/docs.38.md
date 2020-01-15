---
title: 'Dynamic Category Creation'
taxonomy:
    category:
        - docs
visible: true
---

Up from version 3.8.0 missing categories in the product CSV file will be created by default, but only for the `admin` store. To disable this, override the default `add-update` shortcut (defined in the configuration snipped of the [techdivision/import-product](https://github.com/techdivision/import-product/blob/19.x/etc/configuration/shortcuts.json) library), by adding a snippet, e. g. <custom-configuration-dir>/shortcuts.json` and remove the `eav_attribute` related operations

```json
{
  "shortcuts": {
    "ce": {
      "catalog_product": {
        "add-update": [
          ...
          "general/catalog_category/convert",
          "ce/catalog_category/sort",
          "ce/catalog_category/add-update",
          "ce/catalog_category/add-update.path",
          "ce/catalog_category/add-update.url-rewrite",
          "general/catalog_category/children-count",
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
          "general/eav_attribute/convert",
          "general/eav_attribute/add-update.options",
          "general/eav_attribute/add-update.option-values",
          "general/eav_attribute/add-update.swatch-values",
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

! Please be aware, that it is **NOT** possible to create categories for the other stores other than the `admin` store itself. This is, because when creating categories dynamically, the names taken from the column `categories` always has to be the values for the `admin` store. Translations can be done in the Magento backend or with the [category import](/file-structure/categories).