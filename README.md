# Technical Report: Simple Hardware Side-Channel Attack via Bus Locking and Cache Timing on x86 (32-bit)

## 1. Introduction
Modern processors face risks from both software bugs and physical silicon design flaws. Side-Channel Attacks analyze physical behaviors, such as execution time, to extract sensitive information from the operating system kernel. This research examines the physical execution time domain on x86 32-bit architectures, focusing on cycle-counting techniques to evaluate hidden data exposure.

## 2. How the Attack Works (Step-by-Step)
The theoretical mechanism bypasses standard security barriers by creating processing contention within 32-bit hardware through three conceptual steps:

* **Step 1: The Waiting Phase (The NOP Sled)**
  Initial execution employs NOP commands (0x90 "No Operation") to create a controlled stabilization delay before analysis.

* **Step 2: The Flooding Phase (0xF0 / LOCK Prefix)**
  A high-frequency loop utilizes the MOV command with the 0xF0 (LOCK) prefix, locking the system bus on 32-bit systems to restrict memory access across cores and trap kernel data in the cache.

* **Step 3: Checking the EBX Register (Timing Analysis)**
  Using the RDTSC timer, the methodology measures the movement duration of trapped bytes via the EBX register:
  * **Cache Hit (Fast):** Interpreted as binary 0.
  * **Cache Miss (Slow):** Interpreted as binary 1.

## 3. Data Extraction Analysis
The conceptual framework processes the sequential binary stream derived from the timing analysis, demonstrating how kernel states can be represented for analytical evaluation.
