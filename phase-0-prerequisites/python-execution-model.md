# Python Execution Model

## What this concept is about
This note explains how Python executes code, what role processes and threads play, and why Python behaves differently when it comes to parallel execution.

---

## Core understanding

- A Python program runs as a **process**.
- Inside a process, **multiple threads can exist**.
- However, due to the **Global Interpreter Lock (GIL)**, only **one thread can execute Python code at a time within a single process**.
- Because of this, a single Python process cannot execute Python code in parallel on multiple CPU cores.

---

## How Python runs code

- Python source code is first converted into an internal format called **bytecode**.
- This bytecode is executed by the **Python interpreter**.
- Execution happens instruction by instruction at the bytecode level, not directly from the source code lines.

---

## Process vs Thread (execution view)

- A **process** is a running instance of a program with its own memory space.
- A **thread** is the unit that actually executes instructions inside a process.
- Threads inside the same process share memory.

In Python:
- Multiple threads may exist inside one process.
- Only one thread is allowed to execute Python bytecode at any given time.

---

## Global Interpreter Lock (GIL)

- **GIL** stands for **Global Interpreter Lock**.
- It is a lock that ensures only one thread executes Python bytecode at a time inside a process.
- The GIL is **per process**, not shared across processes.

This means:
- Multiple Python processes can run in parallel on multiple CPU cores.
- A single Python process cannot achieve parallelism using threads alone.

---

## Important clarifications

- Python is **not truly single-threaded** — threads can exist.
- Python is **single-threaded in execution of Python code per process**.
- Parallel execution in Python is achieved using **multiple processes**, not threads.

---

## Final takeaway

> Python converts source code into bytecode and executes it inside a process where, due to the Global Interpreter Lock, only one thread can execute Python code at a time. True parallelism is achieved by running multiple processes.
