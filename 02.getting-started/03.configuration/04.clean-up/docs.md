---
title: Clean-Up
visible: true
---

M2IF provides a clean-up functionality that provides the possiblity to remove values for empty columns. This is very helpful when using the `add-update` operation, because in general data will only be added or updated if already available.

> As deleting data can reduce performance significantly, is should be used carefully!

### Product Import

Clean-Up functionality for the product import is available for

* [Images](#images) (`clean-up-empty-image-columns`)
* [Media Gallery](#media-gallery) (`clean-up-media-gallery`)
* [Category Relations](#category-relations) (`clean-up-category-product-relations`)
* [Website Relations](#website-relations) (`clean-up-website-product-relations`)
* [Tier Prices](#tier-prices) (`clean-up-tier-prices`)
* [URL Rewrites](#url-rewrites) (`clean-up-url-rewrites`)

as well as for all product attributes (`clean-up-empty-columns`). These flags has to be set on subject level like

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
                  "clean-up-empty-image-columns" : true,
                  "clean-up-website-product-relations" : true,
                  "clean-up-category-product-relations" : true,
                  "clean-up-empty-columns" : [ ... ]
                }
              ]
              ...
            },
            {
              "id": "import_product_url_rewrite.subject.url.rewrite",
              "identifier": "files",
              "file-resolver": {
                "prefix": "url-rewrite"
              },
              "params" : [
                {
                  "clean-up-url-rewrites" : true
                }
              ]
              ...
            },
            {
              "id": "import_product_tier_price.subject.tier_price",
              "identifier": "files",
              "file-resolver": {
                "prefix": "tier-price"
              },
              "params" : [
                {
                  "clean-up-tier-prices" : true
                }
              ],
              ...
            }
          ]
        }
      ]
    }
  ]
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

Clean-Up functionality for the category import is available for

* [URL Rewrites](#url-rewrites-category) (`clean-up-url-rewrites`)

#### <a name="url-rewrites-category"></a>URL Rewrites

Cleaning-Up URL rewrites will be ncessary in most cases and should be activated by setting the flag `clean-up-url-rewrites` to `true`,