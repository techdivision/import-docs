---
title: 'Default Column Values'
---

Sometimes it can be helpful if default values can be stored for columns not contained in the CSV file, e.g. if there are only simple products and the column `product_type` would be superfluous. From version 3.8.0 on, a snippet can be used to define default values for columns not contained in the CSV file. The snippet must have the following format

```json
{
  "default-values": {
    "catalog_product": {
      "my-column": "my-column-value"
    }
  }
}
```
! Be aware, that the default value will **ONLY** be used if the CSV file does **NOT** contain the column. If the column is available, then always the column value will be used, even if the column is empty.