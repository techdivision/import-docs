---
title: 'Attribute Set + Group Import'
visible: true
---

By default, the Attribute Set + Group Import expects a CSV file with the following defaults

* UTF-8 encoding
* Date format is n/d/y, g:i A
* Values delimiter is a comma (,)
* Multiple value delimiter is a pipe (|)
* Text values are enclosed with double apostrophes (")
* Special chars are escaped with a backslash (\)

> Columns that doesn't contain a value are ignored by default. This means, it is **NOT** possible to delete or override an existing value with an empty value. To delete an existing value, the whole attribute set/group has to be removed by running an import with the `delete` operation. After that, the attribute set/group with the new values can be imported by running an `add-update` operation. This default behaviour can be changed with the `clean-up-empty-columns` parameter in the particular subject.

The CSV file with the attributes for the Magento 2 CE/EE consists of the following columns, whereas each attribute set can have multipe attribute groups

| Column Name                    | Type     | Description                                                                           | Example |
|:-------------------------------|:---------|---------------------------------------------------------------------------------------|:--------|
| attribute_set_name             | varchar  | The unique attribute set name.                                                        | Bag |
| entity_type_code               | varchar  | The entity type code the attribute has to be bound to.                                | catalog_product |
| sort_order                     | integer  | The sort order of the attribute set.                                                  | 1 |
| attribute_group_name           | varchar  | The name of the attribute group.                                                      | Product Details |
| attribute_group_code           | varchar  | The unique code of the attribute group.                                               | product-details |
| attribute_group_tab_group_code | varchar  | The tag group code of the attribute group.                                            | basic |
| attribute_group_sort_order     | int      | The sort order for the attribute group in the backend/frontend.                       | 10       |
| default_id                     | int      | The default ID of the attribute group.                                                | 1 |
