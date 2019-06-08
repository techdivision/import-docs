---
title: 'Product Import'
visible: true
---

The structure for the Product import is 100 % compatible with the Magento 2 CSV structure. Have a look at the Magento 2 [documentation](http://docs.magento.com/m2/ce/user_guide/system/data-attributes-product.html) for a detailed description of the CSV file structure.

In addition to the Magento 2 CSV structure, it is possible to add additional columns to allow tier prices and MSI stock data also be part of the product import. Both columns contains serialized data like the `additional_attributes` column does.

During product import, M2IF extracts the data from the columns and creates separate CSV files. These CSV files are 1:1 the format described for [Tier Prices](/file-structure/product-import-tier-prices) and [MSI](/file-structure/product-import-msi) import in the corresponding sections. After the products have been imported, M2IF imports the tier prices as well as the MSI data.

### Add Tier Prices to Product Import

To import the tier prices with the default product import, two steps are necessary. 

#### Add Additional Column

First step is to add the column `tier_prices` to the CSV file with the product data. For example, the column **MUST** contain the data in the following structure, e. g.

```
qty=10,price=20,value_type=fixed,website=All Websites,customer_group=ALL GROUPS|qty=20,price=30,value_type=fixed,website=All Websites,customer_group=ALL GROUPS
```

The example above has two rows with tier prices, but there is no limiitation in how much rows of tier prices the column comtains. Each row with tier prices will be separated by `|` whereas each column of a row, containing the `attribute_code` to `value` pairs will be separated by the common `,`. The `attribute_code` to `value` pairs itself will use the `=` char for separation. Our sample data comes with an [example](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/data/products/configurable/product-import_20190226-095345_01.csv) how the file should look like. 

#### Extend Configuration

The second step is, to add the subject that processes the tier prices to your configuration file. A example configuration file for the [Community](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/conf/products/techdivision-import-price-tier.json)  Edition is part of the M2IF [commandline tool](https://github.com/techdivision/import-cli-simple)

Basically, the plugin configuration for the apropriate operation has to be extended with

* the subject `import_product_tier_price.subject.tier_price` with the observer `import_product_tier_price.observer.tier_price.update` for the `add-update` and the observer `import_product_tier_price.observer.tier_price` for the `replace operation
* a listener `import_product_tier_price.listener.delete.obsolete.tier_prices` for the event `plugin.process.success` on subject level (only for `add-update` operation)
* param `clean-up-tier-prices` either with the value `true` or `false` whether tier-prices should be cleaned-up or not, also on subject level (only for `add-update` operation)

for the necessary subjects has the following structure. For the `add-update` it'll look like

```json
{
 ...,
 "operations": [
   {
      "name" : "add-update",
      "plugins" : [,
        {
          "id": "import.plugin.subject",
          "subjects": [
            ...,
            {
              "id": "import_product.subject.bunch",
              ...,
              "params" : [
                {
                  "copy-images" : false
                }
              ],
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

For the `replace` operation, the subject configuration looks like

```json
{
  ...,
  "operations": [
       {
      "name" : "replace",
      "plugins" : [
        ...
        {
          "id": "import.plugin.subject",
          "subjects": [{
              "id": "import_product.subject.bunch",
              "identifier": "files",
              "file-resolver": {
                "prefix": "product-import"
              },
              "filesystem-adapter" : {
                "id" : "import.adapter.filesystem.factory.league",
                "adapter" : {
                  "type" : "League\\Flysystem\\Adapter\\Local"
                }
              },
              "params" : [
                {
                  "copy-images" : false
                }
              ],
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
        ...
      ]
    }
  ]
}
```

For both operations, it has to be be addeded after the `import_product_url_rewrite.subject.url.rewrite`. The configuration of the `delete` operation don't need any customizations as the tier prices will be cleaned-up by their foreign keys.

### Add MSI to Product Import

To import MSI with the default product import, two steps are necessary. 

#### Add Additional Column

Add the column `inventory_source_items` to also import MSI stock data with the product import. For example, the column **MUST** contain the data in the following structure, e. g.

```csv
source_code=default,quantity=10.0,status=1|source_code=inventory-01,quantity=5.0,status=1
```

As for the tier prices, the column with the MSI inventory source items supports the same format. Our sample data comes with an [example](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/data/products/configurable/product-import_20190226-095345_01.csv) how the file should look like.

#### Extend Configuration

The second step is, to add the subject that processes the MSI to your configuration file. A example configuration file for the [Community](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/conf/products/techdivision-import-inventory-msi.json) Edition is part of the M2IF [commandline tool](https://github.com/techdivision/import-cli-simple).

Basically, the plugin configuration for the apropriate operation has to be extended with

* the subject `import_product_tier_price.subject.tier_price` with the observer `import_product_tier_price.observer.tier_price.update` for the `add-update` and the observer `import_product_tier_price.observer.tier_price` for the `replace operation
* a listener `import_product_tier_price.listener.delete.obsolete.tier_prices` for the event `plugin.process.success` on subject level (only for `add-update` operation)
* param `clean-up-tier-prices` either with the value `true` or `false` whether tier-prices should be cleaned-up or not, also on subject level (only for `add-update` operation)

for the necessary subjects has the following structure. For the `add-update` it'll look like