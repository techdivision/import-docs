---
title: 'Product Import'
visible: true
---

M2IF comes with a product import and the apropriate command `import:products` therefore. You can find more information about how to invoke the command in the [Usage](/getting-started/usage) section.

In general the filename for the dedicated MSI import **MUST** match the following pattern `<PREFIX>_<FILENAME>_<COUNTER>.csv`, whereas the default `<PREFIX>` is `product-import`, the `<FILENAME>` is a combination of date and time like `20190608-114344`, and the `<COUNTER>` is a consecutive number with two digits starting with `01`. This results in a filename like `product-import_20190608-114344_01.csv`. Additionally an apropriate `.ok` file is needed.

> ATTENTION: Please be aware that the three parts **MUST** be separated with an underscore "_" as there is meaning for the bunch import behind its structure.

The structure for the Product import is 100 % compatible with the Magento 2 CSV structure. Have a look at the Magento 2 [documentation](http://docs.magento.com/m2/ce/user_guide/system/data-attributes-product.html) for a detailed description of the CSV file structure.

In addition to the Magento 2 CSV structure, it is possible to add additional columns to allow tier prices and MSI stock data also be part of the product import. Both columns contains serialized data like the `additional_attributes` column does.

During product import, M2IF extracts the data from the columns and creates separate CSV files. These CSV files are 1:1 the format described for [Tier Prices](/file-structure/product-import-tier-prices) and [MSI](/file-structure/product-import-msi) import in the corresponding sections. After the products have been imported, M2IF imports the tier prices as well as the MSI data.