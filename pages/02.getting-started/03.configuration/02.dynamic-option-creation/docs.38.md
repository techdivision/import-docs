---
title: 'Dynamic Option Creation'
taxonomy:
    category:
        - docs
visible: true
---

Up from version 2.2.x + 2.3.x it is possible to create missing product option values for the `admin` store on the fly. This means that it is **NOT** possible to create translations for the other stores other than the `admin` store itself. This is, because when creating option values dynamically, the option values taken from the column `additional_attributes` always has to be the values for the `admin` store. Translations can be done in the Magento backend or with the [attribute import](/file-structure/attributes).

To enable this, override the default `add-update` operation by adding a snippet, e. g. <custom-configuration-dir>/operations.json` and add the additional frontend input callbacks, that have to be registered, for the operation. The operation configuration should look like this

```json
{
  "operations": {
    "ce": {
      "catalog_product": {
        "add-update": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "subjects": [
                {
                  "id": "import_product.subject.bunch",
                  "file-resolver": {
                    "prefix": "product-import"
                  },
                  "params": {
                    "copy-images": false,
                    "clean-up-media-gallery": true,
                    "clean-up-empty-image-columns": true,
                    "clean-up-website-product-relations": true,
                    "clean-up-category-product-relations": true,
                    "clean-up-empty-columns": [
                      "special_price",
                      "special_price_from_date",
                      "special_price_to_date"
                    ]
                  },
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
                  ],
                  "observers": [
                    {
                      "import": [
                        "import_product.observer.composite.base.add_update"
                      ]
                    }
                  ]
                }
              ]
            }
          }
        }
      }
    }
  }
}
```