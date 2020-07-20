---
title: 'Product Links'
taxonomy:
    category:
        - docs
visible: true
---

Pacemaker Community provides the functionality to import product links as well as their positions. Actually *related*, *upsell* and *crosssell* links are supported. As Pacemaker Community also supports the import of *grouped* products, which are nothing else than the additional link type `super`, it is possible to import the position of the products that are linked to a grouped product.

To import those link types, simply a comma (,) separated list of the linked products has to be specified in the appropriate column

* column `related_skus` for related products that'll be rendered on the product detail page
* column `upsell_skus` for upsell products that'll be rendered on the product detail page
* column `crosssell_skus` for crosssell products that'll be rendered on the shopping cart
* column `associated_skus` for products that will be part of a grouped product, which will also be rendered on the product detail page

For example, the columns to import those link types may look like

| sku     | ... | related_skus    | upsell_skus     | crosssell_skus  | associated_skus | ... |
|:--------|:----|:----------------|:----------------|:----------------|:----------------|:----|
| 24-MB01 |     | 24-MB02,24-MB03 | 24-WG03,24-WG04 | 24-SB01,24-SB02 | 24-AS01,24-AS02 |     |

### Link Positions

Beside the SKUs of the linked product itself, it is also possible to specify the position of the linked products

| sku     | ... | related_position | upsell_position | crosssell_position | associated_position | ... |
|:--------|:----|:-----------------|:----------------|:-------------------|:--------------------|:----|
| 24-MB01 |     | 1,2              | 2,1             |                    | 1,2                 |     |

The position also has to be a comma (,) separated list, but it contains numbers instead of the SKUs. The numbers finally are the position the linked product will be rendered in the GUI.

!! Please be aware, that the positions are **NOT** mandatory and the columns can be empty. If so, the Pacemaker Community creates the position itself, based on the order of the given SKUs. If positions will be changed in the Magento backend, they will be **OVERWRITTEN** within the next import process!!

### Magento 2 CE < 2.1.6

Magento 2 CE supports positions for product links, as well as Magento 2 EE. By default, up to version 2.1.6, importing product positions is **NOT** possible in the CE, because the database of the CE lack's of missing rows in the `catalog_product_link_attribute` table.

In case, that the rows are not available, the positions, defined in the CSV file's columns 

* `related_position`
* `crosssell_position`
* `upsell_position`

will be ignored.

To enable importing positions, add the following rows the Magento 2 CE database

```sql
INSERT INTO 
        `catalog_product_link_attribute` (
            `link_type_id`, 
            `product_link_attribute_code`, 
            `data_type`
        ) 
    VALUES
        (1,'position','int'),
        (4,'position','int'),
        (5,'position','int');
```

! Make sure, that the values are **NOT** already available, before adding them!