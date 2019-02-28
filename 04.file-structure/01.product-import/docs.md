---
title: 'Product Import'
visible: true
---

By default, the product import expects a CSV file with the following defaults

* UTF-8 encoding
* Date format is n/d/y, g:i A
* Values delimiter is a comma (,)
* Multiple value delimiter is a pipe (|)
* Text values are enclosed with double apostrophes (")
* Special chars are escaped with a backslash (\)

> Columns that doesn't contain a value are ignored by default. This means, it is **NOT** possible to delete or override an existing value with an empty value. To delete an existing value, the whole product has to be removed by running an import with the `delete` operation. After that, the product with the new values can be imported by running an `add-update` operation. This default behaviour can be changed with the `clean-up-empty-columns` parameter in the particular subject.

The structure for the Product import is 100 % compatible with the Magento 2 CSV structure. Have a look at the Magento 2 [documentation](http://docs.magento.com/m2/ce/user_guide/system/data-attributes-product.html) for a detailed description of the CSV file structure.