---
title: Clean-Up
taxonomy:
    category:
        - docs
visible: true
---

M2IF provides a *clean-up* functionality that provides the possiblity to remove values for empty columns or relattions that are not any longer part of the CSV file. This is very helpful when using the `add-update` operation, because in general data will only be added or updated if already available.

!!! As deleting data can reduce performance significantly, is should be used carefully!

### Product Import

Clean-Up functionality for the product import is **activated** by default and available for

* [Images](#images) (`clean-up-empty-image-columns`)
* [Media Gallery](#media-gallery) (`clean-up-media-gallery`)
* [Category Relations](#category-relations) (`clean-up-category-product-relations`)
* [Website Relations](#website-relations) (`clean-up-website-product-relations`)
* [Tier Prices](#tier-prices) (`clean-up-tier-prices`)
* [URL Rewrites](#url-rewrites) (`clean-up-url-rewrites`)

as well as for all product attributes (`clean-up-empty-columns`). To deactivate the *clean-up* functionality, add a snippet, e. g. `<custom-configuration-dir>/operations.json` and override the corresponding `clean-up-*` flags like

```json
{
  "operations": {
    "general": {
      "catalog_product": {
        "add-update.url-rewrites": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "subjects": [
                {
                  "id": "import_product_url_rewrite.subject.url.rewrite",
                  "file-resolver": {
                    "prefix": "url-rewrite"
                  },
                  "params": {
                    "clean-up-url-rewrites": false
                  },
                  "observers": [
                    {
                      "import": [
                        "import_product_url_rewrite.observer.url.rewrite.update"
                      ]
                    }
                  ]
                }
              ]
            }
          }
        }
      },
      "catalog_product_tier_price": {
        "add-update": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "listeners": [
                {
                  "plugin.process.success": [
                    "import_product_tier_price.listener.delete.obsolete.tier_prices"
                  ]
                }
              ],
              "params": {
                "clean-up-tier-prices": false
              },
              "subjects": [
                {
                  "id": "import_product_tier_price.subject.tier_price",
                  "listeners": [
                    {
                      "subject.import.success": [
                        "import_product.listener.register.sku.to.pk.mapping"
                      ]
                    }
                  ],
                  "file-resolver": {
                    "prefix": "product-import-tier-price"
                  },
                  "observers": [
                    {
                      "import": [
                        "import_product_tier_price.observer.tier_price.update"
                      ]
                    }
                  ]
                }
              ]
            }
          }
        }
      }  
    },
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
                    "clean-up-media-gallery": false,
                    "clean-up-empty-image-columns": false,
                    "clean-up-website-product-relations": false,
                    "clean-up-category-product-relations": false,
                    "clean-up-empty-columns": []
                  },
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

#### <a name="url-rewrites-category"></a>URL Rewrites

Cleaning-Up URL rewrites will be ncessary in most cases and should be activated by setting the flag `clean-up-url-rewrites` to `true`,