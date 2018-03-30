---
title: Subjects
visible: true
---

Assuming that you don't need to implement a totally new functionality where a plug-in makes sense, e. g. your want to invoke a method of the Magento RESTFul API after the product import has been finished, in most cases a combination of one or more subject(s) with some observers can fully cover the requirements of a new use case. In this chapter, we'll discuss when it makes sense to implement a subject and how it could be done.

### When do i need a subject?

The `TechDivision\Import\Plugins\SubjectPlugin` implements the observer pattern and is the first choice for most use cases. This plug-ins allows you to register an unlimited number of subjects whereas each of them can has an unlimited number of observers. When a new file that has to be imported is found, the `TechDivision\Import\Plugins\SubjectPlugin` invokes the `process()` method of **ALL** registered subjects on this file in the order they have been registered. The subject itself processes the content of the file by invoking **ALL** registered observers for **EACH** row of the given file. You can imagine this as something like a chain that can be confiugred by a workflow engine. This approach allows you to process nearly every type of file by reading the file contents row by row.

So, you'll need to implement a subject when

* You want to process an import file, independent which format it has
* You want to share data between observers or pass the data to the following subjects
* You need to customize the file parsing, e. g. not parsing a single row, but a bunch of rows

> Generally you need an observer, when you want to do something with a file.

### How to implement a subject

In general, you should consider to extend `TechDivision\Import\Subjects\AbstractSubject` or one of it's subclasses, e. g. `TechDivision\Import\Subjects\AbstractEavSubject` if you want to implement to implement the import for another EAV entity. At least, you need to implement the interface `TechDivision\Import\Subjects\SubjectInterface` which is the minimum requirement for a subject implementation. Let's implement a common requirement, where you need a subject that allows one of it's observers to load a product with the `SKU` found in the import file, adding the `SKU` to `entity_id` mapping to the subject and finally pass the mappings to the next subject.

```php

namespace TechDivision\Import\Subjects\MySubject;

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
     * Clean up the global data after importing the bunch.
     *
     * @param string $serial The serial of the actual import
     *
     * @return void
     */
    public function tearDown($serial)
    {

        // load the registry processor and update the status
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
     * @param string $sku The SKU
     *
     * @return void
     */
    public function addSkuEntityIdMapping($sku)
    {
        $this->skuEntityIdMapping[$sku] = $this->getLastEntityId();
    }
}
```

The subject provides the `addSkuEntityIdMappping()` method, that'll be invoked by the observer, that loads the product by the SKU found in the import file. Additional, it implements the `tearDown()` method, that'll be invoked automatically after the import file has been processed. This method allows us, to add data to the `TechDivision\Import\Services\RegistryProcessor`, which acts as a data container for the whole import process. 

```