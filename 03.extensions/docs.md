---
title: Extensions
visible: true
---

In more complex projects, it'll we possible, that addional libraries are necessary. As the M2IF - Simple Console Tool uses a Symfony DI container, it is necessary to register the additional library by adding it to the configuration file. Depending on how the M2IF - Simple Console Tool has been installed, there a two options.

> Whenever you write an extension library do NOT forget to provide the Symfony DI configuration.

## Extension Libraries

Assuming, that the M2IF - Simple Console Tool has been installed as Composer library, together with a Magento 2 installation, the simplest way to register an additional extension will be adding it as a extension library like

```json
"extension-libraries" : [
  "techdivision/import-product-magic360"
]
```

> This is only possible, if the additional library uses the same Composer autoloader as M2IF - Simple Console Tool does.

## Additional Vendor Directories

Assuming, that the M2IF - Simple Console Tool PHAR archive will be used, it is necessary, that the Composer class loader of the additional library vendor directory will be added like

```json
"additional-vendor-dirs" : [
  {
    "vendor-dir" : "target/vendor",
    "libraries": [
      "techdivision/import-product-magic360"
    ]
  }
]
```

This register's the class loader of specified vendor directory and parse's the libraries for the necessary DI configuration files.