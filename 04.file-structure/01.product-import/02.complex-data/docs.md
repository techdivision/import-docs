---
title: 'Complex Data'
published: false
visible: true
---

The Magento standard CSV format provides a feature called comoplex data which allows some kind of simple serialization e. g. to have list of additional attributes within one column.

On the Magento [developer documentation](https://docs.magento.com/m2/ce/user_guide/system/data-complex.html), a short description about the complex data format for configurables, bundles and grouped product inforation is available. 

### Additional Attributes

Beside these columens there is an additional column `additional_attributes` that allows to import values for all attributes that are not part of the default CSV format, including the user defined ones.

As the values of an additional attribute can contain also contain a comma (,) which is the default column separator, it is necessary to enclose the value of this column with a ", if only **ONE** attribute value contains a comma, e. g.

| sku     | ... | additional_attributes                                                                                    | ... |
|:--------|:----|:---------------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | "activity=""gym|hiking"",features=""Audio Pocket|Laptop Sleeve, 15 inches"""                             |     |


