---
title: 'Getting Started'
taxonomy:
    category:
        - docs
visible: true
---

!! ATTENTION: As of version 3.8.0, the structure of the configuration has changed considerably and the previous configuration files can no longer be used. In order to avoid complex adjustments of the configuration, version 3.8.0 merged the configuration for all entities into one, but dedicated overwriting of individual settings is now possible, e. g. for the log level.

To install the Magnento 2 Import Framework, composer is necessary. The framework itself is a set of components that provide import functionality for Magento 2. This repository, based on Symfony Console, uses the the package [M2IF](https://github.com/techdivision/import) and provides a command line tool with import functionality for Magento 2 standard CSV files.

Before you start, keep in mind, that we've prepared a complete set of sample data for you. The sample data contains attribute-sets, attributes, categories as well as products and is based on the Magento 2 sample data. Please have a look at our [sample data repository](https://github.com/techdivision/import-sample-data), when you are going to test M2IF or if you want to get an idea, how the CSV files have to look like.

!! Up from M2IF version 3.6.0 can be used for Magento 2.2.x as well as 2.3.x!

[plugin:youtube](https://youtu.be/AzXrs1c92RY)