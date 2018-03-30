---
title: Observers
visible: true
---

In the chapter [Subjects][#subjects] we've discussed when a subject is needed and how it can be implemented.

### When do i need an observer?

As a subject itself usually will **NOT** implement the main import business logic, this will be the responsibility of the observers. So, usually you need to implement an observer when

* You want to load data from the database based on values you found in the import file
* You want to load data to make it available for the following observers or subjects
* You want to persist data assembled from values you found in a row of an import file

> Generally you need an observer, when you want to do something with a **ROW**.

### How to implement an observer


The `TechDivision\Import\Observers\AbstractObserver` will be a perfect choice to be extended by our first observer implementation. At least an observer has to implement the interface `TechDivision\Import\Observers\ObserverInterface`.
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

The subject provides the `addSkuEntityIdMappping()` method, that'll be invoked by the observer with the ID `import_product.observer.my.observer` that loads the product by the SKU found in the import file. Additional, it implements the methods `setUp()` and `tearDown()`, that'll be invoked automatically before and after the import file has been processed. These methods allows us, to load/add data from/to the `TechDivision\Import\Services\RegistryProcessor` which acts as a data container for the whole import process, and therefore passing it from one subject to next.

By register it in the Symfony DI configuration and the Workflow Engine in the chapter before, the observer will already be ready to use.