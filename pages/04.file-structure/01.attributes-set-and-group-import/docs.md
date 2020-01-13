---
title: 'Attribute Set + Group Import'
visible: true
---

M2IF comes with an attribute import and the apropriate command `import:attributes:set` therefore. You can find more information about how to invoke the command in the [Usage](/getting-started/usage) section.

In general the filename for the dedicated MSI import **MUST** match the following pattern `<PREFIX>_<FILENAME>_<COUNTER>.csv`, whereas the default `<PREFIX>` is `attribute-set-import`, the `<FILENAME>` is a combination of date and time like `20190608-114344`, and the `<COUNTER>` is a consecutive number with two digits starting with `01`. This results in a filename like `attribute-set-import_20190608-114344_01.csv`. Additionally an apropriate `.ok` file is needed.

> ATTENTION: Please be aware that the three parts **MUST** be separated with an underscore "_" as there is meaning for the bunch import behind its structure.

### Unique Identifier

The unique identifier for the attribute set import is the attribute set name. The attribute set name is a mandatory field that has be available in the column `attribute_set_name` on **EVERY** row of the CSV file.

### Columns

The CSV file with the attribute sets for the Magento 2 CE/EE consists of the following columns, whereas each attribute set can have multipe attribute groups

| Column Name                    | Type     | Description                                                                           | Example |
|:-------------------------------|:---------|---------------------------------------------------------------------------------------|:--------|
| attribute_set_name             | varchar  | The unique attribute set name.                                                        | Bag |
| based_on                       | varchar  | The name of the attribute set to copy the attributes from.                            | Default |
| entity_type_code               | varchar  | The entity type code the attribute has to be bound to.                                | catalog_product |
| sort_order                     | integer  | The sort order of the attribute set.                                                  | 1 |
| attribute_group_name           | varchar  | The name of the attribute group.                                                      | Product Details |
| attribute_group_code           | varchar  | The unique code of the attribute group.                                               | product-details |
| attribute_group_tab_group_code | varchar  | The tag group code of the attribute group.                                            | basic |
| attribute_group_sort_order     | int      | The sort order for the attribute group in the backend/frontend.                       | 10       |
| default_id                     | int      | The default ID of the attribute group.                                                | 1 |
