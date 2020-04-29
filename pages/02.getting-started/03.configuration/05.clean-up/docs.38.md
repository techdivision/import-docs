---
title: Clean-Up
taxonomy:
    category:
        - docs
visible: true
recaptchacontact:
    enabled: false
---

!! As deleting data can reduce performance significantly, the clean-up functionality should be used carefully!

M2IF provides a *clean-up* functionality that provides the possiblity to remove values for empty columns or relattions that are not any longer part of the CSV file. This is very helpful when using the `add-update` operation, because in general data will only be added or updated if already available.

### Product Import

*Clean-Up* functionality for the product import is **activated** by default and available for

* [Images](#media-gallery) (`clean-up-empty-image-columns`)
* [Media Gallery](#media-gallery) (`clean-up-media-gallery`)
* [Category Relations](#category-relations) (`clean-up-category-product-relations`)
* [Website Relations](#website-relations) (`clean-up-website-product-relations`)
* [Tier Prices](#tier-prices) (`clean-up-tier-prices`)
* [URL Rewrites](#url-rewrites) (`clean-up-url-rewrites`)

as well as for all product attributes (`clean-up-empty-columns`) which needs additional configuration. To activate the *clean-up* functionality for product attributes, 
as well as additional attributes, the columns that has to be *cleaned-up* has to be specified in the `clean-up-empty-columns` array like `"clean-up-empty-columns": [ "activity", "erin_recommends" ]`.

! Keep in mind, that columns that are specified in the `additional_attributes` column have to be *cleaned-up* **MUST** be available with empty values, e. g. if the column `activity` and `erin_recommends` has to be *cleaned-up* the column `additional_attributes` needs the value '"activity=,erin_recommends="'.

To deactivate the *clean-up* functionality, add a snippet, e. g. `<custom-configuration-dir>/operations.json` and override the corresponding `clean-up-*` flags like

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

#### <a name="media-gallery"></a>Images + Media Gallery

In most cases it is very helpful to delete images that are **NO** longer contained in the CSV files from the database. The image type, e.g. thumbnail as attribute, the link to the product, the name and the position are stored in separate tables. To clean-up both of them, it is necessary to set the flags `clean-up-empty-image-columns` and `clean-up-media-gallery` to `true`, which are the default values. Until you're not absolutely sure what you're doing, both flags should have the same value.

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