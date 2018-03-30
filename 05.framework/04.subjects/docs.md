---
title: Subjects
visible: true
---

Assuming that you don't need to implement a totally new functionality where a plug-in makes sense, e. g. your want to invoke a method of the Magento RESTFul API after the product import has been finished, in most cases a combination of one or more subject(s) with some observers can fully cover the requirements of a new use case. In this chapter, we'll discuss when it makes sense to implement a subject and how it could be done.

### When do i need a subject?

The `TechDivision\Import\Plugins\SubjectPlugin` implements the observer pattern and is the first choice for most use cases. This plug-ins allows you to register an unlimited number of subjects whereas each of them can has an unlimited number of observers. When a new file that has to be imported is found, the `TechDivision\Import\Plugins\SubjectPlugin` invokes the `process()` method of **ALL** registered subjects on this file in the order they have been registered. The subject itself processes the content of the file by invoking **ALL** registered observers for **EACH** row of the given file. You can imagine this as something like a chain that can be confiugred by a workflow engine. This approach allows you to process nearly every type of file by reading the file contents row by row.

So, usually you need to implement a subject when

* You want to process an import file, independent which format it has
* You want to share data between observers or pass the data to the following subjects
* You need to customize the file parsing, e. g. not parsing a single row, but a bunch of rows

> Generally you need an observer, when you want to do something with a file.

### How to implement a subject

In general, you should consider to extend `TechDivision\Import\Subjects\AbstractSubject` or one of it's subclasses, e. g. `TechDivision\Import\Subjects\AbstractEavSubject` if you want to implement to implement the import for another EAV entity. At least, you need to implement the interface `TechDivision\Import\Subjects\SubjectInterface` which is the minimum requirement for a subject implementation. Let's implement a common requirement, where you need a subject that allows one of it's observers to load a product with the `SKU` found in the import file, adding the `SKU` to `entity_id` mapping to the subject and finally pass the mappings to the next subject.

```php

namespace TechDivision\Import\Product\Subjects;

use TechDivision\Import\Utils\RegistryKeys;
use TechDivision\Import\Subjects\AbstractSubject;

class MySubject extends AbstractSubject
{
    
    /**
     * The SKU to entity_id mappings we want to pass to the next subject.
     * 
     * @var array
     */
    protedted $skuEntityIdMapping = array();

    /**
     * Intializes the previously loaded global data for exactly one bunch.
     *
     * @param string $serial The serial of the actual import
     *
     * @return void
     */
    public function setUp($serial)
    {

        // load the status of the actual import
        $status = $this->getRegistryProcessor()->getAttribute($serial);

        // load the SKU => entity_id mappings from further subjects
        $this->skuEntityIdMapping = $status[RegistryKeys::GLOBAL_DATA][RegistryKeys::SKU_ENTITY_ID_MAPPING];

        // invoke the parent method
        parent::setUp($serial);
    }

    /**
     * Clean up the global data after importing the bunch.
     *
     * @param string $serial The serial of the actual import
     *
     * @return void
     */
    public function tearDown($serial)
    {

        // load the registry processor and add the SKU => entity_id mappings
        $this->getRegistryProcessor()->mergeAttributesRecursive(
            $serial,
            array(
                RegistryKeys::SKU_ENTITY_ID_MAPPING => $this->skuEntityIdMapping
            )
        );

        // invoke the parent method
        parent::tearDown($serial);
    }
    
    /**
     * Add the passed SKU => entity ID mapping.
     *
     * @param string  $sku      The SKU to map
     * @param integer $entityId The entity ID to be mapped
     *
     * @return void
     */
    public function addSkuEntityIdMapping($sku, $entityId)
    {
        $this->skuEntityIdMapping[$sku] = $entityId;
    }
}
```

The subject provides the `addSkuEntityIdMappping()` method, that'll be invoked by the observer with the ID `import_product.observer.my.observer` that loads the product by the SKU found in the import file. Additional, it implements the methods `setUp()` and `tearDown()`, that'll be invoked automatically before and after the import file has been processed. These methods allows us, to load/add data from/to the `TechDivision\Import\Services\RegistryProcessor` which acts as a data container for the whole import process, and therefore passing it from one subject to next.

To make your subject accessible for the Workflow Engine, you first have to define it in the Symfony DI configuration file`symfony/Resources/config/services.xml` of your component. Depending on your namespace, this would look like

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container xmlns="http://symfony.com/schema/dic/services"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">
    <services>
        <service id="import_product.subject.my" class="TechDivision\Import\Product\Subjects\MySubject" shared="false">
            <argument type="service" id="import.processor.registry"/>
            <argument type="service" id="import.generator.core.config.data.uid"/>
            <argument type="service" id="loggers"/>
            <argument type="service" id="import.events.emitter"/>
        </service>
        <service id="import_product.observer.my" class="TechDivision\Import\Product\Observers\MyObserver">
            <argument type="service" id="import_product.processor.product.bunch"/>
        </service>
    </services>
</container>
```

The subject from above can finally be added to the Workflow Engine with the following configuration, whereas we'll implement the observer with the ID `import_product.observer.my.observer` in the next chapter 

```json
{
  "id": "import.plugin.subject",
  "subjects": [
    {
      "id": "import_product.subject.my",
      "identifier": "files",
      "prefix": "product-import",
      "observers": [
        "import_product.observer.my"
      ]
    }
  ]
}
```
