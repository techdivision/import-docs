---
title: 'Product URL Rewrites'
taxonomy:
    category:
        - docs
visible: true
---

M2IF comes with a dedicated product URL rewrite import and the apropriate command `import:products:url` therefore. You can find more information about how to invoke the command in the [Usage](/getting-started/usage) section.

In general the filename for the dedicated product URL rewrite import **MUST** match the following pattern `<PREFIX>_<FILENAME>_<COUNTER>.csv`, whereas the default `<PREFIX>` is `url-rewrite`, the `<FILENAME>` is a combination of date and time like `20190608-114344`, and the `<COUNTER>` is a consecutive number with two digits starting with `01`. This results in a filename like `url-rewrite_20190608-114344_01.csv`. Additionally an apropriate `.ok` file is needed.

> ATTENTION: Please be aware that the three parts **MUST** be separated with an underscore "_" as there is meaning for the bunch import behind its structure.

The CSV file with the attributes for the Magento 2 CE/EE consists of the following columns

| Column Name      | Type     | Description                                                                               | Example      |
|:-----------------|:---------|-------------------------------------------------------------------------------------------|:-------------|
| sku              | varchar  | The product SKU to import the tier price for                                              |      24-MB01 |
| store_view_code  | varchar  | The website code to import the tier price for, or `All Websites` for all websites         | All Websites |
| categories       | varchar  | The customer group the import the tier price for, or `ALL GROUPS` for all customer groups |   ALL GROUPS |
| product_websites | varchar  | The quantity the tier price is valid from                                                 |         20.0 |
| visibility       | varchar  | The tier price itself                                                                     |         98.0 |
| url_key          | varchar  | The value type which can either be one of `Fixed` or `Discount`                           |        Fixed |

By default, the product URL rewrites will be imported with the full product import, read the apropriate documentation [section](https://docs.m2if.com/file-structure/product-import) therefore.