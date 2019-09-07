---
title: 'Additional Attributes'
published: true
visible: true
---

The column `additional_attributes` allows to import values for all attributes that are not part of the default CSV format, including the user defined ones.

As the values of additional attributes can contain commas (,) which is the default column separator, it is necessary to enclose those values with a double apostrophe (") if only **ONE** of the attribute option value contains a comma, e. g. like the value `Laptop Sleeve, 15 inches`

| sku     | ... | additional_attributes                                                                                    | ... |
|:--------|:----|:---------------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | "color=black,activity=gym&#124;hiking,""features=Audio Pocket&#124;Laptop Sleeve, 15 inches"""           |     |

In case the values of an additional attributes contains a double apostrophe ("), e. g. like `Laptop "Sleeve"` those has to be escaped with a backslash or an double apostrophe (") itself like

| sku     | ... | additional_attributes                                                                                    | ... |
|:--------|:----|:---------------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | color=black,activity=gym&#124;hiking,features=Audio Pocket&#124;Laptop ""Sleeve""                        |     |

It'll also be possible that the values of additional attributes contains double apostrophes (") as well as commas (,). Those has to be enclosed and escaped like 

| sku     | ... | additional_attributes                                                                                    | ... |
|:--------|:----|:---------------------------------------------------------------------------------------------------------|:----|
| MB-2401 |     | "color=black,activity=gym&#124;hiking,""features=Audio Pocket&#124;Laptop ""Sleeve"", 15 inches"""       |     |

> Please keep in mind, that when a backslash is used to escape the delimiter, e. g. `Laptop \"Sleeve\"` the delimiter will **NOT** be removed automatically, instead it'll be stored in the database.