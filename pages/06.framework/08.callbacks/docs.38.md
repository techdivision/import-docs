---
title: Callbacks
taxonomy:
    category:
        - docs
visible: true
---

### When do i need a callback?

Callbacks can be used to transform values, found in the CSV file into the necessary types that needs to be stored into the database. For example, the default Magento 2 CSV format allows the values

* `Catalog`
* `Search`
* `Catalog, Search`
* `Not Visible Individually`

for the column `visibility`. These values can not be stored in the appropriate database column, as this expects integer values. Therefore, a callback can be use to transform the string into the correct integer value, in this case the class `TechDivision\\Import\\Product\\Callbacks\\VisibilityCallback`.

By default, the necessary callbacks to transform the Magento 2 standard attributes found in the CSV file are already defined. When a new, user defined attribute will be added, e. g. with a setup script, the Pacemaker Community tries to find the best matching callback, depending on the `frontend_input` value of the attribute. Actually Pacemaker Community comes whith callbacks for

* `select`
* `multiselect`
* `boolean`

`frontend_input` types. Callbacks for other input types will be part of upcoming versions, but can always be implemented by the developers using Pacemaker Community in their project.

!!!! In general, you need a callback, if you want to do something with the **VALUE** on as specific column in each row of a CSV file. Please be aware, that a custom callback will **REPLACE** the default callback and will **NOT** be appended!

### How to implement a callback?

To implement a callback, you can extend the abstract class `TechDivision\Import\Callbacks\AbstractCallback`. The callback's `handle()` method expects the invoking observer as parameter which provides the method `getAttributeValue` that you access to the value that you want to process. The callback **MUST** return the processed value to allow the following callbacks to also process it.

```php
namespace TechDivision\Import\Product\Callbacks;

use TechDivision\Import\Observers\AttributeCodeAndValueAwareObserverInterface;

/**
 * A callback implementation that converts the passed visibility.
 *
 * @author    Tim Wagner <t.wagner@techdivision.com>
 * @copyright 2016 TechDivision GmbH <info@techdivision.com>
 * @license   http://opensource.org/licenses/osl-3.0.php Open Software License (OSL 3.0)
 * @link      https://github.com/techdivision/import-product
 * @link      http://www.techdivision.com
 */
class VisibilityCallback extends AbstractCallback
{

    /**
     * Will be invoked by a observer it has been registered for.
     *
     * @param \TechDivision\Import\Observers\AttributeCodeAndValueAwareObserverInterface|null $observer The observer
     *
     * @return mixed The modified value
     */
    public function handle(AttributeCodeAndValueAwareObserverInterface $observer = null)
    {

        // set the observer
        $this->setObserver($observer);

        // replace the passed attribute value into the visibility ID
        return $this->getSubject()->getVisibilityIdByValue($observer->getAttributeValue());
    }
}
```

To register a callback, it has to be added to the array with the callbacks of a subject, like

```json
"callbacks": [
  {
    "visibility": [
      "import_product.callback.visibility"
    ]
  }
]
```

Whereas the callback key, in our case`visibility`, is the column name the callback has to be invoked. Therefore, the callback will be invoked on every column `visibility` for each row of the processed CSV file. 