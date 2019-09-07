---
title: 'Complex Data'
published: false
visible: true
---

The Magento standard CSV format provides a feature called comoplex data which allows some kind of simple serialization e. g. to have list of additional attributes within one column.

On the Magento [developer documentation](https://docs.magento.com/m2/ce/user_guide/system/data-complex.html), a short description about the complex data format for configurables, bundles and grouped product inforation is available. 

### Additional Attributes

Beside these columens there is an additional column `additional_attributes` that allows to import values for all attributes that are not part of the default CSV format, including the user defined ones.

As the values of additional attributes can contain commas (,) which is the default column separator, it is necessary to enclose the complete values with a double apostrophe (") if only **ONE** of the attribute option value contains a comma, e. g. like the value `Laptop Sleeve, 15 inches`

| sku     | ... | additional_attributes                                                                                    | ... |
|:--------|:----|:---------------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | "color=black,activity=gym|hiking,features=""Audio Pocket|Laptop Sleeve, 15 inches"""                     |     |

In case the values of an additional attributes contains a double apostrophe (") this has to be escaped with a backslash or an double apostrophe (") itself like

| sku     | ... | additional_attributes                                                                                    | ... |
|:--------|:----|:---------------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | color=black,activity=gym|hiking,features=Audio Pocket|Laptop \"Sleeve\"                                  |     |

It'll also be possible that the values of additional attributes contains double apostrophes (") as well as commas (,). Those has to be enclosed and escaped like 

| sku     | ... | additional_attributes                                                                                    | ... |
|:--------|:----|:---------------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | "color=black,activity=gym|hiking,features=""Audio Pocket|Laptop \"Sleeve\", 15 inches"""                 |     |
