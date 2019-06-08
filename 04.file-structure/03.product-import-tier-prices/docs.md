---
title: 'Product Import // Tier Prices'
visible: true
---

M2IF comes with a dedicated tier price import and the apropriate command `import:products:tier:price` therefore. You can find more information about how to invoke the command in the [Usage](/getting-started/usage) section.

In general the filename for the dedicated tier price import **MUST** match the following pattern `<PREFIX>_<FILENAME>_<COUNTER>.csv`, whereas the default `<PREFIX>` is `product-import-tier-price`, the `<FILENAME>` is a combination of date and time like `20190608-114344` in most cases and the `<COUNTER>` is a number with two digits like `01`. This results in a filename like `product-import-tier-price_20190608-114344_01.csv`. Additionally an apropriate `.ok` file is needed.

> ATTENTION: Please be aware that the three parts **MUST** be separated with an underscore "_" as there is meaning for the bunch import behind its structure.

The CSV file with the attributes for the Magento 2 CE/EE consists of the following columns

| Column Name               | Type     | Description                                                                               | Example      |
|:--------------------------|:---------|-------------------------------------------------------------------------------------------|:-------------|
| sku                       | varchar  | The product SKU to import the tier price for                                              |      24-MB01 |
| tier_price_website        | varchar  | The website code to import the tier price for, or `All Websites` for all websites         | All Websites |
| tier_price_customer_group | integer  | The customer group the import the tier price for, or `ALL GROUPS` for all customer groups |   ALL GROUPS |
| tier_price_qty            | decimal  | The quantity the tier price is valid from                                                 |         20.0 |
| tier_price                | decimal  | The tier price itself                                                                     |         98.0 |
| tier_price_value_type     | decimal  | The value type which can either be one of `Fixed` or `Discount`                           |        Fixed |
