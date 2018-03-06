<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## Event

The event loop is a mechanism that you can use to defer the execution of code to a different context. A common use of an event loop is to postpone the execution of a code sequence from an interrupt handler to a user context.

### Event class reference

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/classevents_1_1_event_queue.html)

### Event example: deferring from interrupt context

The code executes two handler functions (`rise_handler` and `fall_handler`) in two different contexts:

1. In interrupt context when a rising edge is detected on `SW2` (`rise_handler`).
2. In the context of the event loop's thread function when a falling edge is detected on `SW2` (`fall_handler`). `queue.event()` is called with `fall_handler` as an argument to specify that `fall_handler` will run in user context instead of interrupt context.

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/events_ex_1/)](https://os.mbed.com/teams/mbed_example/code/events_ex_1/file/6ae734681f16/main.cpp)

### Event example: posting events to the queue

The code below demonstrates queueing functions to be called after a delay and queueing functions to be called periodically.

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/events_ex_2/)](https://os.mbed.com/teams/mbed_example/code/events_ex_2/file/488fe91e2e80/main.cpp)

### Event example: chaining events from more than one queue

Event queues easily align with module boundaries, where internal state can be implicitly synchronized through event dispatch. Multiple modules can use independent event queues, but still be composed through the `EventQueue::chain` function.

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/events_ex_3/)](https://os.mbed.com/teams/mbed_example/code/events_ex_3/file/fca134a32b61/main.cpp)
