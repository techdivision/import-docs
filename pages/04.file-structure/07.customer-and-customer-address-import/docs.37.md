---
title: 'Customer + Customer Address Import'
visible: true
---

M2IF comes with a customer and customer address import as well as the apropriate commands `import:customers` and `import:customers:address` therefore. You can find more information about how to invoke the command in the [Usage](/getting-started/usage) section.

In general the filename for the dedicated MSI import **MUST** match the following pattern `<PREFIX>_<FILENAME>_<COUNTER>.csv`, whereas the default `<PREFIX>` for the customer import is `customer-import` and for the customer address import `customer-address-import`. The `<FILENAME>` is a combination of date and time like `20190608-114344`, and the `<COUNTER>` is a consecutive number with two digits starting with `01`. This results in a filename like `customer-import_20190608-114344_01.csv` for customers and `customer-address-import_20190608-114344_01.csv` for customer addresses. Additionally an apropriate `.ok` file is needed.

> ATTENTION: Please be aware that the three parts **MUST** be separated with an underscore "_" as there is meaning for the bunch import behind its structure.

The structure for the Customer + Customer Address Import is 100 % compatible with the Magento 2 CSV structure. Have a look at the Magento 2 [documentation](https://docs.magento.com/m2/ce/user_guide/system/data-attributes-customer.html) for a detailed description of the CSV file structure.