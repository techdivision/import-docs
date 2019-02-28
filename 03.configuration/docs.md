---
title: Configuration
visible: true
---

The necessary configuration has to be defined in a simple JSON file. An example that imports the Magento 2 sample data in a Magento 2 CE version 2.1.2 can be found in our [examples](https://github.com/techdivision/import-cli-simple/blob/master/projects/sample-data/ce/2.1.x/conf/products/techdivision-import.json).

If **NO** configuration file (option `--configuration`) has been specified, the default one, defined by the executed command will be used. In case of the `import:products`, the configuration file provided by the library `techdivision/import-product` in the directory `etc/techdivision-import.json` will be used. This default configuration contains **NO** database configuration and **NO** image directory.

The database configuration can be specified by the commandline options, but if images should be imported.

> If images should be imported, a custom configuration file with the paths to the image files has to be specified.

The import command itself supports a argument as well as several options.

### Arguments

The following configuration arguments are available:

| Argument             | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| operation            | Specify the operation name to execute, either one of add-update, replace or delete | n/a |

### Options

The following configuration options are available:

| Option               | Description                                                     | Default value |
|:---------------------|:----------------------------------------------------------------|:--------------|
| --configuration      | Specify the pathname to the configuration file to use | `./vendor/techdivision/import-product/etc/techdivision-import.json` |
| --pid-filename       | The explicit PID filename to use | `<system-temp-dir>/importer.pid` |
| --system-name        | The system name to be used (will added to the mail subject, if mails are configured) | The hostname |
| --installation-dir   | The Magento installation directory to which the files has to be imported | The actual working directory |
| --entity-type-code   | The Magento entity type code, **MUST** be one of `catalog_product` or `catalog_category`  | n/a |
| --source-dir         | The directory that has to be watched for new files | n/a |
| --target-dir         | The target directory with the files that has been imported | n/a |
| --archive-dir        | The directory with the archived files that has been imported | n/a |
| --archive-artefacts  | The flag to activate the artefact archiving functionality | `true` |
| --magento-edition    | The Magento edition to be used, either one of CE or EE | n/a |
| --magento-version    | The Magento version to be used, e. g. 2.1.2 | n/a |
| --source-date-format | The date format used in the CSV file(s) | n/a |
| --use-db-id          | The ID of the database to use, if not specified, the database with the default flag will be used | n/a |
| --db-pdo-dsn         | The DSN used to connect to the Magento database where the data has to be imported, e. g. `mysql:host=127.0.0.1;dbname=magento` | n/a |
| --db-username        | The username used to connect to the Magento database | n/a |
| --db-password        | The password used to connect to the Magento database | n/a |
| --debug-mode         | The flag to activate the debug mode | `false` |
| --log-level          | The log level to use (see Monolog documentation for further information) | `info` |
| --single-transaction | The flag to wrap the import process into a single transaction | `false` |
| --params             | A JSON encoded string that'll be merged with the params from the configuration file (has to be in the same format) | n/a |
| --params-file        | The path to a file with the JSON encoded params that will be merged with the params from the configuration file  (has to be in the same format)| n/a |

Beside the `configuration` option, all options can and **SHOULD** be defined in the configuration file. The commandline options should only be used to override these values in some circumstances.

If the `configuration` option has **NOT** been specified, the system tries to locate the Magento Edition, based on the specified `installation-dir` option. If the `installation-dir` option **IS** specified explictly, and the directory is a valid Magento root directory, the application tries to load database credentials from the `app/etc/env.php` script, so it is **NOT** necessary to specify a database configuration, nor in the configuration file or as commandline parameter.

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

### Configuration File

The configuration file **MUST** be in JSON format. Beside itself, all necessary configuration options/arguments that can be passed on on the commandline, can and **SHOULD** be defined in the configuration file, instead.

The structure is separated into a general configuration section, the database configuration and the configuration for the available operations.

```json
{
  "magento-edition": "CE",
  "magento-version": "2.1.2",
  "operation-name" : "replace",
  "installation-dir" : "/var/www/magento",
  "databases" : [ ... ],
  "loggers" : [ ... ],
  "operations" : { ... }
}
```

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

Beside Plugins, Subjects, Observers and Callbacks, specific events are triggered. On each event,  

The following events are available

| Event Name               | Description                                                     |
|:---------------------|:----------------------------------------------------------------|:--------------|
| app.process.transaction.start      | Is triggered before the application start's the transaction (if single transaction mode has been activated). |
| app.process.transaction.success      | Is triggered after the application has the transaction committed successfully (if single transaction mode has been activated). |
| app.process.transaction.failure      | Is triggered after the application rollbacked the transaction (if single transaction mode has been activated). |
| subject.artefact.process.start      | Is triggered before an import artefact will be processed. |
| subject.artefact.process.success      | Is triggered when an import artefact has successfully been processed. |
| subject.artefact.process.failure      | Is triggered when an import artefact can not be processed. |
| subject.artefact.row.process.start      | Is triggered when an import artefact has successfully been processed. |
| subject.artefact.row.process.success      | Is triggered when an import artefact has successfully been processed. |

This register's the class loader of specified vendor directory and parse's the libraries for the necessary DI configuration files.

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
  "magento-version": "2.1.2",
  "operation-name" : "replace",
  "installation-dir" : "/var/www/magento",
  "source-dir": "projects/sample-data/tmp",
  "target-dir": "projects/sample-data/tmp",
  "source-date-format": "n/d/y, g:i A",
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
              "prefix": "magento-import",
              "ok-file-needed": true
            },
            {
              "id": "import_product.subject.bunch",
              "identifier": "files",
              "prefix": "magento-import",
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
    "prefix" : "magento-import",
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