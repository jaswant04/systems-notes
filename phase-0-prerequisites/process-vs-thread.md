# Process vs Thread

## What this concept is about
This note explains what a process and a thread are, how they differ, and what role threads play in executing code.

---

## Core definitions

### Process
- A **process** is a program that is currently running.
- Each process has its **own memory space**.
- One process **cannot directly access** another process’s memory.
- If a process crashes, other processes are usually unaffected.

### Thread
- A **thread** is the unit that actually **executes instructions**.
- Threads exist **inside a process**.
- Multiple threads inside the same process **share the same memory**.
- A process must have **at least one thread** to run.

---

## What a thread actually does

- A thread represents a **single line of execution**.
- It moves step by step through instructions in the program.
- It keeps track of:
  - where execution currently is
  - which function is running

Code does not execute by itself — **threads execute code**.

---

## Why it is called a “thread”

A thread is called a thread because it is a **continuous execution path** running through a program.
It is literally a single sequence of instructions being followed from start to finish.

---

## Memory behavior

- Processes are **isolated** from each other.
- Threads inside the same process **share memory**.
- Because memory is shared, threads can:
  - communicate easily
  - interfere with each other if not handled carefully

---

## Single-threaded vs multi-threaded (conceptual)

### Single-threaded execution
- Only one thread exists.
- Instructions execute in a fixed, predictable order.
- No interference from other threads.

### Multi-threaded execution
- More than one thread exists.
- Threads share memory.
- Execution order is not guaranteed.
- Incorrect results can occur if threads modify shared data at the same time.

---

## Important clarifications

- A program is **single-threaded by default** unless it explicitly creates threads.
- Creating multiple threads makes a program **multi-threaded**.
- CPU cores execute **threads**, not processes.
- Processes are containers; threads do the actual work.

---

## Final takeaway

> A process provides isolation and memory, while a thread is the execution path that runs instructions. Threads live inside processes and share memory, which makes them powerful but potentially dangerous.
