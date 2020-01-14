---
title: Operations
taxonomy:
    category:
        - docs
visible: true
---

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

Most of the available configuration options has to be specified on the subject level, which is nested under the plugins.