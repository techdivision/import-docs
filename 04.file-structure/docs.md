---
title: 'File Structure'
visible: true
---

Generally, M2IF supports any given file format to be imported. The main format, and actually the only provided one, is the CSV format, which in case for the product import, is 100 % compatible with the Magento 2 standard import. The file format for the available import entities product, category, customer, attribute and attribute set/group is described in the following chapters. Additionally, example files are available in our [GitHub](https://github.com/techdivision/import-cli-simple/tree/3.1.x/projects/sample-data) repository.

Additional formats can be implemented by either adding new adapters, e. g. for XML or a plug-in to transform XML to CSV. In case of writing an adapter, the default CSV adapter can simply be replaced by overriding the DI configuration. If you want to write a plug-in, that converts one or more XML files into the apropriate CSV format, the plug-in has to be integrated by adding it the configuration of the Workflow Engine.

### Magento Compatibility

Magento 2 itself provides import functionality for the Product, Inventory + Prices as well as the Customer and Cutomer Address entity. M2IF aims to provide nearly 100 % compatibility, whereas replacing the Magento 2 standard functionality should be no problem at all. Beside the entities above, M2IF also supports importing

* Categories
* Attributes
* Attribute-Sets (and their Groups)

> The default import format for all entties is CSV actually, which will always be supported in future versions as well. As M2IF is **NOT** a tool but a framework, replacing the CSV format, e. g. with XML will be possible for sure.

### General CSV File Format

By default, the Customer + Customer Address Import expects a CSV file with the following defaults

* UTF-8 encoding
* Date format is n/d/y, g:i A
* Values delimiter is a comma (,)
* Multiple value delimiter is a pipe (|)
* Text values are enclosed with double apostrophes (")
* Special chars are escaped with a backslash (\)

> By default, columns that doesn't contain a value are ignored by default. This means, it is **NOT** possible to delete or override an existing value with an empty value. To delete an existing value, the whole customer has to be removed by running an import with the `delete` operation. After that, the customer with the new values can be imported by running an `add-update` operation. This default behaviour can be changed with the `clean-up-empty-columns` parameter in the particular subject.