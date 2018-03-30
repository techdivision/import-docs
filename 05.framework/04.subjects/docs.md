---
title: Subjects
visible: true
---

Assuming that you don't need to implement a totally new functionality where a plug-in makes sense, e. g. your want to invoke a method of the Magento RESTFul API after the product import has been finished, in most cases a combination of one or more subject(s) with some observers can fully cover the requirements of a new use case. In this chapter, we'll discuss when it makes sense to implement a subject and how it could be done.

### When do i need a subject?

The `TechDivision\Import\Plugins\SubjectPlugin` implements the observer pattern and is the first choice for most use cases. This plug-ins allows you to register an unlimited number of subjects whereas each of them can has an unlimited number of observers. When a new file that has to be imported is found, the `TechDivision\Import\Plugins\SubjectPlugin` invokes the `process()` method of **ALL** registered subjects on this file in the order they have been registered. The subject itself processes the content of the file by invoking **ALL** registered observers for **EACH** row of the given file. You can imagine this as something like a chain that can be confiugred by a workflow engine. This approach allows you to process nearly every type of file by reading the file contents row by row.

So, you'll need to implement a subject

* Whenever you want to process an import file, independent which format it has
* You want to share data between observers