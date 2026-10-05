Technical Report: Simple Hardware Side-Channel Attack via Bus Locking and Cache Timing
1. Introduction
Modern computer processors (CPUs) are not just vulnerable to software bugs, but also to physical design flaws in their silicon chips. Side-Channel Attacks look at physical behaviors—like how much Time a command takes to execute—to steal secret information from the core of the operating system (Kernel).
Our project focuses on counting CPU cycles using a high-speed timer to find and leak this hidden data.
2. How the Attack Works (Step-by-Step)
The attack bypasses the computer's standard security barriers by creating a heavy traffic jam inside the processor hardware. The code does this in three simple steps:
• Step 1: The Waiting Phase (The NOP Sled)
At the very top of our code, we place a sequence of NOP commands (Assembly code 0x90 which stands for "No Operation"). When the processor runs this part, it spends time doing nothing for a specific number of repeats. This acts as a smart delay to let the system stabilize and wait for the perfect moment before the actual attack starts.
• Step 2: The Flooding Phase (0xF0 / LOCK Prefix)
Right after the waiting phase ends, the real attack begins. The code starts a fast, heavy loop using a data-moving command (MOV) with a special byte in front of it: 0xF0 (the LOCK prefix). When the CPU sees this, it locks the main data pathway (System Bus). This stops all other CPU cores from touching the memory, causing secret kernel data to get trapped and piled up inside the cache memory.
• Step 3: Checking the EBX Register (Timing Analysis)
Our program is built to measure exactly how many nanoseconds those trapped bytes take to move, using the CPU's built-in timer (RDTSC). The code looks directly inside a general hardware storage box called the EBX register to read the results:
	• If the speed is fast (Cache Hit): The code translates this into a binary 0.
	• If the speed is slow (Cache Miss): The code translates this into a binary 1.
3. Saving the Leaked Data
The tool collects all these extracted 0s and 1s from the EBX register one by one. It combines them into a full binary stream and automatically saves them safely into a standard text file (TXT file) on your computer.
