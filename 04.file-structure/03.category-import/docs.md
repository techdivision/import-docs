---
title: 'Category Import'
visible: true
---

M2IF comes with a category import and the apropriate command `import:categories` therefore. You can find more information about how to invoke the command in the [Usage](/getting-started/usage) section.

In general the filename for the dedicated MSI import **MUST** match the following pattern `<PREFIX>_<FILENAME>_<COUNTER>.csv`, whereas the default `<PREFIX>` is `category-import`, the `<FILENAME>` is a combination of date and time like `20190608-114344`, and the `<COUNTER>` is a consecutive number with two digits starting with `01`. This results in a filename like `category-import_20190608-114344_01.csv`. Additionally an apropriate `.ok` file is needed.

> ATTENTION: Please be aware that the three parts **MUST** be separated with an underscore "_" as there is meaning for the bunch import behind its structure.

### Unique Identifier

For the product import, the SKU is for sure the unique identifier of a product. In case of the categories this is a bit more complicated as Magento uses a string of the imploded category IDs, separated by a slash (/), e. g. `1/2/120/1502`. As it is simply not possible that e. g. the PIM system is aware of those IDs, the category instead needs a string with the admin store category names instead of the ID and expects it in the column *path*. This results in values that looks like `Default Category/Women/Tops/Hoodies & Sweatshirts`.

A common problem can be, that one of those category names itself contain a slash (/). In that case, the category name has to be enclosed with the default enclosing character, e. g. the double aposthrophe ("). As for the [Additional Attributes](/file-structure/product-import/additional-attributes) column of the product import this is necessary to make sure that the path can be extracted and the category will be added to the correct parent node. The value for a category path that contains slashes has to look like 

```csv
"Default Category/Dachdecker- & Spenglerarbeiten/""Kehl-/Traufenanschlüsse & -Belüftungen""/""Trauf-/Lüftungsrollen"""
```

### Columns

The CSV file with the categories for the Magento 2 CE/EE consists of the following columns

| Column Name                | Type     | Mandatory | Description                                                                           | Example |
|:---------------------------|:---------|:----------| :-------------------------------------------------------------------------------------|:--------|
| store_view_code            | varchar  | yes       | The specific store view(s) where the category is available. If blank, the category is available at the default store view. | e.g. en_US or [read](/file-structure/category-import/scopes) more about scopes |
| attribute_set_code         | varchar  | yes       | Assigns the product to a specific attribute set or product template, according to product type. Once the product is created, the attribute set cannot be changed. | default |
| path                       | varchar  | yes       | The complete category path, including the root category.                              | Default Category/MyCategory |
| name                       | varchar  | yes       | The category name appears the naviagtion, and is the name that customers use to identify the category. | My Category |
| is_active                  | int      | yes       | Enables or disables the category.                                                     | 1       |
| is_anchor                  | int      | yes       | If the category is anchor, the category's products as well as the products of the subcategories will be listed. | 1       |
| include_in_menu            | int      | yes       | Specifies if the category will be included in the menu or not.                        | 1       |
| use_name_in_product_search | int      | yes       | If the category name is used for fulltext search on products                          | 1       |
| display_mode               | varchar  | yes       | One of "Products only", "Static block only" or "Static block and products"            | Products only |
| url_key                    | varchar  | yes       | The category's unique URL key                                                         | my-category |
| description                | text     | no        | The category description, that'll be rendered on the category page                    | Some longer text here |
| image_path                 | varchar  | no        | The absolute or relative path to a category image file                                | images/categories/my-category.png |
| meta_title                 | varchar  | no        | The category's title that'll be rendered in the category page's &lt;title&gt; tag           | My Category Name |
| meta_keywords              | text     | no        | The category's meta keywords that'll be rendered in the category page's <meta name="keywords"> tag | Category Name, Keyword 1, Keyword 2 |
| meta_description           | text     | no        | The category's meta description that'll be rendered in the category page's <meta name="description"> tag | A good Description with SEO relevant content |
| landing_page               | int      | no        | The ID of a CMS block that has to be rendered in the category page                    | 2       |
| position                   | int      | no        | The category's position in the navigation                                             | 10      |
| custom_design              | varchar  | no        | The custom design name used to display the catgory                                    | Magneto Blank |
| custom_design_from         | datetime | no        | The start date for the scheduled design update                                        | 10/24/16, 12:36 PM |
| custom_design_to           | datetime | no        | The end date for the scheduled design update                                          | 10/24/16, 12:36 PM |
| page_layout                | varchar  | no        | A custom page layout used to disploy the category, one of 1 column, 2 columns with left bar, 2 columns with right bar, 3 columns, Empty | 1 column |
| custom_layout_update       | text     | no        | A custom page layout update in XML format                                             | <referenceContainer name="catalog.leftnav" remove="true"/> |
| available_sort_by          | text     | no        | The comma separated product list sortings for the catgory                             | Position,Name |
| default_sort_by            | varchar  | no        | The default product list sorting for the category                                     | Position |
| custom_apply_to_products   | int      | no        | If set to 1, the design will also be applied to the products listed in the category   | 1       |
| custom_use_parent_settings | int      | no        | Overrides the custom design settings with the default one's                           | 1       |
| filter_price_range         | decimal  | no        | The layered navigation price steps                                                    | 100.00  |
| created_at                 | varchar  | no        | The category's creation date                                                          | 10/24/16, 12:36 PM |
| updated_at                 | varchar  | no        | The date when the category has been updated                                           | 10/24/16, 12:36 PM |
| additional_attributes      | text     | no        | A comma separated list with additional attributes (the attributes **MUST** already be available) | custom_attribute_01=a-value,custom_attribute_02=value-01&#124;value-02 |