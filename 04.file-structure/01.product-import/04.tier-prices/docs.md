---
title: 'Tier Prices'
visible: true
---

### Tier Prices

To import the tier prices with the default product import, two steps are necessary. 

#### Add Additional Column

First step is to add the column `tier_prices` to the CSV file with the product data. For example, the column **MUST** contain the data in the following structure, e. g.

```
qty=10,price=20,value_type=fixed,website=All Websites,customer_group=ALL GROUPS|qty=20,price=30,value_type=fixed,website=All Websites,customer_group=ALL GROUPS
```

The example above has two rows with tier prices, but there is no limiitation in how much rows of tier prices the column comtains. Each row with tier prices will be separated by `|` whereas each column of a row, containing the `attribute_code` to `value` pairs will be separated by the common `,`. The `attribute_code` to `value` pairs itself will use the `=` char for separation. Our sample data comes with an [example](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/data/products/configurable/product-import_20190226-095345_01.csv) how the file should look like. 

#### Extend Configuration

The second step is, to add the listener, the subjects and the observers that processes the tier prices to your configuration file. A example configuration file for the [Community](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/conf/products/techdivision-import-price-tier.json)  Edition is part of the M2IF [commandline tool](https://github.com/techdivision/import-cli-simple).

Basically, the configuration for the apropriate operations has to be extended with

* the subject `import_product_tier_price.subject.tier_price` with the observer `import_product_tier_price.observer.tier_price.update` for the `add-update` and the observer `import_product_tier_price.observer.tier_price` for the `replace` operation
* the observer `import_product_tier_price.observer.product.tier_price` that has to be added **AFTER** the first observer of the first `import_product.subject.bunch` subject
* a listener `import_product_tier_price.listener.delete.obsolete.tier_prices` for the event `plugin.process.success` on subject level (*only for `add-update` operation*)
* param `clean-up-tier-prices` on subject level either with the value `true` or `false` which decides whether or not tier-prices should be cleaned-up (*only for `add-update` operation*)

> If the clean-up functionality is activated with the param `clean-up-tier-prices` set to `true`, all tier prices that are **NOT** anymore part of the SKUs in the CSV file will be **REMOVED**.  

For the `replace` operation, the configuration has to look like

```json
{
  ...,
  "operations": [
       {
      "name" : "replace",
      "plugins" : [
        ...,
        {
          "id": "import.plugin.subject",
          "subjects": [
            {
              "id": "import_product.subject.bunch",
    	      ...,
              "observers": [
                {
                  "import": [
                    "import_product.observer.composite.base.replace",
                    "import_product_tier_price.observer.product.tier_price"
                  ]
                }
              ]
            },
            ...,
            {
              "id": "import_product_tier_price.subject.tier_price",
              "identifier": "files",
              "file-resolver": {
                "prefix": "tier-price"
              },
              "observers": [
                {
                  "import": [
                    "import_product_tier_price.observer.tier_price"
                  ]
                }
              ]
            }
          ]
        }
        ...,
      ]
    }
  ]
}
```

For the `add-update` operation, the configuration has to look like

```json
{
 ...,
 "operations": [
   {
      "name" : "add-update",
      "plugins" : [,
        {
          "id": "import.plugin.subject",
          "listeners" : [
             {
              "plugin.process.success" : [
                "import_product_tier_price.listener.delete.obsolete.tier_prices"
              ]
            }
          ],
          "params" : [
            {
              "clean-up-tier-prices" : true
            }
          ],
          "subjects": [
            ...,
            {
              "id": "import_product.subject.bunch",
              ...,
              "observers": [
                {
                  "import": [
                    "import_product.observer.composite.base.add_update",
                    "import_product_tier_price.observer.product.tier_price"
                  ]
                }
              ]
            },
			...,
            {
              "id": "import_product_tier_price.subject.tier_price",
              "file-resolver": {
                "prefix": "tier-price"
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
        ...,
      ]
    }
  ]
}
```

For both operations, it has to be be addeded after the `import_product_url_rewrite.subject.url.rewrite`. The configuration of the `delete` operation don't need any customizations as the tier prices will be cleaned-up by their foreign keys.