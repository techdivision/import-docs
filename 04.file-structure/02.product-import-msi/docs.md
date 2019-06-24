---
title: 'Product Import // MSI'
visible: true
---

M2IF comes with a dedicated MSI import and the apropriate command `import:products:inventory:msi` therefore. You can find more information about how to invoke the command in the [Usage](/getting-started/usage) section.

In general the filename for the dedicated MSI import **MUST** match the following pattern `<PREFIX>_<FILENAME>_<COUNTER>.csv`, whereas the default `<PREFIX>` is `product-import-inventory-msi`, the `<FILENAME>` is a combination of date and time like `20190608-114344`, and the `<COUNTER>` is a consecutive number with two digits starting with `01`. This results in a filename like `product-import-inventory-msi_20190608-114344_01.csv`. Additionally an apropriate `.ok` file is needed.

> ATTENTION: Please be aware that the three parts **MUST** be separated with an underscore "_" as there is meaning for the bunch import behind its structure.

The CSV file with the attributes for the Magento 2 CE/EE consists of the following columns

| Column Name | Type     | Description                                                                           | Example |
|:------------|:---------|---------------------------------------------------------------------------------------|:--------|
| sku         | varchar  | The product SKU to import the inventory source item for                               | 24-MB01 |
| source_code | varchar  | The code for the products source.                                                     | default |
| status      | integer  | The stock status of the product for this source.                                      |       1 |
| quantity    | decimal  | The quantity of the product for this source.                                          |    98.0 |

If MSI should be imported with the full product import, read the apropriate documentation [section](https://docs.m2if.com/file-structure/product-import#add-msi-to-product-import) how this can be realized.
