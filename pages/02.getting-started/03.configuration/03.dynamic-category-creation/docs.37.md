---
title: 'Dynamic Category Creation'
taxonomy:
    category:
        - docs
visible: true
---

Up from version 3.8.0 missing categories in the product CSV file will be created by default, but only for the `admin` store. To disable this, override the default `add-update` shortcut (defined in the configuration snipped of the [techdivision/import-product](https://github.com/techdivision/import-product/blob/19.x/etc/configuration/shortcuts.json) library), by adding a snippet, e. g. `<custom-configuration-dir>/shortcuts.json` and remove the `catalog_category` related operations