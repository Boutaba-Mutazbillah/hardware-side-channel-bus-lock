# Technical Report: Hardware Side-Channel Attack via Atomic Bus Locking and Cache Timing Analysis

## 1. Introduction & Microarchitectural Threat Vector
Modern hardware security focuses heavily on microarchitectural design flaws within silicon chips. Hardware Side-Channel Attacks exploit physical phenomena, such as execution timing variations, to leak highly privileged data. This bypasses standard operating system access controls without breaking encryption protocols directly.

This research targets the physical execution time domain. An attacker samples fine-grained CPU cycle variations to reconstruct protected kernel data. 

![Device Under Attack - DUA](https://github.com/user-attachments/assets/b00e98db-b0e5-4914-b891-d428455084d8 "Technical block diagram showing a Device Under Attack (DUA) box, highlighting physical emanation vectors with a focus on the temporal side-channel.")
*Figure 1: Microarchitectural block diagram illustrating a system under analysis (Device Under Attack - DUA), tracking the temporal leakage vectors utilized to sample hardware CPU cycles.*

---

## 2. Microarchitectural Exploitation Mechanism
The execution pipeline of this exploit bypasses standard Ring 3 privilege isolation to access Ring 0 states. It deliberately induces critical hardware-level resource contention across CPU cores. The execution sequence flows through two destructive phases:

### • Phase 1: Atomic Bus Flooding & Contention (`0xF0 / LOCK`)
The payload executes a high-density loop packed with data-movement operations (`MOV`), prepended with the `0xF0` opcode byte (the architectural `LOCK` prefix). This forces the processor's Control Unit to assert a hardware signal that locks the shared system bus (Atomic Bus Locking). This isolates the execution core and completely starves adjacent cores from accessing system memory. The resulting memory line collision, known as Cache Line Contention, forces victim transactions to stall. This forces sensitive kernel data (`Sensitive s`) to congest within target lines (`Cache state q`).

### • Phase 2: Pipeline Interruption & Register Profiling (`EBX`)
A software interrupt (`INT`) is triggered concurrently to flush internal execution pipelines. A lightweight tracking thread monitors architectural states, specifically targeting the general-purpose register `EBX`. By using high-resolution cycle counters (`RDTSC`), the tool profiles micro-operations to compute execution latency. A rapid loop indicates a Cache Hit (binary `0`), while a delayed loop signifies a Cache Miss (binary `1`).

![Cache State Q and Inverse Function](https://github.com/user-attachments/assets/4a50fbfa-914c-4801-a7d6-309b0443cd02 "Microarchitectural flowchart demonstrating the mapping of a sensitive kernel state into Cache State Q and the execution of the inverse function.")
*Figure 2: Exploitation flowchart demonstrating the mapping of sensitive kernel states into Cache State Q and the execution of the inverse tracking function to map latency variants.*

---

## 3. Data Visualization & Boot Persistence Strategy
When observing the continuous execution of this loop, the hardware voltage and timing response yield distinct microarchitectural signatures. The steady flooding and sequential probing generate repetitive high-frequency spikes. The software captures these 16 continuous high-frequency peaks, translating the cycle deltas directly into a raw binary stream (`0101`) piped into a local flat `TXT` file.

To maintain persistence and prevent a fatal system collapse, such as a Blue Screen of Death (`BSOD`) caused by permanent hardware bus deadlocks, the engine incorporates a defensive **NOP Sled (`0x90`)** configuration at the initialization sequence. When a hard reset is triggered (`Restart`), the boot persistence script launches the handler. The CPU safely slides over the `NOP` instructions during early boot without initiating new bus lockups, giving the data-extraction engine the necessary window to securely dump and parse the remaining cache remnants before volatile memory registers are cleared.

![Timing Wave Spikes](https://github.com/user-attachments/assets/bb1bdbcf-6dae-4422-ad70-cf6e6b4d7bb2 "Technical timing wave chart displaying voltage and time with 16 distinct high-frequency continuous blue spikes representing binary data extraction profiles.")
*Figure 3: High-frequency timing wave profiling showing 16 distinct voltage and latency peaks (Spikes) mapped during continuous cache flooding and binary extraction.*

---

## 4. Repository Tags
#HardwareSecurity #SideChannelAttack #MicroarchitecturalExploit #BusLocking #TimingAnalysis #x86_64Assembly #CacheContention #LowLevelDevelopment #KernelHacking #RootkitPersistence #NOPSled #ReverseEngineering #CyberSecurity #ComputerArchitecture #IntelPentest #BinaryExploitation #HardwareHacking #TechReport #BinaryDump #SoftwareInterrupt
