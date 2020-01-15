---
title: 'Dynamic Category Creation'
taxonomy:
    category:
        - docs
visible: true
---

Up from versionn 2.2.x + 2.3.x it is possible to create missing product option values for the `admin` store on the fly. This means that it is **NOT** possible to create translations for the other stores other than the `admin` store itself. This is, because when creating option values dynamically, the option values taken from the column `additional_attributes` always has to be the values for the `admin` store. Translations can be done in the Magento backend or with the [attribute import](/file-structure/attributes). To enable this, additional frontend input callbacks have to be registered in the configuration file, for the `add-update` as well as the `replace` operations. The subject configuration should look like this

```json
"subjects" : [
  {
    "id" : "import_product.subject.bunch",
    "identifier" : "files",
    "file-resolver": {
      "prefix": "product-import"
    },
    "observers" : [ ... ],
    "callbacks" : [ ... ],
    "params" : [ ... ],
    "frontend-input-callbacks": [
      {
        "select": [
          "import_attribute.callback.create.select.option.value",
          "import_product.callback.select"
        ],
        "multiselect": [
          "import_attribute.callback.create.multiselect.option.value",
          "import_product.callback.multiselect"
        ]
      }
    ]
  }
]
```