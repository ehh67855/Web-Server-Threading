## Web-Server-Threading
Implements a simple, multi-threaded web server in C, utilizing sockets for network communication, semaphores for synchronization, and pthreads for concurrency. The code structure suggests that it's designed to handle HTTP requests in parallel, with a focus on demonstrating inter-thread communication and synchronization rather than full-fledged web server functionality. 

## Overview
In the implementation webserver.c, several components were implimented to ensure the safe handling of concurrent requests. The server uses a producer-consumer model, where the listener thread (using req_handler) listens for incoming connections and places them in a shared buffer, acting as the producer. Worker threads function as consumers, processing requests from the buffer. Testing involved using multiple concurrent client connections to ensure the server could handle simultaneous requests without crashing or producing incorrect behavior. Another test was deliberatly creating scenarios where the number of incoming connections exceeded the buffer capacity to ensure that the semaphore and mutex logic prevented buffer overflows and managed underflows correctly.
- **Network Communication:** Using sockets to accept and handle incoming TCP connections.
- **Concurrency:** Utilizing multiple threads to handle network IO and request processing simultaneously.
- **Synchronization:** Employing semaphores and mutexes to manage access to shared resources (the request buffer) between threads safely.
- **Dynamic Thread Management:** The attempt to manage worker thread lifecycles dynamically, although this aspect could be clearer or more robustly implemented.

## Threading - bounded buffer synchronyzation
The server's design incorporates a circular buffer to manage incoming requests, with semaphores (sem_full, sem_empty) and a mutex (mutex) ensuring thread-safe operations. The sem_empty semaphore prevents buffer overflow by indicating available space, while sem_full signals the presence of requests to be processed, ensuring consumers do not attempt to process from an empty buffer. The mutex guarantees mutual exclusion during buffer access, preventing race conditions. Due to a lack of robust automated testing methods, testing included outputting the contents of the buffer at specific moments to manually inspect its contents. Additionally, the excecution time was inspected to ensure that it improved with a multithreaded excecution. When running without crash handling, the excecution time is signifigantly reduced which suggests the multithreading has excecuted properly. I have at some point encounted a situation where the program gets stuck, however due to the nondeterministic nature of threading I have not been able to reproduce the situation for debugging.

### Compile and excecution instructions
```
cd Web-Server-Threading
make all
./server 4000 10 50
./client 127.0.0.1 4000 10
make clean

```
