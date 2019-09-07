---
title: Configuration
visible: true
---

The necessary configuration has to be defined in a simple JSON file. Examples that imports the Magento 2 sample data into several Magento 2 CE versions can be found in our [examples](https://github.com/techdivision/import-sample-data).

If **NO** configuration file (option `--configuration`) has been specified, the default one, defined by the executed command will be used. In case of the `import:products`, the configuration file provided by the library `techdivision/import-product` in the directory `etc/techdivision-import.json` will be used. This default configuration contains **NO** database configuration and **NO** image directory.

The database configuration can be specified by the commandline options, but if images should be imported.

> If images should be imported, a custom configuration file with the paths to the image files has to be specified.

### Configuration File

The configuration file **MUST** be in JSON format. Beside itself, all necessary configuration options/arguments that can be passed on on the commandline, can and **SHOULD** be defined in the configuration file, instead.

The structure is separated into a general configuration section, the database configuration and the configuration for the available operations.

```json
{
  "magento-edition": "CE",
  "magento-version": "2.3.1",
  "operation-name" : "replace",
  "installation-dir" : "/var/www/magento",
  "databases" : [ ... ],
  "loggers" : [ ... ],
  "operations" : { ... }
}
```

#### Global Parameters

Global parameters in the configuration file enables developers to pass specific configuration values from the configuration file itself, from the commandline (using the --params option) or an addtional file (using the --params-file option) through to their import logic, e. g. project specifc observers.

##### In the Configuration File itself

```json
{
  "magento-edition": "CE",
  "magento-version": "2.3.1",
  "operation-name" : "replace",
  "installation-dir" : "/var/www/magento",
  "params": [ 
    { 
      "my-website-country-mapping": { 
        "DE": [ "de_DE", "de_AT", "de_CH" ], 
        "EN": [ "en_US", "en_UK" ] 
      } 
    } 
  ],
  "databases" : [ ... ],
  "loggers" : [ ... ],
  "operations" : { ... }
}
```

These params can be used wherever access to the configuration object is available, e. g. in an observer like

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
     --configuration=projects/sample-data/ce/2.3.x/conf/products/techdivision-import.json \
     --params='{ "params": [ { "my-website-country-mapping": { "DE": [ "de_LI" ] } } ] }'
```

which will append the value `de_LI` to the `DE` param of the `my-website-country-mapping`.

##### As file, defined as Commandline Option

Beside the possibility to specify the params directly as commandline option, it is also possible to specify a path to a file that contains the JSON encoded params. The file **MUST** have the following format

```json
{ 
  "params": [ 
    { 
      "my-website-country-mapping": { 
        "DE": [ "de_LI" ]
      } 
    } 
  ] 
}
```
> Please be aware, that the values from the configuration file will be overwritten with the values from the commandline which again will be overwritten with the values from an addtional file that has been specified with the `--params-file` option.

#### Extend M2IF with additional libraries

In more complex projects, it'll we possible, that addional libraries are necessary. As the M2IF - Simple Console Tool uses a Symfony DI container, it is necessary to register the additional library by adding it to the configuration file. Depending on how the M2IF - Simple Console Tool has been installed, there a two options.

> Whenever you write an extension library do NOT forget to provide the Symfony DI configuration.

##### Extension Libraries

Assuming, that the M2IF - Simple Console Tool has been installed as Composer library, together with a Magento 2 installation, the simplest way to register an additional extension will be adding it as a extension library like

```json
"extension-libraries" : [
  "techdivision/import-product-magic360"
]
```

> This is only possible, if the additional library uses the same Composer autoloader as M2IF - Simple Console Tool does.

##### Additional Vendor Directories

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

#### Events

Beside the configured worklfow with Plugins, Subjects, Observers and Callbacks, addtional events are available to add custom functionality.

| Event Name                           | Description                                                                                                                    | Since Version |
|:-------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------|:--------------|
| app.set.up                           | Is triggered before the import will be processed.                                                                              |               |
| app.tear.down                        | Is triggered after the import has been processed.                                                                              |               |
| app.process.transaction.start        | Is triggered before the application start's the transaction (if single transaction mode has been activated).                   |               |
| app.process.transaction.success      | Is triggered after the application has the transaction committed successfully (if single transaction mode has been activated). |               |
| app.process.transaction.failure      | Is triggered after the application rollbacked the transaction (if single transaction mode has been activated).                 |               |
| subject.artefact.process.start       | Is triggered before an import artefact will be processed.                                                                      |               |
| subject.artefact.process.success     | Is triggered when an import artefact has successfully been processed.                                                          |               |
| subject.artefact.process.failure     | Is triggered when an import artefact can not be processed.                                                                     |               |
| subject.artefact.row.process.start   | Is triggered when an import artefact has successfully been processed.                                                          |               |
| subject.artefact.row.process.success | Is triggered when an import artefact has successfully been processed.                                                          |               |
| plugin.process.start                 | Is triggered before a subject's `import()` method will be processed.                                                           |         3.4.0 |
| plugin.process.success               | Is triggered after a subject's `import()` method has been processed.                                                           |         3.4.0 |
| plugin.process.failure               | Is triggered when an exception has been thrown during the subject's `import()` is processed.                                   |         3.4.0 |
| subject.import.start                 | Is triggered before a subject's `import()` method will be processed.                                                           |         3.4.0 |
| subject.import.success               | Is triggered after a subject's `import()` method has been processed.                                                           |         3.4.0 |
| subject.import.failure               | Is triggered when an exception has been thrown during the subject's `import()` is processed.                                   |         3.4.0 |
| subject.export.start                 | Is triggered before a subject's `export()` method will be processed.                                                           |         3.4.0 |
| subject.export.success               | Is triggered after a subject's `export()` method has been processed.                                                           |         3.4.0 |
| subject.export.failure               | Is triggered when an exception has been thrown during the subject's `export()` is processed.                                   |         3.4.0 |

Beside to possiblity to register global events, up with version 3.4.0 it is possible to register events on plugin and subject level. This avoids execution of events that only provide functionality for a dedicated plugin or subject, so keep in mind, that they will **NOT** be fired for every plugin or every subject.

##### Plug-In Level

The listeners will only be executed before, after or on failure of the plugin, for which the event has been  configured for. Configuration will look like

```json
{
  ...
  "operations" : [
    {
      "name" : "add-update",
      "plugins" : [
        {
          "id": "import.plugin.cache.warmer"
        },
        {
          "id": "import.plugin.global.data"
        },
        {
          "id": "import.plugin.subject",
          "listeners" : [
             {
              "plugin.process.success" : [
                "import_product_tier_price.listener.delete.obsolete.tier_prices"
              ]
            }
          ]
          ...
        }
      ]
    }
    ...
  ]
}
```

##### Subject Level

As subjects are responsible for importing **AND** exporting artefacts, events for both steps has been added.

The listeners will only be executed before, after or on failure of the subject, for which the event has been configured for. Configuration will look like

```json
{
  ...
  "operations" : [
    {
      "name" : "add-update",
      "plugins" : [
        {
          "id": "import.plugin.cache.warmer"
        },
        {
          "id": "import.plugin.global.data"
        },
        {
          "id": "import.plugin.subject",
          "listeners" : [
             {
              "plugin.process.success" : [
                "import_product_tier_price.listener.delete.obsolete.tier_prices"
              ]
            }
          ]
          "subjects": [
            ...
            {
              "id": "import_product_tier_price.subject.tier_price",
              "identifier": "files",
              "listeners": [
                 {
                  "subject.import.success" : [
                    "import_product.listener.register.sku.to.pk.mapping"
                  ]
                }
              ],
              "file-resolver": {
                "prefix": "product-import-tier-price"
              },
              "observers": [
                {
                  "import": [
                    "import_product_tier_price.observer.tier_price.update"
                  ]
                }
              ]
            },
          },
          ...
        ]
      ]
    }
    ...
  ]
}
```

#### Default Listeners

By default, M2IF comes with 3 listeners registered. 

```json
{
  "magento-edition": "CE",
  "magento-version": "2.3.0",
  "operation-name" : "add-update",
  "archive-artefacts" : true,
  "debug-mode" : false,
  "source-dir" : "projects/sample-data/tmp",
  "target-dir" : "projects/sample-data/tmp",
  "entity-type-code" : "catalog_product",
  "listeners" : [
    {
      "app.set.up" : [
        "import.listener.render.ansi.art",
        "import.listener.initialize.registry"
      ]
    },
    {
      "app.tear.down" : [
        "import.listener.clear.registry"
      ]
    }
  ],
  ...
}
```

The first one named `import.listener.render.ansi.art` is renders the nice ASCII art when invoking the CLI. The second and the third one are mandatory as they are responsible to initialize the registry of the actual import.

> As solution partner, feel free to replace the ASCII art renderer with one of your choice, e. g. rendering the logo of your company.

#### Database

The configuration allows the registration of multiple databases like

```json
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
```

Depending whether the commandline option `--use-db-id` and the specified value, the database with the given ID will be used. If the commandline options is **NOT** specified, the one with the  flag `"default": true` will be used, if not found, the first configured database will be use.

If a value for the commandline option `--db-pdo-dsn` has been specified, the `--use-db-id` option will be ignored and the given DSN value will be used for database connection instead. Additionally the credentials, by using the `--db-username` and `--db-password` options also needs to be specified.

#### Loggers

M2IF uses [Monolog](https://github.com/Seldaek/monolog) to provide the basic logging functionality. Therefore, at least one logger instance is necessary. By default, if no logger has been configured, a system logger will be instanciated, that writes log messages to the error log that has been configured in the `php.ini` file of the used PHP installation.

To add additional loggers, or override the default one with name `system, the configuration file can be extended like

```json
"loggers": [
  {
    "name": "system",
    "channel-name": "logger/system",
    "type": "Monolog\\Logger",
    "handlers": [
      {
        "type": "Monolog\\Handler\\ErrorLogHandler",
        "params" : [
          {
            "message-type" : 0,
            "log-level" : "debug"
          }
        ]
      }
    ]
  },
  {
    "name": "mail",
    "channel-name" : "logger/mail",
    "type" : "Monolog\\Logger",
    "handlers" : [
      {
        "type": "Monolog\\Handler\\SwiftMailerHandler",
        "params" : [
          {
            "log-level" : "error",
            "bubble" : false
          }
        ],
        "swift-mailer" : {
          "factory" : "TechDivision\\Import\\Utils\\SwiftMailer\\SmtpTransportMailerFactory",
          "mailer-factory" : "\\Swift_Mailer",
          "params" : [
            {
              "to" : "info@my-domain.tld",
              "from" : "info@my-domain.tld",
              "subject": "Something Went Wrong",
              "content-type" : "text/plain"
            }
          ],
          "transport" : {
            "transport-factory" : "\\Swift_SmtpTransport",
            "params" : [
              {
                "smtp-host" : "my-domain.tld",
                "smtp-port" : 25,
                "smtp-security" : "tls",
                "smtp-auth-mode" : "LOGIN",
                "smtp-username" : "your-username",
                "smtp-password" : "your-password"
              }
            ]
          }
        }
      }
    ]
  }
]
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

When you want to copy images from a source directory to the Magento `pub/media` directory, additional parameters have to be added to the subjects configuration.

In case images for categories has to copied from directory `var/importexport/media/wysiwyg` to the apropriate target directory `pub/media/catalog/category` the `copy-images` flag has to be set to `true` as well as the values for the `media-directory` (which is the target directory) and the `images-file-directory` (which is the source directory) has to be specified like

```json
"subjects" : [
   {
     "id": "import_category_ee.subject.bunch",
     "identifier": "files",
     "file-resolver": {
       "prefix": "category-import"
     },
     "params" : [
       {
         "copy-images" : true,
         "media-directory" : "pub/media/catalog/category",
         "images-file-directory" : "var/importexport/media/wysiwyg"
       }
     ],
     "observers": [
       {
         "import": [
           "import_category_ee.observer.composite.add_update"
          ]
        }
     ]
   }
]
```

In the CSV file the path to the images has to start with a `/` like `/womens/womens-main.jpg`. Have a look at the example files in the repository [techdivision/import-sample-data](https://github.com/techdivision/import-sample-data/blob/master/generic/data/categories/add-update/category-import_20161024-194026_01.csv);

> ATTENTION: Keep in mind that it won't usually make sense to copy the images during the import process as this will slow down the performance sigenifcantly. It is strongly recommended to copy the images to the apropriate folder in your Magento installation and only importing the path to the images.

#### Header Mappings

In some cases, it can be very handy to map column names to the appropriate attributes. This is a built-in feature and can simply be configured by adding an array with header mappings like

```json
{
  "header-mappings" : [
    {
      "my_sku_column": "sku",
      "my_qty_column": "qty",
      ...,
    }
  ]
}
```

In the example above, column name `my_sku_column` will automatically be mapped to the mandatory column `sku`. This happens **BEFORE** any columns will be processed and allows vendors to configure any column to the appropriate attribute without custom development.

> Keep in mind, that the header mappings are valid for all operations within the configuration file!

### Product Link Positions (CE)

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

> Make sure, that the values are **NOT** already available, before adding them!

### Dynamic Option Creation

Up from version 2.2.x + 2.3.x it is possible to create missing product option values for the `admin` store on the fly. This means that it is **NOT** possible to create translations for the other stores other than the values for the `admin` store. This is, because when creating option values dynamically, the option values taken from the column `additional_attributes` always has to be the values for the `admin` store. Translations can be done in the Magento backend or with the [attribute import](/file-structure/attributes).

To enable this, additional frontend input callbacks have to be registered in the configuration file, for the `add-update` as well as the `replace` operations. The subject configuration should look like this

```json
"subjects" : [
  {
    "id" : "import_product.subject.bunch",
    "identifier" : "files",
    "file-resolver": {
      "prefix": "product-import"
    },
    "observers" : [ ... ],
    "callbacks" : [ ... ],
    "params" : [ ... ],
    "frontend-input-callbacks": [
      {
        "select": [
          "import_attribute.callback.create.select.option.value",
          "import_product.callback.select"
        ],
        "multiselect": [
          "import_attribute.callback.create.multiselect.option.value",
          "import_product.callback.multiselect"
        ]
      }
    ]
  }
]
```

### Delta Import

Beside the full import functionality, M2IF also supports delta import functionality. The delta import supports SKUs for

* all link types (up-sell, cross-sell + related)
* variant products
* grouped products
* bundle products

that are **NOT** part of one of the actual CSV files.

In contrast to a full import, which requires the following invocation of the appropriate indexers, after a delta import the [delta indexing](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/indexing.html#m2devgde-mview) functionality takes care for the necessary index updates. The delta indexer can not be started on the commandline, instead it'll be started by the default Magento CRON job.