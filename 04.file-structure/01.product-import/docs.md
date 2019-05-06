---
title: 'Product Import'
visible: true
---

The structure for the Product import is 100 % compatible with the Magento 2 CSV structure. Have a look at the Magento 2 [documentation](http://docs.magento.com/m2/ce/user_guide/system/data-attributes-product.html) for a detailed description of the CSV file structure.

In addition to the Magento 2 CSV structure, it is possible to add additional columns to allow tier prices and MSI stock data also be part of the product import. Both columns contains serialized data like the `additional_attributes` column does.

During product import, M2IF extracts the data from the columns and creates separate CSV files. These CSV files are 1:1 the format described for [Tier Prices](/file-structure/product-import-tier-prices) and [MSI](/file-structure/product-import-msi) import in the corresponding sections. After the products have been imported, M2IF imports the tier prices as well as the MSI data.

### Add Tier Prices to Product Import

For the tier prices, simply add the column `tier_prices` to the CSV file with the product data. For example, the column **MUST** contain the data in the following structure, e. g.

```
qty=10,price=20,value_type=fixed,website=All Websites,customer_group=ALL GROUPS|qty=20,price=30,value_type=fixed,website=All Websites,customer_group=ALL GROUPS
```

The example above has two rows with tier prices, but there is no limiitation in how much rows of tier prices the column comtains. Each row with tier prices will be separated by `|` whereas each column of a row, containing the `attribute_code` to `value` pairs will be separated by the common `,`. The `attribute_code` to `value` pairs itself will use the `=` char for separation.

### Add MSI to Product Import

Add the column `inventory_source_items` to also import MSI stock data with the product import. For example, the column **MUST** contain the data in the following structure, e. g.

```csv
source_code=default,quantity=10.0,status=1|source_code=inventory-01,quantity=5.0,status=1
```

As for the tier prices, the column with the MSI inventory source items supports the same format.