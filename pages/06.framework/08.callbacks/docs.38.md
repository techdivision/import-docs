---
title: Callbacks
taxonomy:
    category:
        - docs
visible: true
---

#### When do i need a callback?

Callbacks can be used to transform values, found in the CSV file into the necessary types that needs to be stored into the database. For example, the default Magento 2 CSV format allows the values

* `Catalog`
* `Search`
* `Catalog, Search`
* `Not Visible Individually`

for the column `visibility`. These values can not be stored in the appropriate database column, as this expects integer values. Therefore, a callback can be use to transform the string into the correct integer value, in this case the class `TechDivision\\Import\\Product\\Callbacks\\VisibilityCallback`.

By default, the necessary callbacks to transform the Magento 2 standard attributes found in the CSV file are already defined. When a new, user defined attribute will be added, e. g. with a setup script, the M2IF tries to find the best matching callback, depending on the `frontend_input` value of the attribute. Actually M2IF comes whith callbacks for

* `select`
* `multiselect`
* `boolean`

`frontend_input` types. Callbacks for other input types will be part of upcoming versions, but can always be implemented by the developers using M2IF in their project. To register a custom callback, it has to be added to the array with the callbacks of a subject, like

```json
"callbacks": [
  {
    "visibility": [
      "import_product.callback.visibility"
    ]
  }
]
```

!!!! In general, you need a callback, if you want to do something with the **VALUE** on as specific column in each row of a CSV file. Please be aware, that a custom callback will **REPLACE** the default callback and will **NOT** be appended!

#### How to implement a callback?

TBD