---
title: Clean-Up
taxonomy:
    category:
        - docs
visible: true
---

M2IF provides a *clean-up* functionality that provides the possiblity to remove values for empty columns or relattions that are not any longer part of the CSV file. This is very helpful when using the `add-update` operation, because in general data will only be added or updated if already available.

!! As deleting data can reduce performance significantly, is should be used carefully!

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

#### Images

In most cases it'll be useful, if images that are **NOT** referenced in the appropriate colum of the CSV file anymore, will be removed from the database. This can either be done by specifying the column names manually in this `clean-up-empty-columns`, e. g. if only a dedicated one should be cleaned-up, or and that will be more comfortable, by setting the flag `clean-up-empty-image-columns` to `true`. Setting the flag to `true` will load all attributes that has the value `media_image` in the `frontend_input` and adds the appropriate attribute code to the array with the colum names to clean-up.

#### Media Gallery

Beside clean-up the attribute values of the images, it'll also be necessary to clean-up the media gallery that holds the relations between the images and the products by setting the flag `clean-up-media-gallery` to `true`. This is mandatory if `clean-up-empty-image-columns` is set to `true` or if at least one image column name has been added to `clean-up-empty-columns`. 

#### Category Relations

In many cases product category relations doesn't change at all or change not really often. In case the product category relations will change frequently and it'll be necessary to update them with the `add-update` operation, this can be done by setting the flag `clean-up-category-product-relations` to `true`. As the product category relation is not only persisted in a column, this relation can **NOT** be cleaned-up by adding a column name to the array `clean-up-empty-columns`.

#### Website Relations

In many cases product website relations doesn't change at all or change not really often. In case the product website relations will change frequently and it'll be necessary to update them with the `add-update` operation, this can be done by setting the flag `clean-up-website-product-relations` to `true`. As the product website relation is not only persisted in a column, this relation can **NOT** be cleaned-up by adding a column name to the array `clean-up-empty-columns`.

#### Tier Prices

Prices nearly always needs to be up-to-date, so if tier prices will be imported the clean-up functionality should be activated by setting the flag `clean-up-tier-prices` to `true`.

#### URL Rewrites

Cleaning-Up URL rewrites will be ncessary in most cases and should be activated by setting the flag `clean-up-url-rewrites` to `true`,

### Categories

As the category functionality in Magento is less complicated, the clean-up actually only provides one flag for the

* [URL Rewrites](#url-rewrites-category) (`clean-up-url-rewrites`)

As for the product import the flags has to be configured on subject level like

```json
{
  "operations": {
    "ce": {
      "catalog_category": {
        "add-update.url-rewrite": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "subjects": [
                {
                  "id": "import_category.subject.bunch",
                  "file-resolver": {
                    "prefix": "category-url-rewrite"
                  },
                  "params": {
                    "clean-up-url-rewrites": false
                  },
                  "observers": [
                    {
                      "import": [
                        "import_category.observer.url.rewrite.update"
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

#### <a name="url-rewrites-category"></a>Category URL Rewrites

Cleaning-Up URL rewrites will be ncessary in most cases. To *deactivate* it, set the flag `clean-up-url-rewrites` to `false`.