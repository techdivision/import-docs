---
title: Clean-Up
visible: true
---

M2IF provides a clean-up functionality that provides the possiblity to remove values for empty columns. This is very helpful when using the `add-update` operation, because in general data will only be added or updated if already available.

> As deleting data can reduce performance significantly, is should be used carefully!

##### Product Import

Clean-Up functionality for the product import is available for

* Media Gallery (`clean-up-media-gallery`)
* Images (`clean-up-empty-image-columns`)
* Category Relations (`clean-up-category-product-relations`)
* Website Relations (`clean-up-website-product-relations`)

as well as for all product attributes (`clean-up-empty-columns`). These attributes has to be set on subject level like

```json
{
  ...
  "operations" : [
    {
      "name" : "add-update",
      "plugins" : [
        ...
        {
          "id": "import.plugin.subject",
          "subjects": [
            {
              "id": "import_product.subject.bunch",
              "identifier": "files",
              "file-resolver": {
                "prefix": "product-import"
              },
              "params" : [
                {
                  "copy-images" : true,
                  "clean-up-media-gallery" : true,
                  "clean-up-website-product-relations" : true,
                  "clean-up-category-product-relations" : true,
                  "clean-up-empty-image-columns" : true,
                  "clean-up-empty-columns" : [ ... ]
                }
              ]
              ...
            }
          ]
        }
      ]
    }
  ]
}
```