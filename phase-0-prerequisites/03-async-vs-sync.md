# Async vs Sync

## What this concept is about
This note explains the difference between synchronous and asynchronous execution, especially how a program behaves while waiting for slow operations like database or external service responses.

---

## Synchronous execution (Sync)

- In synchronous execution, code runs **step by step**.
- When a request needs data from an external system (for example, a database), the executing thread **waits until the response arrives**.
- While waiting, the thread is **blocked** and cannot do any other work.

### What this means in practice
- If multiple requests arrive, they are handled **one after another**.
- A slow operation blocks the thread and delays other requests.
- Waiting time is wasted because nothing else can run on that thread.

---

## Asynchronous execution (Async)

- In asynchronous execution, the program can handle **multiple tasks** using the same thread.
- Each incoming request is handled as a **task**.
- When a task reaches an `await` (for example, waiting for a database response), that task **pauses** instead of blocking the thread.
- The thread then switches to handling **another task**.

### What this means in practice
- While one request is waiting for a database response, other requests can be handled.
- When the external system responds, the paused task **resumes** and continues execution.
- Waiting time is reused instead of wasted.

---

## What `await` actually means

- `await` means the current task **needs a result to continue**.
- Instead of blocking the thread, the task pauses and gives control back to the program.
- The task resumes only when the awaited operation has completed.

Important:
- The task is **not running in the background** while waiting.
- The work during waiting is done by external systems (database, network, OS), not by Python code.

---

## Key differences (summary)

| Aspect | Sync | Async |
|-----|-----|------|
| Thread behavior | Blocks while waiting | Never blocks |
| Waiting | Freezes execution | Pauses task |
| Handling multiple requests | One at a time | Interleaved |
| Use of waiting time | Wasted | Reused |

---

## Final takeaway

> Synchronous code blocks the executing thread while waiting for a result, whereas asynchronous code pauses the waiting task and allows the same thread to handle other tasks until the result is ready.
