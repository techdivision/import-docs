---
title: 'Core Components'
taxonomy:
    category:
        - docs
visible: true
---

This page lists the main components, that provides Magento 2 import core functionality to import products, categories and attributes.

### General (Independent from Edition)

* [import](https://github.com/techdivision/import) - A core library supporting generic Magento 2 import functionality
* [import-cli](https://github.com/techdivision/import-cli) - CLI command implementation used by the implementing CLI applications 
* [import-app-simple](https://github.com/techdivision/import-app-simple) - Generic application implementation that uses Symfony Console + DI as well as Pacemaker Community to provide Magento 2 CE/EE import functionality
* [import-configuration-jms](https://github.com/techdivision/import-configuration-jms) - A [JMS](https://github.com/schmittjoh/serializer) based Pacemaker Community configuration implementation

### Components for Community Edition (CE)

These are the Pacemaker Community core components for the Magento 2 Community Edition (CE).

* [import-product](https://github.com/techdivision/import-product) - Provides Product Import functionality
* [import-product-url-rewrite](https://github.com/techdivision/import-product-url-rewrite) - Provides Product URL Rewrite Import functionality
* [import-product-bundle](https://github.com/techdivision/import-product-bundle) - Provides Bundle Product Import functionality
* [import-product-link](https://github.com/techdivision/import-product-link) - Provides Product Relation import functionality
* [import-product-media](https://github.com/techdivision/import-product-media) - Provides Product Image Import functionality
* [import-product-variant](https://github.com/techdivision/import-product-variant) - Provides Configurable Product Import functionality
* [import-product-grouped](https://github.com/techdivision/import-product-grouped) - Provides Grouped Product Import functionality
* [import-product-msi](https://github.com/techdivision/import-product-msi) - Provides Product MSI Import functionality
* [import-product-tier-price](https://github.com/techdivision/import-product-tier-price) - Provides Product Tier Price Import functionality
* [import-category](https://github.com/techdivision/import-category) - Provides Category Import functionality
* [import-attribute](https://github.com/techdivision/import-attribute) - Provides Attribute Import functionality
* [import-customer](https://github.com/techdivision/import-customer) - Provides Customer Import functionality
* [import-customer-address](https://github.com/techdivision/import-customer-address) - Provides Customer Address Import functionality
* [import-converter](https://github.com/techdivision/import-converter) - Provides generic converter functionality
* [import-converter-product-category](https://github.com/techdivision/import-converter-product-category) - Provides the functionality to convert a CSV file with category from a CSV file with products
* [import-converter-product-attribute](https://github.com/techdivision/import-converter-product-attribute) - Provides the functionality to convert a CSV file with attribute option values from a CSV file with products.

!!!! Several components, like import-attribute, will also work with the EE, so there is not separate implementation.

### Components for Enterprise Edition (EE)

These are the Pacemaker Community core components for the Magento 2 Enterprise Edition (EE).

* [import-ee](https://github.com/techdivision/import-ee) - Provides Core Import functionality for Magento 2 EE
* [import-product-ee](https://github.com/techdivision/import-product-ee) - Provides Product Import functionality for Magento 2 EE
* [import-product-bundle-ee](https://github.com/techdivision/import-product-bundle-ee) - Provides Bundle Product Import functionality for Magento 2 EE
* [import-product-link-ee](https://github.com/techdivision/import-product-link-ee) - Provides Product Import Relation functionality for Magento 2 EE
* [import-product-media-ee](https://github.com/techdivision/import-product-media-ee) - Provides Product Import Image functionality for Magento 2 EE
* [import-product-variant-ee](https://github.com/techdivision/import-product-variant-ee) - Provides Configurable Product Import functionality for Magento 2 EE
* [import-product-grouped-ee](https://github.com/techdivision/import-product-grouped-ee) - Provides Grouped Product Import functionality for Magento 2 EE
* [import-category-ee](https://github.com/techdivision/import-category-ee) - Provides Category Import functionality for Magento 2 EE
* [import-converter-ee](https://github.com/techdivision/import-converter-ee) - Provides generic Magento 2 EE converter functionality