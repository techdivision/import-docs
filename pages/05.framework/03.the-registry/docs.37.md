---
title: 'The Registry'
visible: true
---

As Pacemaker Community is build to run in single and multithreaded/multiprocess environments, it needs a possibility to share the data between the components in single process, as well as between threads and/or processes. Whenever data has to be shared, the registry will be the right place. The default implementation only works in a single threaded environment, where it does not need to take care of concurrency issues that will occur in multithreaded or multriprocess ones. 

### Further Thoughts regard Multithreading and/or Multiprocessing

As the registry implementation that comes with the core components only works in a single process, which probably be the choice in mose use cases. Depending on the environment Pacemaker Community will used, it'll be necessary to implement a registry processor implementation that fits the needs of these environment. To give you an example what kind of changes will be necessary in a multithreaded environment, e. g. like [appserver.io](https://appserver.io) we'll implement the method  `mergeAttributesRecursive()`. A threadsave version of this method would look like this

```php

namespace TechDivision\Import\Services;

class RegistryProcessor implements RegistryProcessorInterface
{
 
    // other methods still go here
         
    /**
     * This method merges the passed attributes with an array that
     * has already been added under the passed key.
     *
     * If no value will be found under the passed key, the attributes
     * will simply be registered.
     *
     * @param mixed $key        The key of the attributes that has to be merged with the passed ones
     * @param array $attributes The attributes that has to be merged with the exising ones
     *
     * @return void
     * @throws \Exception Is thrown, if the already registered value is no array
     * @link http://php.net/array_replace_recursive
     */
    public function mergeAttributesRecursive($key, array $attributes)
    {

        // merge the passed attributes in a thread-safe manner
        return $this->synchronized(function ($k, $a) {
            // if the key not exists, simply add the new attributes
            if (!isset($this->attributes[$k])) {
                $this->attributes[$k] = $a;
                return;
            }
            // if the key exists and the value is an array, merge it with the passed array
            if (isset($this->attributes[$k]) && is_array($this->attributes[$k])) {
                $this->attributes[$k] = array_replace_recursive($this->attributes[$k], $a);
                return;
            }
        }, $key, $attributes);

        // throw an exception if the key exists, but the found value is not of type array
        throw new \Exception(sprintf('Can\'t merge attributes, because value for key %s already exists, but is not of type array', $key));
    }   
}
```