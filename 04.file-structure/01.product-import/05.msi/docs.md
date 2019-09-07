---
title: MSI
visible: true
---

To import MSI with the default product import, two steps are necessary. 

### Add Additional Column

Add the column `inventory_source_items` to also import MSI stock data with the product import. For example, the column **MUST** contain the data in the following structure, e. g.

```csv
source_code=default,quantity=10.0,status=1|source_code=inventory-01,quantity=5.0,status=1
```

As for the tier prices, the column with the MSI inventory source items supports the same format. Our sample data comes with an [example](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/data/products/configurable/product-import_20190226-095345_01.csv) how the file should look like.

### Extend Configuration

The second step is, to add the subject that processes the MSI to your configuration file. A example configuration file for the [Community](https://github.com/techdivision/import-cli-simple/blob/3.5.x/projects/sample-data/ce/2.3.x/conf/products/techdivision-import-inventory-msi.json) Edition is part of the M2IF [commandline tool](https://github.com/techdivision/import-cli-simple).

Basically, the configuration for the apropriate operations has to be extended with

* the subject `import_product_msi.subject.bunch` with the observer `import_product_msi.observer.inventory.source.item.update` for the `add-update` and the observers `import_product_msi.observer.clear.inventory.source.item` and `import_product_msi.observer.inventory.source.item` for the `replace` operation
* the observer `import_product_msi.observer.product.source.item` that has to be added **AFTER** the first observer of the first `import_product.subject.bunch` subject
* the observers `import_product_msi.observer.product.source.item` and `import_product.observer.composite.base.delete` **BEFORE** the `import_product.observer.composite.base.delete` of the subject `import_product.subject.bunch` of the `delete` operation
* the subject `import_product_msi.subject.bunch` with the observer `import_product_msi.observer.clear.inventory.source.item` of the `delete` operation

For the `delete` operation, the configuration has to look like

```json
{
 ...,
 "operations": [
   {
      "name" : "delete",
      "plugins" : [,
        {
          "id": "import.plugin.subject",
          "subjects": [
            ...,
            {
              "id": "import_product.subject.bunch",
              ...,
              "observers": [
                {
                  "import": [
                    "import_product.observer.last.entity.id",
                    "import_product_msi.observer.product.source.item",
                    "import_product.observer.composite.base.delete"
                  ]
                }
              ]
            },
			...,
            {
              "id": "import_product_msi.subject.bunch",
              "identifier": "files",
              "file-resolver": {
                "prefix": "inventory-msi"
              },
              "observers": [
                {
                  "import": [
                    "import_product_msi.observer.clear.inventory.source.item"
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
                    "import_product_msi.observer.product.source.item"
                  ]
                }
              ]
            },
            ...,
            {
              "id": "import_product_msi.subject.bunch",
              "identifier": "files",
              "file-resolver": {
                "prefix": "inventory-msi"
              },
              "observers": [
                {
                  "import": [
                    "import_product_msi.observer.clear.inventory.source.item",
                    "import_product_msi.observer.inventory.source.item"
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
          "subjects": [
            ...,
            {
              "id": "import_product.subject.bunch",
              ...,
              "observers": [
                {
                  "import": [
                    "import_product.observer.composite.base.add_update",
                    "import_product_msi.observer.product.source.item"
                  ]
                }
              ]
            },
			...,
            {
              "id": "import_product_msi.subject.bunch",
              "identifier": "files",
              "file-resolver": {
                "prefix": "inventory-msi"
              },
              "observers": [
                {
                  "import": [
                    "import_product_msi.observer.inventory.source.item.update"
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