---
title: 'Product Import'
visible: true
---

The structure for the Product import is 100 % compatible with the Magento 2 CSV structure. Have a look at the Magento 2 [documentation](http://docs.magento.com/m2/ce/user_guide/system/data-attributes-product.html) for a detailed description of the CSV file structure.

In addition to the Magento 2 CSV structure, it is possible to add additional columns to allow tier prices and MSI stock data also be part of the product import. For the tier prices, simply add the column `tier_prices`, for MSI stock data, add the column `inventory_source_items`. Both columns contains serialized data like the `additional_attributes` column does.

