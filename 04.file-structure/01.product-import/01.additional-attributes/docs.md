---
title: 'Additional Attributes'
published: true
visible: true
---

The column `additional_attributes` allows to import values for all attributes that are not part of the default CSV format, including the user defined ones.

> **ALWAYS** keep in mind, that in case of an attribute with type *Yes/No, Select, Multiple Select, Dropdown, Text Swatch, Visual Swatch* the value is **ALWAYS** the value of the admin store view, **NEVER** one of the scope values. In case of all other attribute types, the value **IS** the value that'll rendered for the select store view, either in backend or frontend.

### Enclosing, Escaping and Delimiter

As the default multiple field seprarator is a comma (,) the key value pairs of the additional attributes will be speparated by a comma (,). Therefore it is necessary to enclose the **COMPLETE** value of the column with double apostrophes ("). 

Additionally, if only **ONE** of the attribute option values also contains a comma, e. g. like the value `Laptop Sleeve, 15 inches` the **COMPLETE** key value pair additionally has to be enclosed with double apostrophes ("). This in case, leads to funny constellations, as those double apostrophes (") has to be escaped with double apostrophes (") again like

| sku     | ... | additional_attributes                                                           | ... |
|:--------|:----|:--------------------------------------------------------------------------------|:----|
| MB-2401 |     | "color=black,""features=Audio Pocket&#124;Laptop Sleeve, 15 inches"""           |     |

In case the values of an additional attributes contains a double apostrophe ("), e. g. like `Laptop "Sleeve"` those has to be escaped with a backslash or an double apostrophe (") itself like

| sku     | ... | additional_attributes                                                           | ... |
|:--------|:----|:--------------------------------------------------------------------------------|:----|
| MB-2401 |     | "color=black,features=Audio Pocket&#124;Laptop ""Sleeve"""                      |     |

It'll also be possible that the values of additional attributes contains double apostrophes (") as well as commas (,). Those has to be enclosed and escaped like 

| sku     | ... | additional_attributes                                                           | ... |
|:--------|:----|:--------------------------------------------------------------------------------|:----|
| MB-2401 |     | "color=black,""features=Audio Pocket&#124;Laptop ""Sleeve"", 15 inches"""       |     |

> Please keep in mind, that when a escape character, which defaults to backslash (\\), is used to escape the delimiter, e. g. `Laptop \"Sleeve\"` the delimiter will **NOT** be removed automatically, instead it'll be stored in the database.

### Multiselect Attributes

In case of Multiselect Attributes, mostly more than one value for an attribute will be specified. Those values has to b separated by character, which by default is the pipe (|). For example if the Multiselect Attribute `activity` should have the values `gym` and `hiking, trails` selected the column `additional_attributes` **MUST** look like  

| sku     | ... | additional_attributes                                                                                | ... |
|:--------|:----|:-----------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | """activity=gym&#124;hiking, trails"",""features=Audio Pocket&#124;Laptop Sleeve, 15 inches"""       |     |

> The Multiselect Attributes ore the only values that uses the multiple value delimiter!

### Configuration

Beside the general CSV configuration on subject level M2IF allows to override the default values, which are a comma (,) for the multiple field and a pipe (|) for the muliple value separator. In contrast to the general CSV configuration this has to be done on the global level and is therefore valid for all operations, subjects and observers.

The default configuration can be overwritten by adding either one of or both `multiple-field-delimiter` and `mulitple-value-delimiter` to the confifguration file like

```json
{
  "magento-edition": "CE",
  "magento-version": "2.3.2",
  "operation-name" : "add-update",
  "installation-dir" : "/var/www/magento",
  "multiple-field-delimiter" : ",",
  "multiple-value-delimiter" : "|",
  "databases" : [ ... ],
  "loggers" : [ ... ],
  "operations" : { ... }
}
```