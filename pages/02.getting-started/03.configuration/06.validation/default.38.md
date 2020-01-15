---
title: Validation
published: false
---

Up with version 3.8.0, validation for all entity types will be activated by default. Especially in case of huge CSV files, lets say > 100 MB, validation can slow down the import process massively. Therefore, the validation is highly customizable and can, if necessary, completely be switched off.

### Custom Validations

For each entity type a snippet, that declares the available operations, is available in the corresponding repositories configuration folder `etc/configuration/operations.json`. Each of This files contain's the configuration for the validation operation. By overriding this definition, e. g. with a custom snippet like `custom-configuration-dir>/operations.json`, you've full control what will be validated and how the validation works.

In general, the validation operation is based on a validator subject, an observer, some listeners and a bunch of callbacks. Finally, the validations are implemented as callbacks which allows you register one or more validation callbacks for each column. M2IF comes with some specialized callbacks that only can used for the corresponding columns and a custom regex validator that can be used to integrate custom, regex based validations.

To go into details, let's have a look at the validation operation of the `catalog_product` entity in the [techdivision/import-product](https://github.com/techdivision/import-product/blob/19.x/etc/configuration/operations.json#L118) repository. In order to make it easier to understand, we've removed the unimportant parts.

```json
{
  "operations": {
    "general": {
      "catalog_product": {
        "validate": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "subjects": [
                {
                  ...
                  "params" : {
                    "custom-validations" : {
                      "sku" :  [ "/.+/" ],
                      "product_type": [ "simple", "virtual", "configurable", "bundle", "grouped", "giftcard" ],
                      "visibility": [ "Not Visible Individually", "Catalog", "Search", "Catalog, Search" ]
                    }
                  },
                  "callbacks": [
                    {
                      "sku": [ "import.callback.custom.regex.validator" ],
                      "store_view_code": [ "import.callback.store.view.code.validator" ],
                      "attribute_set_code": [ "import.callback.attribute.set.name.validator" ],
                      "product_type": [ "import.callback.custom.array.validator" ],
                      "tax_class_id": [ "import_product.callback.validator.tax.class" ],
                      "product_websites": [ "import.callback.store.website.validator" ],
                      "visibility": [ "import.callback.visibility.validator" ],
                      "related_skus": [ "import_product.callback.validator.link" ],
                      "upsell_skus": [ "import_product.callback.validator.link" ],
                      "crosssell_skus": [ "import_product.callback.validator.link" ],
                      "created_at" : [ "import.callback.validator.datetime" ],
                      "updated_at" : [ "import.callback.validator.datetime" ],
                      "special_price_to_date" : [ "import.callback.validator.datetime" ],
                      "special_price_from_date" : [ "import.callback.validator.datetime" ],
                      "custom_design_to" : [ "import.callback.validator.datetime" ],
                      "custom_design_from" : [ "import.callback.validator.datetime" ],
                      "new_to_date" : [ "import.callback.validator.datetime" ],
                      "new_from_date" : [ "import.callback.validator.datetime" ],
                      "price" : [ "import.callback.validator.number" ],
                      "special_price" : [ "import.callback.validator.number" ],
                      "map_price" : [ "import.callback.validator.number" ],
                      "msrp_price" : [ "import.callback.validator.number" ],
                      "qty" : [ "import.callback.validator.number" ]
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

#### Regex Validator

The regex validator, used to validate the SKU uses a regular expression to validate the value of the corresponding column. For the SKU, the regular expression simply results in `/.+/` what allows any character with at least one char. So if you want to allow digitals only, you can change the regular expression in the `params/custom-validations` array from `/.+/` to `/\+d/`. Our recommendation for a regex builder is https://regex101.com/ which will help you a lot to find the right expression for your needs.

#### Custom Array Validator

If you don't want to use a regular expression, but you've a list of allowed values, the custom array validator will be a good decision. By default, we use it to validate the product type. To add your own product type, simply extend the list in the `params/custom-validations` array, e. g. to `"simple", "virtual", "configurable", "bundle", "grouped", "giftcard", "my_product_type"`.