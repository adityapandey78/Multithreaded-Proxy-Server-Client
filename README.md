


# Multi-Threaded Proxy Server (with and without Cache)

This is a simple but powerful HTTP proxy server made in `C`. You can run it with or without cache. The main aim was to get hands-on with real systems concepts like `multithreading`, `synchronization`, `socket programming`, and how to build an `LRU cache` from scratch. This project is perfect for learning and also looks good in interviews!

---



## Features

- **Multithreaded**: Handles multiple clients at the same time using `pthread`. No lag even if many people connect together!
- **Semaphore for Control**: Used `semaphore` to limit how many clients can connect at once. So, system never hangs or crashes due to overload.
- **LRU Cache (Optional)**: If you want, you can enable cache. It stores GET responses and serves them super fast if the same request comes again. Used a simple linked list and time logic for LRU.
- **Custom HTTP Parsing**: Wrote my own HTTP parser to extract method, headers, etc. So, it works with most browsers and tools.
- **Error Handling**: If someone sends a wrong request or something fails, the proxy gives a proper error message. No silent failures.
- **Easy to Switch Modes**: Just change one line in the `Makefile` to switch between cache and no-cache mode. Very flexible for testing.

---


## Architecture & Flow

![Proxy Server UML](https://github.com/Lovepreet-Singh-LPSK/MultiThreadedProxyServerClient/blob/main/pics/UML.JPG)

**How it works (in simple words):**
1. Start the proxy on any port you want. It will keep listening for new connections.
2. Whenever a new client connects (like your browser or `curl`), a new thread is created for that client. But, only up to a certain limit (set by `semaphore`).
3. The proxy reads the HTTP request, parses it, and checks if the response is already in cache (if cache is enabled).
4. If found in cache, it sends the cached response. Otherwise, it fetches from the real server, sends it to the client, and (if cache is on) saves it for next time.
5. After sending the response, the thread closes the connection and is done. Proxy keeps running for new clients.

---


## How to Build & Run

**Requirements:**
- Any Linux system (tested on Ubuntu, but should work on most)
- `gcc` compiler (C99 or above)

**Steps:**
```bash
git clone <this-repo-url>
cd Multithreaded-Proxy-Server-Client
make all
./proxy <port>
```

**How to use:**
- Set your browser or tool (like `curl`) to use `localhost:<port>` as the proxy.
- Example: Open `http://localhost:<port>/https://www.cs.princeton.edu/` in your browser, or use `curl -x http://localhost:<port> https://www.cs.princeton.edu/`

**Some tips:**
- Works only on Linux (uses `pthread` and `semaphore`).
- For testing cache, make sure to disable your browser's own cache.
- To switch off cache, just open `Makefile` and change `proxy_server_with_cache.c` to `proxy_server_without_cache.c`.

---


## Demo

![Cache Demo](https://github.com/Lovepreet-Singh-LPSK/MultiThreadedProxyServerClient/blob/main/pics/cache.png)

- First time you open a site, you will see `url not found` (means cache miss).
- Next time, if you open the same site, you will see `Data is retrieved from the cache` (means cache hit, super fast!).

---



## CS Concepts I Actually Learned

- **Multithreading**: Used `pthread_create`, `pthread_join`, etc. to run multiple things at once. Learnt how to avoid race conditions and why locks are important.
- **Semaphores & Mutexes**: Used `semaphore` to control how many clients can connect, and `mutex` to make sure cache is not corrupted when many threads use it together.
- **Socket Programming**: Understood how to use `socket()`, `bind()`, `listen()`, `accept()`, and how to send/receive data between client and server.
- **LRU Cache**: Made my own LRU cache using linked list and time logic. Got to know how browsers and big servers use caching to save time and bandwidth.
- **HTTP Protocol**: Wrote code to parse HTTP requests, extract headers, and forward them. Now I know how browsers and servers actually talk!
- **Error Handling**: Learnt to handle all sorts of errors (bad requests, network issues, etc.) and give proper messages instead of crashing.

---



## Key Implementation Concepts (How I Did It)

- **One Thread per Client**: For every client, a new thread is created. This makes the code simple and easy to debug. Also, it uses all CPU cores.
- **Semaphore for Max Clients**: Used a counting `semaphore` to make sure only a fixed number of clients can connect at once. If limit is reached, new clients wait.
- **Mutex for Cache**: All cache operations are protected by a `mutex` so that two threads don't mess up the cache at the same time.
- **Custom HTTP Parser**: Instead of using any library, wrote my own code to split HTTP requests and extract what is needed.
- **Clean Resource Management**: Made sure to close all sockets and free memory after use. No memory leaks!
- **Debug Prints**: Added lots of `printf` statements for debugging. You can see when cache is hit/miss, when errors happen, etc.

---



## Limitations & What Can Be Improved

- **Cache is Simple**: Each request/response is stored separately. If a site is very dynamic or opens many connections, cache may not help much.
- **Cache Size is Fixed**: If a response is too big or too many requests come, old cache entries are removed. Big sites may not fit in cache.
- **Only GET Supported**: Right now, only `GET` requests work. No support for `POST`, `PUT`, etc.
- **Linux Only**: Uses `pthread` and `semaphore`, so won't run on Windows/Mac without extra work.

**What can be added next:**
- Use process pool or event-driven model for even more speed.
- Add support for `POST` and other HTTP methods, and maybe even HTTPS.
- Make cache size and policy configurable from command line.
- Add some access control or filtering (block some sites, etc.).
- Add some stats or monitoring to see how many cache hits/misses, etc.

---



## References & Further Reading

- HTTP parsing logic adapted from: [vaibhavnaagar/proxy-server](https://github.com/vaibhavnaagar/proxy-server)
- [Beej's Guide to Network Programming](https://beej.us/guide/bgnet/)
- [Linux man pages: pthreads, semaphores, sockets](https://man7.org/linux/man-pages/)
- [Wikipedia: Proxy server](https://en.wikipedia.org/wiki/Proxy_server)

---
