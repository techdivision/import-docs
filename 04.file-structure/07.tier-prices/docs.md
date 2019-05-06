---
title: 'Tier Prices'
visible: true
---

The CSV file with the attributes for the Magento 2 CE/EE consists of the following columns

| Column Name               | Type     | Description                                                                               | Example      |
|:--------------------------|:---------|-------------------------------------------------------------------------------------------|:-------------|
| sku                       | varchar  | The product SKU to import the tier price for                                              |      24-MB01 |
| tier_price_website        | varchar  | The website code to import the tier price for, or `All Websites` for all websites         | All Websites |
| tier_price_customer_group | integer  | The customer group the import the tier price for, or `ALL GROUPS` for all customer groups |   ALL GROUPS |
| tier_price_qty            | decimal  | The quantity the tier price is valid from                                                 |         20.0 |
| tier_price                | decimal  | The tier price itself                                                                     |         98.0 |
| tier_price_value_type     | decimal  | The value type which can either be one of `Fixed` or `Discount`                           |        Fixed |
