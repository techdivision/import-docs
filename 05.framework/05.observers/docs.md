---
title: Observers
visible: true
---

In the previous chapter [Subjects][#subjects] we've discussed when a subject is needed and how it can be implemented.

### When do i need an observer?

As a subject itself usually will **NOT** implement the main import business logic, this will be the responsibility of the observers. So, usually you need to implement an observer when

* You want to load data from the database based on values you found in the import file
* You want to load data to make it available for the following observers or subjects
* You want to persist data assembled from values you found in a row of an import file

> Generally you need an observer, when you want to do something with a **ROW**.

### How to implement an observer

The `TechDivision\Import\Observers\AbstractObserver` will be a perfect choice to be extended by our first observer implementation. At least an observer has to implement the interface `TechDivision\Import\Observers\ObserverInterface`. Our example observer simply tries to load the product with the SKU found in the actual row and adds the `SKU` to `entity_id` mapping to the subject.

```php

namespace TechDivision\Import\Product\Observers;

use TechDivision\Import\Utils\RegistryKeys;
use TechDivision\Import\Observers\AbstractObserver;

class MyObserver extends AbstractObserver
{

    /**
     * The product bunch processor instance.
     *
     * @var \TechDivision\Import\Product\Services\ProductBunchProcessorInterface
     */
    protected $processor;

    /**
     * Initialize the observer with the passed product bunch processor instance.
     *
     * @param \TechDivision\Import\Product\Services\ProductBunchProcessorInterface $productBunchProcessor The product bunch processor instance
     */
    public function __construct(ProductBunchProcessorInterface $processor)
    {
        $this->processor = $processor;
    }

    /**
     * Process the observer's business logic.
     *
     * @return array The processed row
     * @throws \Exception Is thrown, if the product with the SKU can not be loaded
     */
    protected function process()
    {

        // try to load the product with the SKU of the actual row and store the entity ID => SKU mapping in the subject
        if ($product = $this->processor->loadProduct($this->getValue(ColumnKeys::SKU)) {
            $this->subject->addSkuEntityIdMapping($product[MemberNames::ENTITY_ID], $product[MemberNames::SKU]);
        } else {
         	throw new \Exception(sprintf('Can\'t load product with SKU "%s"', $sku));   
        }
    }
}
```