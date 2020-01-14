---
title: Configuration
taxonomy:
    category:
        - docs
visible: true
---

!! ATTENTION: As of version 3.8.0, the structure of the configuration has changed considerably and the previous configuration files can no longer be used. In order to avoid complex adjustments of the configuration, version 3.8.0 merged the configuration for all entities into one, but dedicated overwriting of individual settings is now possible, e. g. for the log level.

If **NO** configuration file is specified, it will be loaded and merged from individual parts that are delivered with the respective repositories. In case of the `import:products`, only the operations, specified by the given shortcut, e. g. `add-update`, will be used and executed. By default, the configuration contains whether database configuration nor an image directory. Whereas the database configuration can be specified by the commandline options, it is neccessary to provide a custom configuration snipped that activates the flags `"copy-images":true`, `"clean-up-media-gallery": true"` and `"clean-up-empty-image-columns": true` and contains the paths to the image files. This snippet **MUST** have the JSON format, can have a random name, e. g. `images.json` and has to be placed in the [custom configuration dir](/getting-started/usage). By default, the value for the `custom-configuration-dir` is `<magento-install-directory>/app/etc/configuration`.

### Configuration Snippets

All configuration snippets **MUST** be in JSON format and have to be located in the [custom configuration directory](/getting-started/usage). In general, nearly all available configuration options/arguments can and **SHOULD** be defined in those snippets, instead of passing them as commandline options. The possiblity to override configuration values on the commandline should only be used during development or in special cases, e. g. when the values will be loaded from a third party system. 

The structure is separated into a general configuration section, the database configuration, the logger configuration and the configuration for the available operations.

#### General Configuration

The general configuration contains basic metadata like the Magento edition and version, which will be necessary when M2IF will be invoked from a directory other than the Magento installation directory and without the `--installation-dir` option. Additionally it can be used to specify default values, if you want to change the default operation from `add-update` to `replace` for example. The change those general metadata we recommend to place a snipped, e. g. with the name `<custom-configuration-dir>/configuration.json` in the custom configuration directory.

```json
{
  "magento-edition": "CE",
  "magento-version": "2.3.1",
  "operation-name" : "replace",
  "installation-dir" : "/var/www/magento"
}
```

#### Global Parameters

Global parameters in the configuration file enables developers to pass specific configuration values from the configuration file itself, from the commandline (using the `--params` option) or an addtional file (using the `--params-file` option) through to their import logic, e. g. project specifc observers.

##### In a Configuration Snippet

It's possible to create a snippet with params, e. g. `<custom-configuration-dir>/params.json` which contains values like

```json
{
  "params": { 
    "my-website-country-mapping": { 
      "DE": [ "de_DE", "de_AT", "de_CH" ], 
      "EN": [ "en_US", "en_UK" ] 
    } 
  }
}
```

These params can then be used wherever access to the configuration object is available, e. g. in an observer like

```php

namespace My\Project;

use TechDivision\Import\Observers\AbstractObserver;

/**
 * A custom observer implementation.
 */
class MyObserver extends AbstractObserver
{
    
    /**
     * Return's the global param with the passed name.
     *
     * @param string $name         The name of the param to return
     * @param mixed  $defaultValue The default value if the param doesn't exists
     *
     * @return string The requested param
     * @throws \Exception Is thrown, if the requested param is not available
     */
    public function getGlobalParam($name, $defaultValue = null)
    {
        return $this->getSubject()->getConfiguration()->getConfiguration()->getParam($name, $defaultValue);
    }
    
    /**
     * Will be invoked by the action on the events the listener has been registered for.
     *
     * @param \TechDivision\Import\Subjects\SubjectInterface $subject The subject instance
     *
     * @return array The modified row
     */
    public function handle(SubjectInterface $subject)
    {
        
        // load the params from the configuration
        $myWebsiteMapping = $this->getGlobalParam('my-website-country-mapping');
        
        // do something with the configuration value
    }
}
```

##### As Commandline Option

Beside the params that can be defined in the configuration file itself, additionally params can be specified on the commandline as option, e. g.

```sh
bin/import-cli-simple.phar import:products \
     --params='{ "params": { "my-website-country-mapping": { "DE": [ "de_LI" ] } } }'
```

which will append the value `de_LI` to the `DE` param of the `my-website-country-mapping`.

##### As file, defined as Commandline Option

Beside the possibility to specify the params directly as commandline option, it is also possible to specify a path to a file that contains the JSON encoded params. The file **MUST** have the following format

```json
{ 
  "params": { 
    "my-website-country-mapping": { 
      "DE": [ "de_LI" ]
    } 
  }
}
```
! Please be aware, that the values from the configuration file will be overwritten with the values from the commandline which again will be overwritten with the values from an addtional file that has been specified with the `--params-file` option.

#### Extend M2IF with additional libraries

In more complex projects, it'll we possible, that addional libraries are necessary. As the M2IF - Simple Console Tool uses a Symfony DI container, it is necessary to register the additional library by adding it to the configuration file. Depending on how the M2IF - Simple Console Tool has been installed, there a two options.

! Whenever you write an extension library do NOT forget to provide the Symfony DI configuration.

##### Extension Libraries

Assuming, that the M2IF - Simple Console Tool has been installed as Composer library, together with a Magento 2 installation, the simplest way to register an additional extension is to add a snippet, e. g. `<custom-configuration-dir>/extension-libraries.json` that contains the name of the library, like

```json
{
  "extension-libraries" : [
    "techdivision/import-product-magic360"
  ]
}
```

> This is only possible, if the additional library uses the same Composer autoloader as M2IF - Simple Console Tool does.

##### Additional Vendor Directories

Assuming, that the M2IF - Simple Console Tool PHAR archive will be used, it is necessary, that the Composer class loader of the additional library vendor directory will be added by a snippet, e. g. `<custom-configuration-dir>/additional-vendor-dirs`, which contains the following content

```json
{
  "additional-vendor-dirs" : {
      "vendor-dir" : "target/vendor",
      "libraries": [
        "techdivision/import-product-magic360"
      ]
    }
  }
}
```

#### Events

Beside the configured worklfow with Plugins, Subjects, Observers and Callbacks, addtional events are available to add custom functionality.

| Event Name                                | Description                                                                                                                    | Since Version |
|:------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------|:--------------|
| app.set.up                                | Is triggered before the import will be processed.                                                                              |               |
| app.tear.down                             | Is triggered after the import has been processed.                                                                              |               |
| app.process.transaction.start             | Is triggered before the application start's the transaction.                                                                   |               |
| app.process.transaction.success           | Is triggered after the application has the transaction committed successfully.                                                 |               |
| app.process.transaction.failure           | Is triggered after the application rollbacked the transaction.                                                                 |               |
| app.process.transaction.finished          | Is triggered after the application transaction has been finished (either it has been successful or not).                       |        3.8.0  |
| subject.artefact.process.start            | Is triggered before an import artefact will be processed.                                                                      |               |
| subject.artefact.process.success          | Is triggered when an import artefact has successfully been processed.                                                          |               |
| subject.artefact.process.failure          | Is triggered when an import artefact can not be processed.                                                                     |               |
| subject.artefact.row.process.start        | Is triggered when an import artefact has successfully been processed.                                                          |               |
| subject.artefact.row.process.success      | Is triggered when an import artefact has successfully been processed.                                                          |               |
| subject.artefact.header.row.process.start | Is triggered before an import artefact's header row will be processed.                                                         |         3.8.0 |
| subject.artefact.header.row.process.start | Is triggered when an import artefact's header row has successfully been processed.                                             |         3.8.0 |
| plugin.process.start                      | Is triggered before the plugin's `process()` method will be processed.                                                         |         3.4.0 |
| plugin.process.success                    | Is triggered after a plugin's `process()` method has been processed.                                                           |         3.4.0 |
| plugin.process.failure                    | Is triggered when an exception has been thrown during the plugin's `process()` method is processed.                            |         3.4.0 |
| plugin.export.start                       | Is triggered before a plugin's `export()` method will be processed.                                                            |         3.8.0 |
| plugin.export.success                     | Is triggered after a plugin's `export()` method has been processed.                                                            |         3.8.0 |
| plugin.export.failure                     | Is triggered when an exception has been thrown during the plugin's `export()` method is processed.                             |         3.8.0 |
| subject.import.start                      | Is triggered before a subject's `import()` method will be processed.                                                           |         3.4.0 |
| subject.import.success                    | Is triggered after a subject's `import()` method has been processed.                                                           |         3.4.0 |
| subject.import.failure                    | Is triggered when an exception has been thrown during the subject's `import()` is processed.                                   |         3.4.0 |
| subject.export.start                      | Is triggered before a subject's `export()` method will be processed.                                                           |         3.4.0 |
| subject.export.success                    | Is triggered after a subject's `export()` method has been processed.                                                           |         3.4.0 |
| subject.export.failure                    | Is triggered when an exception has been thrown during the subject's `export()` is processed.                                   |         3.4.0 |

Beside to possiblity to register global events, up with version 3.4.0 it is possible to register events on plugin and subject level. This avoids execution of events that only provide functionality for a dedicated plugin or subject, so keep in mind, that they will **NOT** be fired for every plugin or every subject.

##### Plug-In Level

The listeners will only be executed before, after or on failure of the plugin, for which the event has been  configured for. Configuration in a snippet, e. g. `<customer-installation-dir>/operations.json`, can look like

```json
{
  "operations": {
    "general": {
      "catalog_product_tier_price": {
        "add-update": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "listeners": [
                {
                  "plugin.process.success": [
                    "import_product_tier_price.listener.delete.obsolete.tier_prices"
                  ]
                }
              ],
              "subjects": [ ... ]
            }
          }
        }
      }
    }
  }
}
```

##### Subject Level

As subjects are responsible for importing **AND** exporting artefacts, events for both steps has been added. The listeners will only be executed before, after or on failure of the subject, for which the event has been configured for. Configuration in an appropriate snippet, e. g. `<customer-installation-dir>/operations.json`, can look like

```json
{
  "operations": {
    "general": {
      "catalog_product_tier_price": {
        "add-update": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "subjects": [
                {
                  "id": "import_product_tier_price.subject.tier_price",
                  "listeners": [
                    {
                      "subject.import.success": [
                        "import_product.listener.register.sku.to.pk.mapping"
                      ]
                    }
                  ],
                  "observers": [ ... ]
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

#### Default Listeners

By default, M2IF comes with several listeners registered. 

```json
{
  "listeners": [
    {
      "app.set.up": [
        "import.listener.render.ansi.art",
        "import.listener.render.operation.info",
        "import.listener.render.mysql.info",
        "import.listener.render.debug.info",
        "import.listener.initialize.registry"
      ],
      "app.process.transaction.success": [
        "import.listener.finalize.registry",
        "import.listener.archive",
        "import.listener.clear.artefacts",
        "import.listener.clear.directories",
        "import.listener.operation.report"
      ],
      "app.process.transaction.failure": [
        "import.listener.finalize.registry",
        "import.listener.render.validations"
      ],
      "app.tear.down": [
        "import.listener.import.history",
        "import.listener.clear.registry"
      ]
    }
  ]
}
```

The first one named `import.listener.render.ansi.art` is renders the nice ASCII art when invoking the CLI. The second and the third one are mandatory as they are responsible to initialize the registry of the actual import.

!!!! As solution partner, feel free to replace the ASCII art renderer with one of your choice, e. g. rendering the logo of your company.

#### Database

The configuration allows the registration of multiple databases, for example in a snippet `<custom-configuration-dir>/databases.json`, which can look like

```json
{
  "databases": [
    {
      "id": "local",
      "default": false,
      "pdo-dsn": "mysql:host=127.0.0.1;dbname=appserver_magento2_ee212",
      "username": "your-username",
      "password": "your-password"
    },
    {
      "id": "remote",
      "default": true,
      "pdo-dsn": "mysql:host=127.0.0.130;dbname=appserver_magento2_ee212",
      "username": "your-username",
      "password": "your-password"
    }
  ]
}
```

Depending whether the commandline option `--use-db-id` and the specified value, the database with the given ID will be used. If the commandline options is **NOT** specified, the one with the  flag `"default": true` will be used, if not found, the first configured database will be use.

If a value for the commandline option `--db-pdo-dsn` has been specified, the `--use-db-id` option will be ignored and the given DSN value will be used for database connection instead. Additionally the credentials, by using the `--db-username` and `--db-password` options also needs to be specified.

#### Loggers

M2IF uses [Monolog](https://github.com/Seldaek/monolog) to provide the basic logging functionality. Therefore, at least one logger instance is necessary. By default, if no logger has been configured, a system logger will be instanciated, that writes log messages to the error log that has been configured in the `php.ini` file of the used PHP installation.

To add additional loggers, e. g. in case you want to send mails if an exception has been thrown, the configuration can be extended, e. g. with a snippet `<custom-configuration-dir>/loggers.json` which looks like

```json
{
  "loggers": {
    "mail": {
      "id": "import.logger.factory.monolog",
      "channel-name": "logger/mail",
      "handlers": [
        {
          "id": "import.logger.factory.handler.swift",
          "formatter": {
            "id": "import.logger.factory.formatter.line",
            "params": {
              "format": "[%datetime%] %channel%.%level_name%: %message% %context% %extra%",
              "date-format": "Y-m-d H:i:s",
              "allow-inline-line-breaks": true,
              "ignore-empty-context-and-extra": true
            }
          },
          "params": {
            "log-level": "error",
            "bubble": false
          },
          "swift-mailer" : {
            "id" : "import.logger.factory.transport.swift.smtp",
            "params" : {
              "to" : "tw@techdivision.com",
              "from" : "m2if@techdivision.com",
              "subject": "Something Went Wrong",
              "content-type" : "text/plain"
            },
            "transport" : {
              "params" : {
                "smtp-host" : "mail.techdivision.com",
                "smtp-port" : 25
              }
            }
          }
        }
      ]
    }
  }
}
```

This will override the system logger, as the name is `system`, and set the default log level to **debug**.

#### Cache

M2IF uses a cache to avoid excessive database penetration and to pre-load data when the import starts. The cache can be configured in the configuration file as well. M2IF distinguishes two different cache types. The implemented cache with the Symfony DI identifier `cache.static` can **NOT** be disabled and will be used for data that will/can not be updated during the import, e. g. like the pre-loaded EAV attributes for the product import.

In most cases it should not be necessary to change cache configurations or disable it. We provide this functionality to support scenarios where M2IF has to work in a distributed environment and e. g. Redis will be used to share the cached data.

In contrast to that, the cache with the DI identifier `cache.configurable` will be used to cache e. g. products that has been loaded once during the import process. This cache type can be enabled/disabled or a TTL can be set like

```json
{
  ...,
  "caches": [
    {
      "type": "cache.static"
    },
    {
      "type": "cache.configurable",
      "enabled" : true,
      "time": 1440
    }
  ],
  ...,
}
```

> In most cases we do not recommend to change the default cache settings as can have a massive impact on performance on the one hand and a deep understanding of the impact on the main functionality will be necessary to avoid errors and disfunctions. 

#### Aliases

Aliases can be used to override classes with custom functionality, e. g. provided by a project specific library. For example. if a custom adapter for caching should be used, the default class behind the Symfony DI configuration of the `cache.adapter` can be replaced by the customer DI identifier `import_parallel.cache.adapter.redis`.

```json
{
  ...,
  "aliases": [
    {
      "id": "cache.adapter",
      "target": "import_parallel.cache.adapter.redis"
    }
  ],
  ...,  
}
```

> Overriding classes can be dangerous and should only be done when know what you're doing.

#### Operations

A operation reflects an import command like the `delete` operation and combines the necessary functionality as as simple container, that allows to have a custom plugin configuration. Usually, most of the operations are at least build out of the tree plugins

* TechDivision\Import\Plugins\GlobalDataPlugin
* TechDivision\Import\Plugins\SubjectPlugin
* TechDivision\Import\Plugins\ArchivePlugin

Each plugin usually implements a specific functionality like the `GlobalDataPlugin` that pre-loads the global data like attributes or attribute sets. Other plugins like the `SubjectPlugin` are again only a container that can be configured with several subjects, that'll be executed synchronously for each matching file a subject can find.

The example above shows a short excerpt of a complete configuration file and should give a impression how the `delete` operation for products is configured by default.

The `GlobalDataPlugin` loads the global data that'll be needed in other plugins into the memory. This prevents other plugins to load these data again and again and avoids unnecessary database traffic therfore. 

The next plugin, called `SubjectPlugin`, is configured with two subject. The `MoveFilesSubject` simply moves the CSV files into a temporary folder, where the `BunchSubject` starts to import them into the database.

Finally, the `ArchivePlugin` archives the imported files additionally artefacts into a ZIP archive and moves it to the configured archive directory.

```json
{
  "magento-edition": "CE",
  "magento-version": "2.3.1",
  "operation-name" : "replace",
  "installation-dir" : "/var/www/magento",
  "source-dir": "projects/sample-data/tmp",
  "target-dir": "projects/sample-data/tmp",
  "archive-artefacts" : false,
  "archive-dir" : "archive",
  "debug-mode" : false,
  "ignore-pid" : false,
  "pid-filename" : "projects/sample-data/tmp/importer.pid",
  "databases" : [ ... ],
  "operations" : [
    {
      "name" : "delete",
      "plugins" : [
        {
          "id": "import.plugin.global.data"
        },
        {
          "id": "import.plugin.subject",
          "subjects" : [
            {
              "id": "import.subject.move.files",
              "identifier": "move-files",
              "file-resolver": {
                "prefix": "product-import"
              },
              "ok-file-needed": true
            },
            {
              "id": "import_product.subject.bunch",
              "identifier": "files",
              "file-resolver": {
                "prefix": "product-import"
              },
              "observers": [
                {
                  "import": [
                    "import_product.observer.clear.product"
                  ]
                }
              ]
            }
          ]
        },
        {
          "id": "import.plugin.archive"
        }
      ]
    }
  ]
}
```

Most of the available configuration options has to be specified on the subject level, which is nested under the
plugins.

#### Plugins

Plugins are usually used to implement the workflow itself and are not bound to a special context like subjects that are invoked on each matching CSV file, found in the configured source directory. As shown in the example above, the plugins can be used to implement functionality that has to be invoked before or after the data has been imported from the CSV files.

```json
"plugins" : [
  {
    "id" : "import.plugin.subject",
    "subjects" : [ ... ]
  }
]
```

#### Subjects

The `SubjectPlugin` is the plugin that provides the real import functionality. It can be configured with an endless number of subjects which invokes the configured observers on each line of a CSV file, extracts the data and writes it to the database, if needed.

```json
"subjects" : [
  {
    "id" : "import_product.subject.bunch",
    "identifier" : "files",
    "file-resolver": {
        "prefix": "product-import"
    },
    "observers" : [ ... ],
    "callbacks" : [ ... ]
  }
]
```

#### Observers

Observers provides the functionality of a subject **ON ROW LEVEL** and need to be registered for a subject by a adding them to the list of observers like

```json
"observers" : [
  {
    "import" : [
      "import_product.observer.clear.product"
    ]
  }
]
```

#### Callbacks

Callbacks can be used to transform values, found in the CSV file into the necessary types that needs to be stored into the database. For example, the default Magento 2 CSV format allows the values

* `Catalog`
* `Search`
* `Catalog, Search`
* `Not Visible Individually`

for the column `visibility`. These values can not be stored in the appropriate database column, as this expects integer values. Therefore, a callback can be use to transform the string into the correct integer value, in this case the class `TechDivision\\Import\\Product\\Callbacks\\VisibilityCallback`.

By default, the necessary callbacks to transform the Magento 2 standard attributes found in the CSV file are already defined. When a new, user defined attribute will be added, e. g. with a setup script, the M2IF tries to find the best matching callback, depending on the `frontend_input` value of the attribute. Actually M2IF comes whith callbacks for

* `select`
* `multiselect`
* `boolean`

`frontend_input` types. Callbacks for other input types will be part of upcoming versions, but can always be implemented by the developers using M2IF in their project. To register a custom callback, it has to be added to the array with the callbacks of a subject, like

```json
"callbacks": [
  {
    "visibility": [
      "import_product.callback.visibility"
    ]
  }
]
```

> Please be aware, that a custom callback will **REPLACE** the default callback and will **NOT** be appended!

#### Images

When you want to copy images from a source directory to the Magento `pub/media` directory, you can add a snippet, e. g. `<custom-configuration-dir>/operations.json`, that overrides the default operation.

In case images for the products has to copied from directory `var/importexport/media/wysiwyg` to the apropriate target directory `pub/media/catalog/product` the `copy-images` flag has to be set to `true` as well as the values for the `media-directory` (which is the target directory) and the `images-file-directory` (which is the source directory) has to be specified like

```json
{
  "operations": {
    "ce": {
      "catalog_product": {
        "add-update": {
          "plugins": {
            "subject": {
              "id": "import.plugin.subject",
              "subjects": [
                {
                  "id": "import_product.subject.bunch",
                  "file-resolver": {
                    "prefix": "product-import"
                  },
                  "params": {
                    "copy-images": true,
                    "media-directory" : "pub/media/catalog/product",
                    "images-file-directory" : "var/importexport/media/wysiwyg",
                    "clean-up-media-gallery": true,
                    "clean-up-empty-image-columns": true,
                    "clean-up-website-product-relations": true,
                    "clean-up-category-product-relations": true,
                    "clean-up-empty-columns": [
                      "special_price",
                      "special_price_from_date",
                      "special_price_to_date"
                    ]
                  },
                  "observers": [
                    {
                      "import": [
                        "import_product.observer.composite.base.add_update"
                      ]
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

In the CSV file the path to the images has to start with a `/` like `/m/b/mb01-blue-0.jpg`. Have a look at the example files in the repository [techdivision/import-sample-data](https://github.com/techdivision/import-sample-data/blob/4.x/generic/data/products/add-update/product-import_20161021-161909_01.csv);

! ATTENTION: When you copy images, you **MUST** create resized versions of them. Therefore, you can invoke the command `bin/magento catalog:images:resize` on the CLI. If you missed that, the images will be rendered in the admin area but **NOT** on the frontend! Please also keep in mind that it won't usually make sense to copy the images during the import process as this will slow down the performance sigenifcantly. It is strongly recommended to copy the images to the apropriate folder in your Magento installation and only importing the path to the images.

#### Header Mappings

In some cases, it can be very handy to map column names to the appropriate attributes. This is a built-in feature and can simply be configured by adding a snippet, e. g. `<custom-configuration-dir>/header-mappings.json` that contains an array with header mappings like

```json
{
  "header-mappings": {
    "catalog_product": {
      "my_sku_column": "sku",
      "my_qty_column": "qty",
      ...,
    }
  }
}
```

In the example above, column name `my_sku_column` will automatically be mapped to the mandatory column `sku`. This happens **BEFORE** any columns will be processed and allows vendors to configure any column to the appropriate attribute without custom development.

! Keep in mind, that the header mappings are valid for all operations within the configuration file!