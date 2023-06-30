---
title: Wielding Concurrency - Binary Semaphore vs Mutual Exclusion
type: page
description: Leveraging Concurrency for effective FreeRTOS operation
topic: career
---

### BalanceBug

The objective of our Year 2 Project at Imperial College London was to design and put together a self-balancing maze solver in one month. The microcontroller that we were provided with to build our rover was a ESP32 WROOM DA Board. Some of our requirements had strict timing constraints - the rover was self-balancing (think Segway), with a cascaded control loop that needed to continuously run. To meet these constraints, we had to use FreeRTOS to schedule tasks appropriately on the ESP32's dual cores. I had the pleasure of learning and using FreeRTOS for this project though it was not covered as part of Imperial's Y2 curriculum (the department seems to enjoy challenging its students to self-learn and apply concepts - not that I mind. We even had to implement some computer vision algorithms on the FPGA).  

{{< figure src="/images/y2rover.jpg" title="" >}}

### What is FreeRTOS

FreeRTOS is one example of a Real-Time Operating System (RTOS). An RTOS differs from a general purpose operating system in that it gives engineers better control over timing; this means that tasks are executed in a deterministic manner. An RTOS enables us to condense our functions into tasks. For example, our cascaded control loop will become one task that is managed by the scheduler. We also included other tasks such as (1) Wi-Fi and MQTT connection, and (2) a state machine that provides the autonomous logic for our rover to solve the maze via its suite of on-board sensors. We are able to assign priorities to each task, determine its stack size, and also which core it will run on. In our case, we decided to have the cascaded control loop run on one core, and the rest of the tasks to run on the other core. This was important as the cascaded control loop had strict timing constraints.  

### Memory Management

One issue that we faced during development was memory management. FreeRTOS has several synchronisation primitives that enable us to prevent race conditions. Race conditions occur when two threads write over the same location in memory, causing errors and CPU crashes (see below). For context, we had two tasks running at this time on the same core; one for Wi-Fi and MQTT 'keep alive' (Priority 4), and one for our rover state machine (Priority 5).    

{{< figure src="/images/crash.png" title="" >}}

To overcome this, we had several primitives that we could use to 'lock' the memory location and prevent race conditions. This included the binary semaphore and the mutual exclusion (mutex). The binary semaphore was used at first; however it continued to cause problems as we continued to get core panic errors. It was used whenver I had to publish MQTT messages on the rover state machine task.   

After spending some time reading the docs, I had assumed that the Binary Semaphore and Mutex were similar. However, they weren't. This was why we kept getting errors. I've listed their differences below (taken from G2G):    

|Binary Semaphore|Mutex|
|---|---|
|Its functions based up on signalling mechanism|Its functions based up on locking mechanism|
|The thread which is having higher priority than current thread can also release binary semaphore and take lock.|The thread which has acquired mutex can only release Mutex when it exits from critical section.|
|Semaphore value is changed according to wait () and signal () operations.|Mutex values can be modified just as locked or unlocked.|
|Multiple number of threads can acquire binary semaphore at a time concurrently.|Only one thread can acquire mutex at a time|
|Binary semaphore have no ownership.|There is ownership associated with mutex because only owner can release the lock.|
|They are faster than mutex because any other thread/process can unlock binary semaphore.|They are slower than binary semaphores because only thread which has acquired must release the lock.|
|If you have number of instances for resource it is better to use Binary semaphore.|If you have single instance for resource it is better to use mutex.|

So as you can see, the key issue was that the Rover state machine had a higher priority than our keep alive MQTT task, causing it to release the lock whenever the FreeRTOS scheduler performs a task switch. This caused potential memory corruption, leading to the core dump errors.  

The fix was simple: we just updated all our binary semaphores to mutexes. Worked like a charm! Now we were able to publish MQTT messages and run our MQTT keep-alive task simultaneously.  

Check out our [GitHub](https://github.com/clemenkok/BalanceBug) if you want to read up more about our project. 