---
title: 'Product Import'
visible: true
---

The structure for the Product import is 100 % compatible with the Magento 2 CSV structure. Have a look at the Magento 2 [documentation](http://docs.magento.com/m2/ce/user_guide/system/data-attributes-product.html) for a detailed description of the CSV file structure.

In addition to the Magento 2 CSV structure, it is possible to add additional columns to allow tier prices and MSI stock data also be part of the product import. Both columns contains serialized data like the `additional_attributes` column does.

### Add Tier Prices to Product Import

For the tier prices, simply add the column `tier_prices` to the CSV file with the product data. For example, the column can contain the following data

```csv
qty=10,price=20,value_type=fixed,website=All Websites,customer_group=ALL GROUPS|qty=20,price=30,value_type=fixed,website=All Websites,customer_group=ALL GROUPS
```

Each tier price row with tier prices will be separated by `|` whereas each column, containing the attribute code => value pairs will be separated by the common `,`. The attribute code => value pairs itself will use the `=` char for separation.

### Add MSI to Product Import

Add the column `inventory_source_items` to also import MSI stock data with the product import.