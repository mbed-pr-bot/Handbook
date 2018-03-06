<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## MemoryPool

You can use the MemoryPool class to define and manage fixed-size memory pools.

### MemoryPool class reference

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/classrtos_1_1_memory_pool.html)

### MemoryPool example

```
MemoryPool<message_t, 16> mpool;

message_t *message = mpool.alloc();

mpool.free(message);
```

### Queue and MemoryPool example

This example shows <a href="https://os.mbed.com/docs/v5.6/reference/queue.html" target="_blank">Queue</a> and MemoryPool managing measurements.

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/rtos_queue/)](https://os.mbed.com/teams/mbed_example/code/rtos_queue/file/0cb43a362538/main.cpp)

### Related content

- <a href="https://os.mbed.com/docs/v5.6/reference/queue.html" target="_blank">Queue</a> API reference.
