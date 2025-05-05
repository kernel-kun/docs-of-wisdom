Imagine you have a single chef (the CPU) in a kitchen that needs to cook dozens of dishes (tasks) at once—but can only work on one thing at a time. How can the chef give the illusion of multitasking without burning anything? Enter the **event loop**: a smart kitchen manager that keeps track of what needs cooking, what’s waiting on the oven timer, and what’s ready to serve—and tells the chef exactly what to do next.

---

## 1. The Very Basics: What Is an “Event”?

- An **event** is simply “something happened” that your program cares about:
    - A network reply arrived
    - A timer expired
    - A file finished writing
- You register a little note (“when that event happens, run this code”) with the kitchen manager.
    

> “Event loop waits for and dispatches events or messages in a program… then it calls the relevant event handler.” ([Reddit](https://www.reddit.com/r/explainlikeimfive/comments/1boxmhk/eli5_the_concept_of_event_loop_in_computer_science/?utm_source=chatgpt.com "ELI5: the concept of event loop in computer science - Reddit"))

---

## 2. The Event Loop’s Job

1. **Wait** for something to happen (I/O, timer, etc.)
    
2. **Look up** which code (“handler”) is tied to that event
    
3. **Run** that code until it needs to wait again
    
4. **Repeat** forever (or until you tell it to stop)
    

Because the chef only ever works on one thing at a time, there’s no confusion—but the kitchen manager juggles the order so every dish finishes on schedule. ([Python documentation](https://docs.python.org/3/library/asyncio-eventloop.html?utm_source=chatgpt.com "Event Loop — Python 3.13.3 documentation"))

Ref. https://docs.python.org/3/library/asyncio-eventloop.html#executing-code-in-thread-or-process-pools

---

## 3. Coroutines: Kitchen Helpers

- A **coroutine** is like a recipe written so the chef can pause mid‑step, go do something else, then come back and pick up right where they left off.
    
- In Python you write them with `async def` and pause with `await`.
    

```python
async def make_tea():
    await boil_water()    # pause until water’s boiled
    steep_tea()           # then resume here
```

When `await` appears, the event loop chef goes, “Great—while we wait on water, let me work on the soup.” ([Python documentation](https://docs.python.org/3/library/asyncio.html?utm_source=chatgpt.com "asyncio — Asynchronous I/O — Python 3.13.3 documentation"))

---

## 4. Tasks & Futures: Booking Your Spot

- A **Future** is a promise: “I’ll have a result later.”
    
- A **Task** wraps a coroutine and puts it into the event loop’s schedule.
    

Think of a Task as a ticket – you give the chef a ticket for “make_tea,” and the manager knows when to call you back with your tea. ([Python documentation](https://docs.python.org/3/library/asyncio.html?utm_source=chatgpt.com "asyncio — Asynchronous I/O — Python 3.13.3 documentation"))

---

## 5. Why Use an Event Loop?

- **No wasted time**: While one task is waiting on I/O (network, disk), the chef works on another.
    
- **Single thread**: You avoid the complexity of many chefs bumping into each other (no thread‑locking headaches).
    
- **Deterministic order**: The manager always knows who goes next.
    

This model shines when most work is waiting on slow I/O, not raw number‑crunching. ([Medium](https://medium.com/delivus/understanding-pythons-asyncio-a-deep-dive-into-the-event-loop-89a6c5acbc84?utm_source=chatgpt.com "Understanding Python's asyncio: A Deep Dive into the Event Loop"))

---

## 6. Putting It All Together: A Simple Example

```python
import asyncio

async def fetch_data(n):
    await asyncio.sleep(1)       # pretend this is network I/O
    print(f"Fetched {n}")

async def main():
    tasks = [asyncio.create_task(fetch_data(i)) for i in range(3)]
    await asyncio.gather(*tasks)  # wait for all to finish

asyncio.run(main())
```

- `asyncio.run` starts the event loop.
    
- `create_task` hands the coroutine to the loop.
    
- `await asyncio.gather` waits until all tickets (tasks) are done.
    

---

## 7. Next Steps

1. Try adding a timer or file‑read to see how the loop juggles them.
    
2. Read about `asyncio.Semaphore` to limit how many tasks run at once.
    
3. Experiment with **uvloop** (a faster replacement) when you need extra speed.
    

With these basics, you now know how Python’s event loop lets one thread handle dozens—or thousands—of waiting tasks, keeping your programs responsive and efficient. Happy cooking!


---

I'll help you understand the distinction between using threads vs processes with `run_in_executor` in asyncio, and why this matters for different types of tasks.

# Understanding `loop.run_in_executor` with Threads vs Processes

## The Core Concepts

When working with asyncio, you'll encounter three types of operations:

1. **Native async/await operations** - These use asyncio's event loop directly and are non-blocking by design
2. **I/O-bound operations** - Operations that spend most of their time waiting for external resources (files, network, etc.)
3. **CPU-bound operations** - Operations that perform heavy computational work using the CPU

## Why Use Different Executors?

Looking at the Python documentation for `loop.run_in_executor()`, there's a reason the examples show:

```python
# 1. I/O-bound operations in ThreadPoolExecutor
result = await loop.run_in_executor(None, blocking_io)  # Uses default ThreadPoolExecutor

# 2. CPU-bound operations in ProcessPoolExecutor
with concurrent.futures.ProcessPoolExecutor() as pool:
    result = await loop.run_in_executor(pool, cpu_bound)
```

### I/O-Bound Tasks in ThreadPoolExecutor

For I/O-bound operations (like file operations, network requests), threads work well because:

- Python's Global Interpreter Lock (GIL) is **released during I/O operations**, so threads can work concurrently
- Threads have lower overhead than processes (less memory, faster creation)
- The blocking operation is waiting for an external system, not using CPU
- Multiple threads can efficiently handle many concurrent I/O operations

For example, in your Confluence service code:
```python
return await loop.run_in_executor(_executor, self._get_user_details_blocking, email)
```

This is perfect for a ThreadPoolExecutor because it's making HTTP requests (I/O operations), and the GIL is released while waiting for the response.

### CPU-Bound Tasks in ProcessPoolExecutor

For CPU-bound operations (like heavy calculations), processes are better because:

- Python's GIL prevents true parallel execution in threads
- Processes run in separate memory spaces, bypassing the GIL limitation
- Each process gets its own Python interpreter and can use a full CPU core
- Computational tasks can achieve true parallelism across CPU cores

## What Happens If You Choose the Wrong One?

### Using Threads for CPU-Bound Tasks:

- Performance suffers significantly since the GIL prevents threads from executing Python code in parallel
- You're essentially running tasks sequentially despite the overhead of thread creation
- Your async application will become sluggish since a CPU-bound task in a thread will still block the GIL

### Using Processes for I/O-Bound Tasks:

- Unnecessary overhead in memory and process creation
- Data must be serialized between processes
- More complex error handling
- Often slower than threads for I/O operations due to the overhead

## Integration with Asyncio - Why It's Important

The beauty of `run_in_executor` is that it bridges the gap between:

1. Asyncio's non-blocking concurrent model
2. Python's threading/multiprocessing libraries for blocking operations

Without `run_in_executor`:

- Any blocking call (I/O or CPU-intensive) would freeze your entire event loop
- All other coroutines would have to wait, defeating the purpose of asyncio
- Your application's responsiveness would suffer dramatically

By using `run_in_executor` appropriately:

1. Your async event loop remains responsive
2. Blocking operations run in appropriate contexts (threads for I/O, processes for CPU)
3. Results are properly awaited and integrated back into your async flow
4. Exception handling works seamlessly across the boundary

## Practical Example

In your Confluence service code:

```python
# This is excellent for I/O bound operations (HTTP requests)
_executor = concurrent.futures.ThreadPoolExecutor(max_workers=thread_count)

async def get_user_details(self, email: str):
    # Running a blocking HTTP call in a thread
    return await loop.run_in_executor(_executor, self._get_user_details_blocking, email)
```

If this were CPU-bound work instead (like heavy data processing), you would be better off with:

```python
_process_executor = concurrent.futures.ProcessPoolExecutor()

async def heavy_computation(self, data):
    # Running CPU-intensive work in a separate process
    return await loop.run_in_executor(_process_executor, self._compute_data, data)
```

## In Summary

- **ThreadPoolExecutor**: Use for I/O-bound tasks (file operations, network calls, DB queries)
- **ProcessPoolExecutor**: Use for CPU-bound tasks (data processing, number crunching)

The asyncio event loop combined with appropriate executors gives you the flexibility to handle both types of blocking operations without sacrificing the performance and responsiveness of your application.


---

## 1. Concurrency vs. Parallelism & the GIL

| Term                              | What it means                                                                    | Python implication                                                               |
| --------------------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **Concurrency**                   | multiple tasks making progress “at the same time” (interleaved)                  | `asyncio` coroutines, threads switching on I/O or scheduling                     |
| **Parallelism**                   | tasks literally running simultaneously on different CPU cores                    | multiprocessing or native threads in languages without a GIL                     |
| **GIL (Global Interpreter Lock)** | a mutex in CPython that ensures only one thread executes Python bytecode at once | Threads can’t run Python code in true parallel; they shine on I/O, not CPU work. |

- **Effect on CPU‑bound threads**: if threads all run pure‑Python compute, they serialize on the GIL, so you won’t see speed‑ups.
    
- **Effect on I/O‑bound threads**: when a thread does I/O (file, network, sleep), it releases the GIL, letting another thread run.
    

---

## 2. Threading and `concurrent.futures`

### a) Low‑level: `threading` module

```python
import threading

def worker(n):
    print(f"Worker {n} starting")
    # simulate I/O
    import time; time.sleep(1)
    print(f"Worker {n} done")

threads = []
for i in range(5):
    t = threading.Thread(target=worker, args=(i,))
    t.start()
    threads.append(t)

for t in threads:
    t.join()
```

- **`.start()`** begins the thread; **`.join()`** waits for it to finish.
    
- Use `threading.Lock()` (or `RLock`, `Semaphore`, etc.) to protect shared data.
    

### b) High‑level: `concurrent.futures.ThreadPoolExecutor`

```python
from concurrent.futures import ThreadPoolExecutor

def fetch(url):
    import requests
    return requests.get(url).status_code

urls = ["https://example.com"] * 10
with ThreadPoolExecutor(max_workers=5) as exe:
    # exe.map returns results in order
    statuses = list(exe.map(fetch, urls))
print(statuses)
```

- **`.submit(fn, *args)`** → `Future`; **`.map(fn, iterable)`** → iterator of results.
    
- Good for simple I/O concurrency without manual thread management.
    

### c) CPU‑bound: `ProcessPoolExecutor`

For CPU‑heavy work, bypass the GIL by spawning processes:

```python
from concurrent.futures import ProcessPoolExecutor

def fib(n):
    if n < 2: return n
    return fib(n-1) + fib(n-2)

with ProcessPoolExecutor() as exe:
    results = list(exe.map(fib, [30,31,32]))
print(results)
```

---

## 3. Asyncio: Event Loop, Coroutines, Tasks

### a) Core primitives

```python
import asyncio

async def greet(n):
    print(f"Hello {n}")
    await asyncio.sleep(1)    # non‑blocking sleep
    print(f"Goodbye {n}")

async def main():
    # schedule two coroutines concurrently
    await asyncio.gather(greet(1), greet(2))

asyncio.run(main())   # creates & runs the event loop
```

- **`async def`** defines a coroutine.
    
- **`await`** yields control back to the event loop until the awaited thing completes.
    
- **`asyncio.run()`** sets up the event loop, runs until the given coroutine completes, then closes it.
    

### b) The Event Loop

- A loop that picks one ready coroutine, runs it until it hits `await`, then switches to another.
    
- All code between `await`s runs under the GIL, but `await` points allow “context switch.”
    

### c) Creating Tasks

```python
loop = asyncio.get_event_loop()
task1 = loop.create_task(greet(1))
task2 = loop.create_task(greet(2))
await asyncio.gather(task1, task2)
```

- **`asyncio.create_task()`** (or `loop.create_task`) schedules a coroutine “in background” as a Task.
    
- Tasks are needed if you want to kick off work and then do other things before awaiting their result.
    

---

## 4. Combining Async + Threads: `run_in_executor`

When you have a blocking function (e.g. legacy library, CPU‑bound chunk you can’t convert to async), you can offload to a thread (or process) so the event loop stays responsive:

```python
import asyncio
from concurrent.futures import ThreadPoolExecutor

def blocking_io(x):
    import time; time.sleep(2)
    return x * 2

async def main():
    loop = asyncio.get_running_loop()
    with ThreadPoolExecutor() as pool:
        # schedule blocking_io in thread, don’t block loop
        result = await loop.run_in_executor(pool, blocking_io, 5)
    print(result)

asyncio.run(main())
```

- You saw speedups because the I/O in threads released the GIL, and the event loop could interleave other coroutines while threads blocked.
    

---

## 5. Synchronization & Safe Mutation

### a) Threading locks

```python
import threading

lock = threading.Lock()
shared = 0

def thread_task():
    global shared
    with lock:
        # only one thread at a time here
        tmp = shared
        tmp += 1
        shared = tmp
```

### b) Asyncio locks

When multiple coroutines share data:

```python
import asyncio

lock = asyncio.Lock()
shared = 0

async def coro_task():
    global shared
    async with lock:
        tmp = shared
        await asyncio.sleep(0)   # simulate context switch
        shared = tmp + 1
```

- **`asyncio.Lock()`**, **`asyncio.Semaphore()`** behave like their threading counterparts but for coroutines.
    

---

## 6. CPU‑bound vs I/O‑bound: How to decide

|Work type|Characteristics|Best tool|
|---|---|---|
|**I/O‑bound**|network calls, disk I/O, sleep, subprocess|`asyncio`, threads|
|**CPU‑bound**|heavy computation, tight loops|`ProcessPoolExecutor`|

**Rule of thumb:**

- If your function spends most time waiting (sleep, network, file), use `asyncio` or threads.
    
- If it spends time computing, use processes.
    

---

## 7. Key APIs Cheat‑Sheet

|Concept|Module / Class|How to use|
|---|---|---|
|**Thread**|`threading.Thread`|`t=Thread(...); t.start(); t.join()`|
|**Thread pool**|`concurrent.futures.ThreadPoolExecutor`|`executor.map()` / `submit()`|
|**Process pool**|`concurrent.futures.ProcessPoolExecutor`|same API as ThreadPoolExecutor|
|**Coroutine**|`async def`|call it with `await`|
|**Event loop**|`asyncio.run()`, `get_event_loop()`|drives coroutines|
|**Task**|`asyncio.create_task()`|schedule in background|
|**Awaitable I/O**|`await asyncio.sleep()`, `await reader.read()`|yields control|
|**Executor bridge**|`loop.run_in_executor()`|run blocking in thread/process|
|**Thread lock**|`threading.Lock`|`with lock:`|
|**Async lock**|`asyncio.Lock`|`async with lock:`|

---

## 8. Next‑step Exercises

1. **Pure asyncio I/O**
    
    - Write an `async def fetch_all(urls)` that uses `aiohttp` or `httpx.AsyncClient` to fetch 10 URLs concurrently.
        
    - Measure elapsed time vs synchronous.
        
2. **ThreadPool vs ProcessPool**
    
    - Write a CPU‑bound function (e.g. compute 35th Fibonacci recursively) and compare:
        
        - direct call
            
        - in `ThreadPoolExecutor`
            
        - in `ProcessPoolExecutor`
            
    - Observe timings and explain in terms of the GIL.
        
3. **Shared Counter**
    
    - Create 100 threads incrementing a global counter 10,000 times each.
        
    - First do it without locks (observe wrong result), then with `threading.Lock`.
        
4. **Async shared state**
    
    - Create 100 coroutines incrementing a shared variable under `asyncio.Lock`.
        
    - Sprinkle `await asyncio.sleep(0)` inside critical section to force context switches.
        
5. **Mixing blocking + async**
    
    - Use `loop.run_in_executor` to call a blocking function (e.g. `time.sleep(2)`) from within an `async def`, interleaving with other coroutines.
        

---

## 9. Resources & Next Reading

- **Official docs**:
    
    - Threading: [https://docs.python.org/3/library/threading.html](https://docs.python.org/3/library/threading.html)
        
    - Concurrent futures: [https://docs.python.org/3/library/concurrent.futures.html](https://docs.python.org/3/library/concurrent.futures.html)
        
    - Asyncio: [https://docs.python.org/3/library/asyncio.html](https://docs.python.org/3/library/asyncio.html)
        
- **Tutorials**:
    
    - “AsyncIO in Python: A Complete Walkthrough” (Miguel Grinberg)
        
    - Real Python: “Python Concurrency: Threads, Asyncio, and Multiprocessing”

---

Here’s what’s going on under‑the‑hood in CPython—and why your I/O‑bound threads can “run in parallel” but CPU‑bound threads cannot.

---

## 1. What the GIL is

- **GIL = Global Interpreter Lock**  
    A mutex inside the CPython interpreter that ensures **only one** thread is executing Python **bytecode** at any given instant.
    
- Purpose: makes memory management (reference counting) safe without fine‑grained locking in every object.
    

**Implication:** if you have N Python threads all running pure‑Python computation, they will _never_ execute bytecode truly simultaneously on multiple cores—they take turns holding the GIL.

---

## 2. When the GIL is released

CPython is written in C, and C extensions can surround long‑running C calls with macros to drop the GIL:

```c
/* Pseudocode inside a C extension */
Py_BEGIN_ALLOW_THREADS;
/* ... call into a blocking C library, or do long compute in C ... */
Py_END_ALLOW_THREADS;
```

Similarly, many built‑in operations release the GIL around blocking I/O or long C calls:

|Operation|GIL behavior|
|---|---|
|File or socket I/O|GIL released during the system call|
|`time.sleep()`|GIL released while sleeping|
|Blocking C‑API calls (e.g. certain database drivers)|GIL released around the call|
|Pure‑Python loops / arithmetic|**GIL held** the entire time|

---

## 3. “I/O release” vs. “CPU release”

|Term|What happens|Effect on other threads|
|---|---|---|
|**I/O release**|Thread calls a blocking I/O function (e.g. `read()`, `sleep()`, socket send/recv). CPython temporarily **drops** the GIL before entering kernel, re‑acquires it on return.|Other Python threads can run while this one waits in the kernel.|
|**CPU‑bound (no release)**|Thread runs Python bytecode or C code that doesn’t drop GIL (e.g. tight arithmetic loop).|No other thread can run Python bytecode until current thread hits a periodic “check” or finishes.|

### Example: I/O release

```python
import threading, time

def io_task():
    print("I/O task sleeping")
    time.sleep(2)     # internally releases GIL
    print("I/O task done")

def cpu_task():
    print("CPU task starting")
    x = 0
    for i in range(10**7):  # pure‑Python loop, GIL held except periodic checks
        x += 1
    print("CPU task done")

t1 = threading.Thread(target=io_task)
t2 = threading.Thread(target=cpu_task)
t1.start(); t2.start()
t1.join(); t2.join()
```

- While `io_task` is in `time.sleep`, the GIL is released → `cpu_task` can run full‑speed.
    
- If both threads were CPU‑bound, they’d swap holding the GIL only at periodic “check intervals” (default every 5 ms), so you won’t see a linear speed‑up.
    

---

## 4. Why CPU‑bound threads don’t scale

- Python’s bytecode evaluator only checks for thread switches every **`sys.getswitchinterval()`** seconds (default 0.005 s).
    
- A CPU‑heavy loop will grab the GIL and only yield at those check points—so threads effectively serialize.
    
- **Workaround for CPU work:** use `multiprocessing` or `ProcessPoolExecutor` (each process has its own GIL).
    

---

## 5. Key takeaway

- **I/O‑bound threads** → benefit from threading, because blocking I/O operations release the GIL and let other threads run.
    
- **CPU‑bound threads** → do **not** benefit (they fight over the GIL). Use processes or rewrite the hotspot in a C extension that drops the GIL.
    

---

### Quick reference: common GIL‑releasing operations

|Python call|Releases GIL?|Notes|
|---|--:|---|
|`time.sleep()`|Yes|yields GIL for the sleep duration|
|`socket.recv()`, `read()`|Yes|during the underlying OS read syscall|
|`subprocess.call()`|Yes|around the blocking system call|
|Pure‑Python arithmetic|No|holds GIL until bytecode count threshold|
|List/dict operations|No|C code runs **with** the GIL|

---

With this you should see why your thread‑pool sped up I/O: the GIL was dropped during each blocking call. By contrast, if you point that same pool at a tight computation, threads simply take turns on one core.