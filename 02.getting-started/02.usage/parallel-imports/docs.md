---
title: 'Parallel Imports'
visible: true
---

To avoid unwanted behaviour, only one import process can be started at a time. To make sure, that only one process is running, a PID file in the system's temporary directory (`sys_get_temp_dir()`) is created which contains the UUID of the actual import process. After the import process has been finished, the file will be deleted and a new process can be started.