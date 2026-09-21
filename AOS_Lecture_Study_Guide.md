# CS G623 Advanced Operating Systems: Lecture Outlines and Textbook Map

**Source:** Whisper large-v3 transcripts of Lec 3–14, 16 and 17 (Prof. Biju K Raveendran, Sem I 2026–27).
**Primary text:** T1 = Silberschatz, Galvin, Gagne, *Operating System Concepts*, **10th ed.** (the edition your handout names).
**Secondary:** T2 = Singhal & Shivaratri; R7 = Bovet & Cesati, *Understanding the Linux Kernel* (ULK, 3rd ed. chapter numbers); Stallings = *Operating Systems: Internals and Design Principles* (not on the handout, but several of his slides follow it word for word; see below).

---

## 0. Read this first

**Coverage.** Lectures 1, 2 and 15 are missing from the folder. Lec 1–2 were probably the intro (handout L01–L02, T1 Ch 1–2). Lec 15 fell between the fork-tree tracing in Lec 14 and Lec 16, so get notes from a classmate. So far, the lectures cover roughly **handout L01–L08** (OS structure, system calls, processes in UNIX/Linux). They also preview multiprocessor architecture (L09–L12, in Lec 3) and pieces of Linux scheduling and load balancing (L17–L22, in Lec 10, 12 and 13). Threads (T1 Ch 4) haven't started yet. He said in Lec 17 that he is "six classes behind" last year.

**Handout discrepancy.** The handout cites "T1 Ch 16" for Linux, but in the 10th edition Ch 16 is *Security*. The Linux case study is **Ch 20**. The handout's reference probably dates from an older edition, so use Ch 20.

**Material Silberschatz doesn't cover.** A lot of what he teaches, and therefore examines, isn't in Silberschatz:
- **Stallings Ch 3:** the 7-state (suspend) model, the UNIX SVR4 state diagram, PCB = "identification / *processor* state / control" information, the six-step UNIX `fork()` list, and mode switch vs process switch.
- **ULK (R7) Ch 3, 7 and 10:** Linux task states, the PID bitmap, `real_parent` vs `parent`, `do_fork()` internals, the O(1) quantum formula, and the `int 0x80` syscall path.
- **T2 Ch 16:** tightly vs loosely coupled systems, UMA/NUMA/NORMA, and caching and coherence.

I list these next to each lecture. If a Silberschatz section only partly covers a topic, read the other source.

**Where his answer differs from the textbook.** Give his answer in his exam. See §3 at the bottom.

**Transcript noise to skip:**
- Lec 3, first ~6 lines: garbled.
- Lec 5, last lines ("It's a good one" ×3): hallucination.
- Lec 11, everything after "D, Y, C, R, 1 is the class": student chatter after the recording kept running.
- Lec 17, everything after the inode discussion: student chatter.
- Lec 10, first ~10 min: attendance and research-practice advice. It isn't exam material, but it's worth one read for career planning.

**Section numbers.** The numbers below are from the 10th-edition table of contents. The Wiley India reprint uses the same section numbering, so spend 5 minutes checking them against your copy's TOC.

---

## 1. Lecture by lecture

Each lecture has four parts:
- **Outline:** the cleaned content.
- **He asked / stressed:** exam-style questions he posed and "never misunderstand" statements. This is the highest-value material.
- **Read:** the textbook sections.
- **Transcript fixes:** mis-transcriptions in that file.

---

### Lec 3: Multiprogramming vs multitasking vs multi-user; CPU and multiprocessor architecture; coherence; UMA/NUMA/NORMA; RPC

**Outline**
- **Multiprogramming vs multitasking.** Multitasking performs better. Multiprogramming has fewer decision points. **Multi-user** is a different axis: many users work at once. Linux isolates users fully, so a misbehaving user can't hurt others, and it enforces per-user **soft and hard limits** on things like the number of forks (this returns in Lec 17). The hardware underneath is still just multitasking.
- **CPU features:**
  - Pipelining is instruction-level parallelism: IF/ID/EX/MEM/WB. The clock period equals the *slowest* stage. Deeper pipelines allow a faster clock but add latch/buffer overhead.
  - **Superscalar** means some stages are replicated.
  - **Out-of-order execution** runs instructions out of order but retires them in order, with a buffer (he called it the "reservation station") holding early results.
  - **Hyper-threading** gives 2 *hardware* threads per core. These are never software threads.
  - **Turbo boost** is DVFS: power ∝ V²f, so lower frequency and voltage save energy.
  - His framing: i3 = hyper-threading, i7 = HT + one turbo frequency, i9 = HT + multiple turbo frequencies. "**Never say the difference between i3/i5/i7/i9 is frequency.**"
- **Symmetric vs asymmetric depends on the level you compare.** Same ISA and same frequency is truly symmetric (SMP Linux assumes this). Same ISA with different frequencies is symmetric by ISA but asymmetric by frequency; processes can still migrate. Different ISAs (e.g., CPU + DSP) make a **heterogeneous multicore**: the cores share data through memory, but processes cannot migrate. Fast and slow cores exist for energy saving.
- **Cache hierarchy:**
  - **L1 is always private and split (I/D)** because it's part of the pipeline: fetch and memory access happen at the same time.
  - L2 is private or shared, and always unified.
  - L3 is always shared and *dynamically partitioned* between cores.
- **Tightly coupled** means shared memory, one box or a rack joined by InfiniBand. **Loosely coupled** means distributed: nothing shared, *message passing only*.
- **Coherence:**
  - **Write-invalidate is preferred over write-update.** Invalidation is a one-time signal. After it, you hold the line in the **Exclusive** state and can modify it without asking anyone. In the **Shared** state, you must notify everyone and wait for n−1 acknowledgements before committing.
  - Exception: two cores ping-ponging on the same data. There, write-update wins.
- **Lookup order:** own L1 → **other cores' L1** (it may hold the only up-to-date, modified copy) → L2 → L3. Faster transfer isn't the only reason for this order; correctness is.
- **Write batching:** one byte written → a cache line → a larger block → a 4 KB page to disk. So accumulate writes.
- **NUMA, in his framing:** different parts of the hierarchy take different times to access (your own L1 is faster than another core's). All tightly coupled multicore systems are NUMA. He claims "UMA is not practical and is never used." **NORMA (no remote memory access)** applies to loosely coupled systems: you ask the remote *processor*, which validates the request and sends the data. This is slower but safer than shared memory.
- **Synchronization** is easy in tightly coupled systems (shared memory, one clock) and very hard in loosely coupled ones. Example: clock synchronization, where variable routes, hops and queueing make delay unmeasurable ("1000+ algorithms"). This previews T2 Ch 5, logical clocks.
- **RPC:** a remote call blocks the caller until the remote side returns. The block is remote, not local.

**He asked / stressed**
- "Cores at 3.2/4.2/3.2/3.6 GHz with the same ISA: symmetric or not?" (It depends on the level.)
- "Why is L1 always split?"
- "Write-invalidate or write-update, and why?"
- "On an L1 miss, why check the other core's L1 before your own L2?"
- "Why is clock sync hard in a distributed system?"

**Read**
- **T2 16.1–16.5:** the primary source for tightly vs loosely coupled, UMA/NUMA/NORMA, interconnects and caching/coherence (handout L09–L12).
- **T1:**
  - 1.3.1–1.3.3: single-processor, multiprocessor (SMP, multicore, **NUMA figure**) and clustered systems.
  - 1.4.1: multiprogramming and multitasking.
  - 1.5.5: cache management and coherency.
  - 1.8: distributed systems.
  - 5.5.2: multicore processors, hardware threads, chip multithreading.
  - 5.5.4: NUMA-aware affinity.
  - 5.5.5: heterogeneous multiprocessing (big.LITTLE).
  - 3.8.2: RPC.
- Pipelining, superscalar and out-of-order execution aren't in either text. Any architecture book covers them (Hennessy & Patterson).

**Transcript fixes:** ILV → ILP · "Kindle has 20-stage pipeline" → probably "Intel (Pentium 4)" · "institute" → execute · "block cycle" → clock cycle · "codes/course" → cores · "pneuma / new mark" → NUMA · "umma" → UMA · "infinite band" → InfiniBand · "pops" → hops · "right update" → write update · "Intel Atom… nine cores" → likely misheard (the point is one fast core + several slow cores).

---

### Lec 4: OS design paradigms (layered/monolithic); UNIX structure; the system-call entry point; files vs processes; the inode walk; file tables

**Outline**
- **Layered approach:** layer 0 is the hardware and layer N is the user interface. Each layer uses only the one below it (OSI analogy).
  - Pros: easy debugging and verification.
  - Cons: overhead from layers you don't use (you can't bypass them), and the layers need careful definition.
  - *Modular ≠ layered.* One layer can contain many modules.
- **UNIX/Linux structure:** user program → libc wrapper (e.g., `scanf` in libc.so) → **system-call interface** → kernel (process control incl. IPC and scheduling, memory management, file system, device drivers) → hardware. The kernel **validates all device data** before handing it to the user.
- **Single entry point.** Every system call enters the kernel at the *same* location via `int 0x80`. The **EAX** register holds the syscall number (valid range 0 … N−1, with 400+ syscalls). The kernel then jumps to the handler through a table.
- **Monolithic kernel** → one `bzImage`. Any change (e.g., a new syscall) means recompiling and rebooting.
- **Why kernel functions are `inline`.** A function call flushes the prefetch queue and the pipeline and may miss in the cache. Inlining keeps the modular source but removes the call at compile time. Recursion is expensive; tail recursion can be turned into iteration by a gcc optimization.
- **"Everything is either a file or a process."** stdin, stdout and stderr are files. `ls -l` type characters: `-` regular, `d` directory, `c` character device, `b` block device, `l` link.
- **PID and inode:**
  - A **PID is an index** into an array of fixed-size PCBs (0…32767). "PID 200" means slot 200.
  - An **inode** is the UNIX file control block (`ls -i`).
  - Each process remembers only two inode numbers: **root** (a global/system-wide structure) and **cwd** (per-process).
- **Worked example:** `fopen("/bin/a1.txt","r")` takes **6 disk transfers**:
  1. inode of `/`
  2. data block of `/` (to find `bin`)
  3. inode of `bin`
  4. data block of `bin` (to find `a1.txt`)
  5. inode of `a1.txt`
  6. its *first* data block

  Mode `a` loads the *last* block instead. Mode `w` truncates the file to size 0.
- **File descriptors and offsets.**
  - `fp` indexes the **per-process file descriptor table**. Entries 0, 1 and 2 are stdin, stdout and stderr, which is why `2>` redirects only stderr.
  - The **offset is per process**: two processes reading the same file keep separate positions.
  - The **open count** is global. `fclose` only decrements it. The last close writes back and frees memory. Process exit closes files automatically.
- **Course scope (his words):** AOS covers scheduling, IPC and part of memory management. File systems belong to "Data Storage Technologies." FS internals are low priority, but the fopen/offset/open-count reasoning keeps coming back (Lec 13, 17).
- **Buffer cache:** only for **block devices** (never character devices). It's part of main memory, not the CPU cache. It exploits spatial and temporal locality. Byte-level transfer happens only between the CPU and L1; everywhere else transfers are whole blocks.
- **OS policy:** the OS steps in only when you harm *others*. Overwriting your own memory is your problem. A segfault means touching someone else's region. Allocation happens in blocks, which is why an off-by-one often doesn't crash (until the demo).

**He asked / stressed**
- "How many disk accesses to open this path?" (He repeats this with a longer path in Lec 13.)
- Pros and cons of the layered approach.
- Why the kernel uses inline functions.
- What `fclose` does when two processes have the file open.
- Why the cwd is per-process and root is global.

**Read**
- **T1:**
  - 2.3.1–2.3.3: system calls, API, types.
  - 2.8.1: monolithic.
  - 2.8.2: layered.
  - 2.8.4: modules.
  - 2.9.1: building the Linux kernel from source.
  - 12.3.1: block vs character devices.
  - 13.1.2: file operations, **per-process vs system-wide open-file tables, file pointer, open count**.
  - 13.3: absolute vs relative path names.
  - 14.2: in-memory FS structures, the `open()` figure.
  - 14.4.3: indexed allocation and the UNIX inode.
  - 14.6: buffer/page cache.
  - 20.2–20.3: Linux design and kernel modules.
- **R7 ULK Ch 10:** system call handler.

**Transcript fixes:** "microcurrent / micro critical approach" → microkernel · "interrupt xat" → `int 0x80` · "scanner function" → scanf · "I not / INOD" → inode · "BID" → PID · "a1.pxt / pxp" → a1.txt · "beta block / delta block" → data block · "temperance" → temporal · "no-sit-brain" → a gcc tail-call option (probably `-foptimize-sibling-calls`; unclear) · "S-close / F-close" → fclose · "units" → Unix.

---

### Lec 5: Microkernel; hybrid kernels; partitions and swap; the three schedulers; thrashing; which process to suspend

**Outline**
- **Microkernel:** a minimal kernel (mainly **message passing/IPC**) with services in user space.
  - Pros:
    - You can add modules without recompiling or rebooting, so there's little downtime.
    - It's easy to extend.
    - **It's easy to port**, because only the small core is architecture-specific. That matters for ASICs, embedded systems and time-to-market.
    - It's more reliable and secure because the kernel is small.
  - Con: user↔kernel message-passing overhead, so it isn't used for general-purpose or high-performance OSes.
  - Examples: **QNX** (automotive, avionics, robotics; he pushed the QNX certification), TinyOS (sensor motes). Nano- and pico-kernels are even smaller. Energy-constrained devices (a pacemaker battery must last 4+ years) are another use case.
- **Monolithic kernel:** the file system, scheduler and virtual memory are all inside the kernel.
- **Hybrids:**
  - Linux = monolithic + loadable modules (`insmod`).
  - Windows = monolithic + microkernel-style subsystem "personalities."
  - Hypervisors (KVM, Xen) are partly microkernel.
  - "No OS follows a single paradigm."
- **Portability:** the `arch/` directory in the Linux source; gcc cross-compiling.
- **Tangents (skip):** interoperable file systems (FAT12 on floppies because the FAT32 table exceeds 1.44 MB; ISO 9660 on CDs), GRUB/LILO passwords, bypassing Windows SAM.
- **Partitions:** *raw* vs *cooked* (formatted with a file system). **Swap is a raw partition, 1.5–2× RAM.** A raw byte dump is faster than going through a file system.
- **Schedulers:**
  - Long-term: new → ready.
  - Short-term: ready → running.
  - **Medium-term:** swapping, i.e., blocked → blocked-suspend and ready → ready-suspend.
- **Thrashing:** plot CPU utilization against the degree of multiprogramming. Past a point, each process gets too few frames and everyone just swaps. Utilization drops, so the **long-term scheduler adds more jobs**, which makes it worse. The fix is to suspend processes.
- **What gets suspended:** the **PCB is never swapped**; only code and data go to swap. Where a process can come back to depends on the binding: compile-time and load-time binding force the same location, while **execution-time binding** (paging) allows anywhere. Modern systems use execution-time binding.
- **Question he posed:** swap out a *low-priority ready* process or an *I/O-blocked* one? Answer: the low-priority ready one. The I/O process was high priority (it had been on the CPU), so it rejoins at the front of the queue, and suspending a blocked process creates a dangling I/O return target. "**Every selection must have a justified reason. Do the same in interviews.**"
- Only hard links and symbolic links exist ("there is nothing called soft link"). A symlink to a deleted file is a dangling pointer.
- **Modular design allows load-on-demand.** Valid/invalid bits start out invalid. The 80/20 rule: 20% of the code runs 80% of the time.

**Read**
- **T1:**
  - 2.8.3: **microkernels** (Mach, Darwin, QNX).
  - 2.8.4: modules.
  - 2.8.5: **hybrid systems** (macOS/iOS, Android, Windows).
  - 3.2.2: CPU scheduling, including the swapping/medium-term sidebar.
  - 9.1.2: **address binding**.
  - 9.1.4–9.1.5: dynamic loading and linking.
  - 9.5: swapping.
  - 10.2.1: demand paging and valid-invalid bits.
  - **10.6.1: cause of thrashing.** This is almost exactly his "long-term scheduler makes it worse" story.
  - 11.5.1: partitions and raw disk.
  - **11.6: swap-space management** (raw partition).
- **Stallings 3.2:** suspended processes and reasons for suspension (his "which one to suspend" framing).

**Transcript fixes:** "microcurrent" → microkernel · "unx workshop" → QNX workshop · "minecast / micas / telos p" → MICAz / TelosB motes · "tiny orders" → TinyOS · "ARC directory" → `arch/` · "MDFs, getStick4, part32, part12" → NTFS, ext4, FAT32, FAT12 · "high signer" → High Sierra/ISO 9660 · "lilo / grep password" → LILO / GRUB password · "row / couple / book the partition" → raw / cooked · "with time scheduler" → medium-term · "tangling" → dangling · "zen" → Xen · "Solari" → Solaris.

---

### Lec 6: Abstract data types and C practice for the lab

This is mostly lab material. It matters for the onlines, not much for the midsem.

**Outline**
- **Why ADTs:** you ship only `.h` and `.o` and hide the implementation. You can swap implementations (linear vs binary search) by **relinking, without recompiling the driver**. This gives you abstraction and encapsulation in C.
- **Three files:**
  - **Interface** `.h`: constants, struct declarations, `extern` prototypes with pre- and post-condition comments, and `#ifndef/#define` include guards.
  - **Implementation** `.c`: `#include`s the interface. Its name doesn't have to match the header's.
  - **Driver:** `main` only, as small as possible.
- **`int main(int argc, char *argv[])`.** He insists on this exact signature, not `char **argv`. The return value goes to the parent (bash); check it with `echo $?`. A finished process stays a zombie until the parent collects its status.
- **Arguments:** `argv[0]` is the program name, and every argument is a string. `atoi` stops at the first non-digit (`"5abc6"` → 5). Use `atof` for floats.
- **`void*` is the generic pointer.** A pthread start routine is `void *f(void *)`, so pack many arguments into a struct.
- **Row-major arrays and caches:** `a[i][j]` loops are cache-friendly; `a[j][i]` misses on every access (his 1024-column example).
- **OS work is about finite n.** At n = 10, n² + 5 beats n + 2000. That's why quicksort and mergesort coexist, why randomized pivots help, and why random cache replacement is used. External sorting handles data larger than RAM.
- **Build:** `gcc -c x.c` → `x.o`, then `gcc -o name a.o b.o driver.o`. Always name the executable (otherwise it's `a.out` and gets overwritten).
- **Lab rules:**
  - Input comes from files via `fopen`/`fscanf`, not typing and not redirection.
  - **No `gets`/`fgets`** (his rule); use `fscanf`/`read`.
  - Prefer `strncpy`/`strncmp`.
  - **Learn `man`** because there's no internet in the lab.
  - Bring only a pen and water; everyone uses identical machines.
- **Next up:** recompile the kernel from kernel.org *unmodified* first, then implement an individual syscall.

**Read**
- **T1:**
  - 2.5: linkers and loaders (object files, relocation).
  - 3.3.2: return status and zombies.
  - 4.4.1: Pthreads (`void*` signature).
  - **10.9.5: program structure** (the classic row- vs column-order array example).
- **R9 K&R** for C details.

**Transcript fixes:** "scan of an printer" → scanf and printf · "LIPSY role SO" → libc.so · "extent / extend" → extern · "rc / rv / rg" → argc / argv · "K2F" → atof · "CUBE sort / MERG" → quicksort / merge sort · "huda programming" → CUDA · "get test / fget does" → gets / fgets · "echo dollars hash" → `echo $?` · "A dot O" → a.out.

---

### Lec 7: Protection, dual/multi-mode, virtualization, the timer, what a syscall costs, process relationships

**Outline**
- **Protection goal in multitasking:** protect the OS from users and users from each other. A process may harm only itself.
- **I/O protection:** all I/O instructions are **privileged**. The OS performs I/O and validates the data.
- **Dual-mode → multi-mode:** extra mode bits exist because of virtualization. **Mode bit 0 = monitor/kernel, 1 = user**, and lower numbers mean more privilege throughout Linux. A syscall via `int 0x80` switches the mode.
- **Virtualization:**
  - *Hosted*: the guest OS runs on a host OS, so it's slower.
  - *Hypervisor/VMM layer*: every OS sits directly on the VMM.
  - Para- vs full virtualization.
  - VMware's translation layer is written in assembly, which is why they hire assembly-strong candidates.
- **Load-balancing rule of thumb:** migrate a process only if **computation time ≫ communication time**. Forked children stay on the parent's core by default (shared code, COW data).
- **Termination is always done by the kernel.** It must update the parent, child and sibling links: the parent keeps only its first and last child, and siblings keep next/prev pointers (fixed-size structs). The PID is released only after the zombie is reaped.
- **`malloc` → `brk`/`sbrk` syscall.** Heaps can be global or partitioned; mobile devices use one large heap.
- **Execution time = utime + stime.**
- **Lazy admission.** Newly ready processes wait in a *pending queue* and are admitted only on the way back from the kernel. So a syscall can cost you the CPU. Example: P1 has a 200-cycle quantum; higher-priority P2 arrives at t = 50. P1 makes a syscall at t = 100, P2 is admitted on the return path, and **P2 runs**. Without the syscall, P1 would have run until t = 200. Avoid unnecessary syscalls; calling by number skips one level of wrapper indirection.
- **The timer is the *only* CPU protection.** A pure multiprogramming system can't stop a malicious infinite loop. Even if only one job exists, the timer still hands control back to the OS.
  - Timers are **down-counters**: detecting zero needs one NOR gate, versus a comparator of XORs.
  - Loading the timer is privileged.
- **Memory hardware:** SDRAM is synchronized to an *external* bus clock, not the CPU clock, so you know when to check back. DMA does disk→RAM transfers and interrupts when done. SRAM (used for caches) needs no refresh; DRAM needs refresh.
- Electronics tangents (multivibrators, RC charging) → skip.

**He asked / stressed**
- "Why is the timer the only mechanism for CPU protection?"
- "Why can a syscall make you lose the CPU?"
- Why children stay on the same core.

**Read**
- **T1:**
  - 1.4.2: **dual-mode and multimode operation**.
  - 1.4.3: **timer**.
  - 1.2.1 and 12.2.3: interrupts.
  - 12.2.4: **DMA**.
  - 1.7, 18.4–18.5: VM building blocks; Type 0/1/2 hypervisors and paravirtualization.
  - 17.3: protection rings (x86 and ARM, hypervisor ring).
  - 3.3.1–3.3.2: creation, termination, zombies.
  - 5.5.3–5.5.4: load balancing and affinity.
- **ULK Ch 9:** the `brk` system call.

**Transcript fixes:** "interrupt xat" → `int 0x80` · "cash" → cache · "v ram / D-RAM" → DRAM · "malo" → malloc · "keep / keeps" → heap(s) · "port" (process creation) → fork · "impending queue" → pending queue · "zohari industry" → Zuari (local tangent).

---

### Lec 8: Memory protection (segmentation and paging); syscall mechanics and parameter passing; mode vs context switch

**Outline**
- **Kernel recompilation:** use the bare-metal lab machine; recompile unmodified first.
- **Protection trio:** CPU protection = timer; I/O protection = privileged instructions; **memory protection:**
  - **Segmentation** is the user's view of a program (code, data, stack and extra segments).
    - Intel: **LDT + GDT, 8K entries each** (13-bit index + 1 table-indicator bit) → **16K segments max**, never more than 8K of either kind.
    - A segment-table entry is **base + limit (length)**. Check offset < limit (not ≤); otherwise it's a segmentation fault. Physical address = base + offset.
  - **Paging.** A page-table entry = frame number + valid/invalid + auxiliary bits. The **page number is never stored**: it's the index. The **only place a page number is stored is the TLB**, which is fully associative. The offset passes through unchanged.
  - Paging needs no limit register because pages are fixed size (his networking analogy: fixed-size packets don't need a length field).
  - Segmentation causes **external fragmentation (~⅓ of memory unusable)**. Intel therefore uses **segmentation + paging**: the user sees segments, memory stores pages, and translation takes two lookups.
  - Contiguous allocation: base/limit registers; the valid range is base … base + limit − 1.
- **Syscall parameter passing:**
  - **EAX** (RAX on 64-bit) = syscall number. Up to **5 args in EBX, ECX, EDX, ESI, EDI**.
  - More than 5 args (e.g., `ioctl`): put them in a **block in memory and pass its address** (what Linux and Solaris do), or use the **stack** (least efficient: push plus pop).
  - Function-call analogy: MIPS `$a0/$a1`, `jal` saves PC + 4, `jr` returns.
- **Inside the kernel:**
  1. Check **EAX < max syscall number** (= last syscall + 1). Equal counts as invalid.
  2. **SAVE_ALL** registers.
  3. Jump through the table entry at **base + EAX×4** (32-bit) or **×8** (64-bit). His example: a table at 2000, entry 4 → 2016, which holds a jump to the handler.
  4. Run `sys_read` (which may block on I/O).
  5. Validate the result.
  6. **RESTORE_ALL**, set the mode bit back to 1, return.
- **`scanf` path:** main → libc wrapper → `read` → `int 0x80` → `sys_read` → keyboard I/O → validate → return. That's many function calls plus one mode switch.
- **Mode switch vs context switch.** If the kernel runs as part of the user process (UNIX), a syscall is only a mode switch. If the kernel is a separate process, *every* mode switch is a context switch, which is much costlier.
- **Pointer arguments.** The kernel must check (1) the address is in *user* space (the kernel itself can access anything, so it must protect itself first), then (2) it belongs to *this* process with the right permissions. That's why passing parameters through memory is slower. Many "arguments" are really output addresses (`scanf`, `ioctl`).
- **Registers:** RISC has more registers than CISC. MIPS **saved temporaries** (s0–s7) are always preserved across calls; **temporaries** (t0–t9) aren't.
- **State models coming up:** 5, 6, 7, 9 (UNIX), then Linux.

**He asked / stressed**
- "What's in one segment-table entry? One page-table entry?"
- "Where is the page number stored?" (Only in the TLB.)
- "Why doesn't paging need a limit register?"
- "Registers vs memory for parameters: which is faster, and *why* is memory slower?" (Validation.)

**Read**
- **T1:**
  - 9.1.1: base/limit hardware.
  - 9.2.1: memory protection.
  - 9.2.3: **fragmentation and the 50-percent rule (⅓ unusable)**.
  - 9.3.1–9.3.3: paging, TLB, valid-invalid bit.
  - **9.6.1: IA-32 segmentation** (LDT/GDT, 8K entries, selector = 13 + 1 + 2 bits) **and paging**. In 10e, segmentation lives here, not in its own section.
  - **2.3.2–2.3.3: three parameter-passing methods** (registers / block / stack, Fig 2.7).
  - 3.2.3: context switch.
- **Stallings 3.5:** "Execution of the OS" (non-process kernel vs executing within user processes). This is the mode-switch vs process-switch argument.
- **ULK Ch 2:** Linux segmentation, GDT/LDT.
- **ULK Ch 10:** syscall handler, parameter passing, verifying parameters.

**Transcript fixes:** "waging / phasing" → paging · "resistance" → registers · "gem / gem and link" → jump / jal · "interrupt x ap / INGREP text ID" → `int 0x80` · "edx ecx edx esi" → **EBX, ECX, EDX, ESI, EDI** · "DAX / eas / BIS" → EAX · "IOC / iocdl" → ioctl · "cisco" → CISC · "Solanese" → Solaris · "staffing address" → starting address · "thunder of the computer" → control.

---

### Lec 9: The syscall table; fork/vfork/COW; quantum split; context-switch cost; TLB; page-fault bookkeeping

**Outline**
- A syscall returns to **PC + 4**. The table lives in **`arch/x86/entry/syscalls/syscall_64.tbl`** (ABI `common` / `64` / `x32`). Don't reuse the reserved numbers (387–423, x32); add new syscalls after the last one (548+). Many entries are `sys_ni_syscall` (not implemented) or deprecated, kept for backward compatibility. Syscalls return `long`; on −1, use `perror`.
- **After `fork()`:**
  - The **child joins the ready queue ahead of the parent**, so the child *usually* runs first. It isn't guaranteed: they may be on different queues or cores, and often *neither* runs next.
  - **The child gets half the parent's *remaining* quantum.** Example: a 400 ms quantum with 300 ms used leaves 100 ms, so the child gets 50 and the parent keeps 50. A second fork gives 25/25. This stops a process from stealing CPU by forking.
  - Code is shared (read-only; separate PCs), and the child stays on the parent's core for locality.
- **Copy-on-write:** data is shared until *either* parent or child writes it first; then only that page is copied. **Code is never part of COW** because it's read-only.
- **vfork (pre-COW):** the child ran in the parent's address space while the parent was suspended until the child called exec/exit. This avoided copying 500 MB only to throw it away at `exec`. After COW arrived, vfork became pointless, so it's **deprecated but kept**. Syscalls, once stable, are never removed.
- **5-state model:**
  - The short-term scheduler handles only ready → running, and it's the hottest code path.
  - The long-term scheduler moves new → ready with **no return**.
  - Blocked processes go → ready, **never directly → running**.
- **UNIX splits running into user running and kernel running.** Blocking for I/O and termination (→ zombie) happen *only from kernel running*. Time is tracked as utime + stime.
- **"Context-switch *time* is constant; context-switch *impact* varies."**
  - The time is constant: save/restore all registers to/from the PCB, a fixed count per architecture.
  - The impact varies: the TLB is flushed, and the next process may evict your cache lines. How much depends on *who ran next*.
- **The TLB is flushed on a switch** because page numbers overlap across processes. **ASID/PID-tagged TLBs** avoid the flush; entries are flushed only at process termination.
- **TLB vs L1:** the TLB (20–32 entries) is faster than L1. Caches are accessed by *physical* address, so **the TLB comes before L1**.
- **TLB miss vs cache miss:**
  - A **TLB miss raises an exception**: the OS does a **page-table walk**, updates the TLB, and *re-executes the instruction*, which then hits. So 1000 accesses with 100 misses = **1100 TLB accesses**. "Textbooks get this calculation wrong."
  - A **cache miss** just stalls (memory stall cycles); there's no mode switch.
- **Page fault:** "page *tables* are updated" (plural). A *replacement* invalidates the victim's entry in *its owner's* table and validates yours. **Placement** (a free/invalid frame exists) updates only your table. **Replacement happens only when placement is impossible** (every frame valid).
- **Study tip from him:** read the textbook a month early, when you read "between the lines," not the night before.

**He asked / stressed**
- The quantum-split arithmetic.
- fork + exec with and without COW; why vfork existed.
- "Constant time, varying impact."
- Why the TLB is flushed.
- TLB-access counting.
- Placement vs replacement.

**Read**
- **T1:**
  - 2.3.3: types of system calls.
  - 3.1.2: process states.
  - **3.2.3: context switch**.
  - **3.3.1: `fork()`/`exec()`/`wait()`** (Figs 3.8–3.9).
  - **10.3: copy-on-write (includes `vfork()`)**. This matches the lecture almost exactly.
  - **9.3.2: TLB, ASIDs, effective access time.** Contrast its EAT formula with his 1100-access count.
  - 10.2.1: page-fault handling steps.
  - 10.4.1: basic page replacement ("if there is no free frame").
  - 20.4.1: the fork/exec model.
- **ULK Ch 3:** process creation.
- **ULK Ch 7:** timeslice split at fork in the 2.6-era scheduler.

**Transcript fixes:** "interrupt XID" → `int 0x80` · "jam" → jump · "indri" → `entry` (arch/x86/entry) · "v4 / four" → vfork / fork · "emccv / EFCC" → execve / exec · "delta section" → data section · "TLP / TLV / DLB / ELB" → TLB · "priority reverse system" → priority-preemptive system · "TLB hint" → TLB *hit* · "contact switch" → context switch · "ni system called" → `sys_ni_syscall` · "blue linux" → `include/linux` (syscalls.h).

---

### Lec 10: Linux priority ranges; FIFO vs RR; syscall overheads; read vs write; syscall design; the syscall catalogue

**Outline**
- **Mode vs context switch** (recap of Lec 8).
- **Linux priorities:** 140 levels, 0–139, where a **lower number means higher priority**. **0–99 are real-time**, **100–139 are conventional**. A conventional task runs only when no real-time task is runnable.
- **SCHED_FIFO and SCHED_RR use the same queue.** At quantum expiry, **RR re-queues the task at the tail; FIFO puts it back at the head**. FIFO still takes the timer interrupt, so CPU protection is kept. The data structure doesn't change, only where the task rejoins.
- **Why syscalls are expensive:**
  - Every jump can flush the prefetch queue and the pipeline and cause cache misses.
  - SAVE_ALL/RESTORE_ALL.
  - The possibility of being preempted at the admission point on return. His example: you're at priority 110; a 108 is admitted while you're in the kernel; the 108 runs next.
- **Prefetching:** background fetching of what you'll probably need next (the movie-frame analogy). It's wasted if you jump elsewhere.
- **Read vs write, in practice:**
  - A write is **acknowledged once it's in the disk controller's cache**. The actual platter write happens later, piggybacked on a nearby read, so seek and rotational latency are already paid.
  - A read **blocks** until the data reaches RAM.
  - So in practice, writes feel faster.
  - This is why a file can be empty after a segfault; `fsync` forces the flush.
- **Never invoke a syscall from inside the kernel.** Call the kernel function directly.
- **No floating point in the kernel.** The first syscall assignment: pass the IEEE-754 bits as an unsigned int and interpret them in the kernel. Return distinct error codes for NaN, ±∞, overflow and underflow.
- **Placement aside:** systems companies (Qualcomm etc.) came first last year and asked **bit manipulation** and a lot of OS.
- **Syscall design principles:**
  - One purpose per syscall (compose several rather than building a CISC-like one).
  - Define the arguments, return value and **error codes**.
  - Portable and robust.
  - **Verify pointers:** the address is in user space, belongs to this process, and has the right permissions.
  - Syscall numbers are officially assigned and form a **stable ABI**, so deprecated numbers are never reused. Each architecture registers syscalls separately.
- **Syscall vs function call:** a syscall involves at least a mode switch, is much slower, and always returns a value (`long`, for errors).
- **Catalogue:**
  - Process: `fork, wait, waitpid, execl/execlp/execv/execvp, exit, signal, kill`.
  - File: `creat, open, close, unlink, read, write, lseek`. The offset is per-process, so it's never passed as an argument; `lseek` moves it.
  - Memory: `malloc → brk/sbrk`. `realloc` to a larger size may move the block (copy + free).
  - **`man 2 exit` (syscall) vs `man 3 exit` (library).**

**Read**
- **T1:**
  - **5.7.1: Linux scheduling** (scheduling classes; RT 0–99, normal 100–139).
  - 5.3.3–5.3.4: RR and priority scheduling.
  - 2.3.2: API vs syscall.
  - **2.3.3: types of system calls** (Windows vs UNIX table).
  - 11.1: HDD seek and rotational latency.
  - 12.4.2–12.4.3: buffering and caching.
  - 14.6.2: synchronous vs asynchronous writes.
- **ULK Ch 7:** SCHED_FIFO/SCHED_RR semantics.
- **ULK Ch 10:** system calls.

**Transcript fixes:** "pipo / q4 / before" → FIFO · "protein point" → floating point · "mandesa" → mantissa · "flutter / plaster / plotter" → platter · "HPA" → HBA (host bus adapter) · "lc / LC" → lseek · "sys architecture / trist" → CISC / RISC · "man to exit" → `man 2 exit` · "Lipsy" → libc.

---

### Lec 11: Process concept, process image, 5/6/7-state models, thrashing, the UNIX (SVR4) state model

**Outline**
- A **program is passive; a process is active.** In Linux, *even a thread is a process* (a task).
- **Process image**, from low to high addresses:
  - text (read-only)
  - **initialized data (.data)**
  - **uninitialized data (.bss)**
  - heap (grows up via `brk`)
  - shared-library/mmap region in the middle
  - stack (grows down)
  - argv/environment at the top

  `int *values` lives on the stack and points into the heap. A dynamically allocated *array* keeps random access; a linked list loses it. Code and data are kept in separate segments to suit the split L1 I-cache and D-cache.
- **5-state model:** the dispatcher/short-term scheduler handles ready ↔ running; the long-term scheduler handles new → ready (no return); blocked → ready only; timeout arrow running → ready; terminated until the parent reaps.
- **Adding suspend states:**
  - The **medium-term scheduler reduces** multiprogramming.
  - The long-term scheduler increases it.
  - The short-term scheduler is neutral.
- **Thrashing curve**, and why the drop is steep: the long-term scheduler keeps pumping in jobs. Detection methods: rising process count with falling utilization, page-fault-frequency upper/lower bounds, a working-set window. Response: **first stop the long-term scheduler**, then suspend processes.
- **6-state model:** suspend only from blocked. Catch: **I/O order ignores CPU priority** (the disk goes by seek distance), so a blocked high-priority process's I/O isn't guaranteed to finish first.
- **Swap-out:** everything except the PCB (code, data, heap, stack) goes to the **raw** swap partition. A byte dump is fast and **endianness doesn't matter**.
- **Binding:**
  - Compile-time: absolute addresses.
  - Load-time: e.g., CS:IP with a base set at load, so it must return to the *same* place.
  - Execution-time: page/segment tables, so it can return *anywhere*.
- **7-state transitions to know:**
  - blocked → blocked-suspend
  - blocked-suspend → blocked (memory freed up; I/O still pending)
  - blocked-suspend → ready-suspend (I/O finished)
  - ready ↔ ready-suspend
  - **running → ready-suspend.** Under RR, the process whose quantum *just* expired is the best one to suspend because it's last in line.
  - new → ready or ready-suspend
- **Prefer suspending a low-priority *ready* process over a *blocked* one**, for two reasons. The blocked one is probably high priority. And suspending it creates a **dangling I/O target**: the I/O returns to memory now owned by someone else, so the OS must redirect it.
- **UNIX SVR4 model:**
  - user running ↔ kernel running (via syscall or interrupt).
  - The **kernel-running self-loop** means the kernel calls its own functions (no syscalls inside the kernel).
  - exit → **zombie**, only from kernel running.
  - I/O → **asleep in memory**.
  - ready to run in memory; sleep/ready swapped; created.
  - **preempted**: on return from the kernel, if a higher-priority process was admitted.

  Difference from the 7-state model: **there's no sleep-swapped → asleep-in-memory arrow** (UNIX waits for the I/O to finish first).
- **exit vs return:** the same inside `main`; anywhere else, `exit` kills the process.

**He asked / stressed**
- "Suspend from ready or from blocked? Justify it."
- "Why the running → ready-suspend arrow?"
- "Where does zombie come from?" (Kernel running.)
- Three schedulers and their effect on multiprogramming.

**Read**
- **T1:**
  - **3.1.1: process memory layout**, including the C-program layout figure (text / initialized / uninitialized / heap / stack / argc, argv).
  - 3.1.2: process states.
  - 3.2.2: swapping as the medium-term scheduler.
  - 9.1.2: binding.
  - **10.6: thrashing** (10.6.2 working set, 10.6.3 PFF).
  - 11.6: swap space.
- **Stallings 3.2:** five-state model and **two suspend states (7-state figure)**.
- **Stallings 3.6:** UNIX SVR4 process states (state diagram + table).

  These are the diagrams he's teaching from; **Silberschatz has no 7-state or SVR4 diagram**.

**Transcript fixes:** "keep" → heap · "rerolling section" → read-only section · "clashing" → thrashing · "mid-time / with time scheduler" → medium-term scheduler · "NDMS / NDM" → endianness / endian. **Ignore everything after "D, Y, C, R, 1 is the class."**

---

### Lec 12: Linux task states; per-core run queues; push/pull migration; affinity

**Outline**
- **Linux task states.** They are **bit flags**, and the state field is a bitmap.
  - `TASK_RUNNING` (0): ready *or* running.
  - `TASK_INTERRUPTIBLE`: woken by an **event or a signal**, e.g. `sleep()`.
  - `TASK_UNINTERRUPTIBLE`: woken by an **event only**; typical disk I/O. A signal leaves the state unchanged.
  - `__TASK_STOPPED`: stopped by SIGSTOP. It listens **only to signals**, and *another* process must send SIGCONT.
    - **Race:** if SIGCONT arrives *before* SIGSTOP takes effect, the CONT is lost and the process stays stopped forever. Order the signals carefully.
  - `__TASK_TRACED`: under ptrace/gdb; the tracer acts as the parent. Aside: the `__` prefix is a naming convention to avoid collisions. He was annoyed that lab submissions ignored it.
  - `TASK_PARKED`: **never considered for load balancing** (explained below).
  - `TASK_DEAD`: after the zombie is reaped. Used for **reporting stats** (CPU time, I/O wait, time stopped) before the task is removed.
  - `TASK_WAKEKILL`: waking only in order to kill.
  - `TASK_WAKING`: a transient state; announces "I'm already waking" so duplicate wake-ups are ignored.
  - `TASK_NOLOAD`: like the idle task; excluded from the load calculation.
  - `TASK_NEW`: created but not yet on a run queue.
  - `TASK_RTLOCK_WAIT`.
  - `EXIT_ZOMBIE`, `EXIT_DEAD`, `EXIT_TRACE`.
  - Composites: `TASK_KILLABLE` = WAKEKILL | UNINTERRUPTIBLE · `TASK_IDLE` = UNINTERRUPTIBLE | NOLOAD · `TASK_NORMAL` = INTERRUPTIBLE | UNINTERRUPTIBLE.
  - `TASK_STATE_MAX` marks how many bits are in use.
- **Why per-core run queues rather than one shared queue:**
  1. Cache affinity. Forked children share code, and data too until COW. Moving a task to another core means rebuilding its cache and paying coherence traffic.
  2. A shared queue needs a lock, so cores wait in turn (and without the lock there's a race).
- **Per-core queues cause load imbalance, which calls for load balancing:**
  - **Push migration:** *periodic* (every ~200–500 ms); the overloaded core pushes tasks to lightly loaded ones.
  - **Pull migration:** *aperiodic*; an **idle** core pulls work.
- **Estimating load.** Not the queue length (100 tiny jobs vs 1 huge one). Use the **predicted CPU burst** via exponential averaging (α × last burst + (1 − α) × history), among other factors.
- **Which task to migrate:** trade cache coldness against fairness. Moving the last task in line ahead of earlier arrivals is unfair. The balancing code is about 20k lines; Linux uses fair-share scheduling.
- **Affinity:** *soft* (the default; can migrate) vs *hard* (`sched_setaffinity` CPU mask; can't move even if other cores are idle). Example: pin the producer to core 0 and keep the consumer off it. Migration only ever happens between cores with the same ISA.
- **Load *im*balancing for energy:** 4 cores at 20% → 2 cores at 40% with 2 powered off. Powering off saves static *and* dynamic energy.
- **Orphans in Linux** are reparented to **init** (PID 1), not to the grandparent, which is why you see PPID = 1.
- Real implementations have many transient states. Example: MESI has 4 stable states but ~28 intermediate ones.

**He asked / stressed**
- The interruptible vs uninterruptible vs stopped comparison: *who can wake each one*.
- "Why not a single ready queue for all cores?"
- Push vs pull migration: which is periodic?
- "Which job would you migrate?"
- What `TASK_PARKED` is for.

**Read**
- **T1:**
  - 3.1.2–3.1.3, including the **"Process representation in Linux" (`task_struct`) sidebar**.
  - 3.3.2: orphans reparented to init/systemd.
  - **5.3.2: exponential averaging**.
  - **5.5.1: common vs per-core run queues**.
  - **5.5.3: push/pull migration**.
  - **5.5.4: soft/hard affinity, `sched_setaffinity`**.
  - 5.7.1: CFS load balancing, scheduling domains.
  - 20.5.4: SMP.
- **Linux task states aren't in Silberschatz.** Use **ULK Ch 3, "Process State"** (it lists an older subset) plus the kernel's `include/linux/sched.h`. His slide list matches the current `sched.h`.
- **T2 17.6:** multiprocessor scheduling (handout L17+).

**Transcript fixes:** "SIP / sick stop, sick continue" → SIGSTOP / SIGCONT · "SME" → SMP · "full migration" → **pull** migration · "4-0" → core 0 · "messy knot" → MESI.

---

### Lec 13: PCB / `task_struct`; PID allocation; the three PCB categories; nice, static/dynamic priority, quantum formula

**Outline**
- **Online 1:** ADT only, a simple question (no syscall). Syscall assignments are done offline and demoed to him.
- **PCB basics:**
  - `task_struct` is defined in `include/linux/sched.h`; it's very long.
  - The **PCB stays in RAM** for as long as the process exists; it's never swapped.
  - The **PID is an index** into the array of task structs.
  - A **bitmap** tracks which PIDs are used or free.
  - `pid_max` is configurable (32767 is the classic value).
- **PID allocation is next-fit and circular**, starting from the last allocated PID. So a child's PID can be **smaller** than its parent's once the counter wraps. Never assume child PID > parent PID.
- **Three PCB categories** (Stallings' wording, which he uses):
  1. **Process identification:** at minimum **PID + PPID** (like `.` and `..` in a directory). Also UID, GID, effective UID, **session ID**, and **TGID**. In a thread, `getpid()` returns the thread-group leader's ID; the thread's own PID is hidden. UID and GID are mainly for *resource* access (owner/group/other; `waitpid` on a process group).
  2. **Processor state information.** *Not* "process state": it's all the registers (general-purpose, PC, status, flags). SAVE_ALL saves every register whether it's used or not, which is **why context-switch *time* is constant**.
  3. **Process control information:** state, priorities, scheduling class and policy, `cpus_allowed` mask, privileges, memory, resources, `utime/stime/gtime`, **`nvcsw/nivcsw`** (voluntary vs involuntary context switches), exit code and exit signal.
- **Environment:**
  - Variables are `name=value`. PATH is colon-separated and searched in order (`printenv` lists them). argv is also in the process image.
  - **cwd is per-process; the root inode is system-wide.**
  - **Disk-access count:** a 4-level path from `/` takes about **10 accesses**; starting from cwd takes far fewer. That's why software keeps its files under one directory.
- **Priority arithmetic (O(1)-scheduler formulas):**
  - **static_prio = 120 + nice.** Nice runs −20…+19, so static_prio runs 100…139. **A negative nice needs root.**
  - **Base quantum = (140 − static_prio) × 20 ms if static_prio < 120; × 5 ms if static_prio ≥ 120.**
    - nice 0 → 100 ms
    - **nice −1 → 21 × 20 = 420 ms**
    - nice −20 → 800 ms (the maximum)
    - nice +19 → 5 ms (the minimum)

    `renice` changes nice at runtime.
  - **Dynamic priority** = static priority adjusted by a sleep-time bonus, so I/O-bound tasks are favoured. It's still within 100–139 and is what scheduling actually uses for conventional tasks.
  - Real-time tasks use **rt_priority 0–99** and always beat conventional ones.
  - Policies: `SCHED_FIFO`, `SCHED_RR`, `SCHED_OTHER`.

**He asked / stressed**
- "What is the *minimum* identifier set?" (PID + PPID.)
- "It's *processor* state information, not *process* state."
- Nice → quantum calculations. **Practice these numbers.**
- Why a child's PID can be less than its parent's.
- Voluntary vs involuntary context switches.

**Read**
- **T1:**
  - **3.1.3: PCB + Linux `task_struct` sidebar**.
  - 3.3.1: process tree and PIDs.
  - **5.1.4: dispatcher** (voluntary/nonvoluntary switches in `/proc/<pid>/status`).
  - **5.7.1: nice values**, CFS (O(1) is mentioned historically).
  - 13.3: path names.
- **Stallings 3.3:** the table of **typical PCB elements**, organised into exactly his three categories.
- **ULK Ch 3:** process descriptor, PID hash and pidmap.
- **ULK Ch 7:** static priority, **base time quantum formula**, dynamic priority and bonus.
- Note: this formula is from the **O(1) scheduler** (pre-2.6.23). Current Linux uses CFS/EEVDF weights. The handout lists both O(1) and CFS for L17–L22, so learn both.

**Transcript fixes:** "sked.h / step.h" → `sched.h` · "pgid" → tgid · "RP priority" → rt_priority · "sked underscore before / rr / other" → SCHED_FIFO / SCHED_RR / SCHED_OTHER · "cross" → thread (pthread) · "nb csw / niv csw" → nvcsw / nivcsw · "g time" → gtime · "I0 number" → inode number · "foreign separated" → colon-separated.

---

### Lec 14: `wait()`, exit status, orphans; fork-tree tracing

**Outline**
- **Online logistics:**
  - Log in to local Quanta beforehand.
  - Accounts are IP-bound to your lab machine; access from anywhere else is logged and *you* are penalized.
  - **No phones in the lab, even switched off.**
  - The online will be easier than the previous year's paper, with at least one test bench provided. It's ADT format with full error handling.
- **`wait()`:**
  - Waits for **any one** child and **returns that child's PID**.
  - Its single argument is a pointer where the OS stores the status.
  - **The exit value sits in bits 8–15, so use `status >> 8` (÷256)** (= `WEXITSTATUS`). `exit(1)` reads as 256 raw.
  - `waitpid()` waits for a specific child.
  - **`while (wait(NULL) != -1);`** reaps every child, and −1 means there are no children left.
- **Parent exits first → the child is reparented to init (PPID = 1).** This is bad: it flattens the tree and hurts searches. Avoid orphans. It's advisory, not enforced.
- **Fork-tracing method:**
  - **Analyse one process at a time, to completion.** Don't trace depth-first across processes.
  - A child starts right *after* `fork()`, with copies of the variables (e.g., the current `i`).
  - `if (fork())` → the *parent* enters (it gets the child's PID, which is non-zero).
  - `if (!fork())` → the *child* enters.
- **`for (i=0; i<3; i++) fork();`** → the root has 3 children; the first child has 2, the second has 1, and the first grandchild has 1. That's **7 new processes (8 total)**.
  - Within one parent, child PIDs are allocated in increasing order.
  - Overall interleaving and **print order are arbitrary** (buffering, scheduling, multiple cores).
- **if / else-if / else variants with `exit()`:** a process that hits `exit` stops creating children. Trace each branch.

**Read**
- **T1:**
  - **3.3.1:** `fork()`, `exec()`, `wait()` code and figures.
  - **3.3.2:** `wait()`, status, zombies, orphans, init/systemd.
  - **Ch 3 exercises on "how many processes are created".** They're exactly this style; he said the midsem will be harder than the class samples.

**Transcript fixes:** "sylvan" → children · "unit process" → init process · "weigh / weight" → wait · "if not for / if 4" → `if (!fork())` / `if (fork())`.

---

### Lec 16: Fork trace with variable values; PID management; parent vs real_parent; family pointers

**Outline**
- **Worked example.** Start with `x = 100` and a loop `i < 2`. Branches: `if (fork())` → x += 10; `else if (!fork())` → x = x + x; a second else-if → x −= 50 or −30; an `else` path. Then `wait(NULL)` before `execl("ls")`, with a final `x -= 20` in the survivors.
  - Answer to report: the final x for each process (and which processes run `ls`).
  - **The code was on the slide/Quanta, not in the audio. Get the .c file.** The transcript's narrated arithmetic is unreliable (e.g., "120 minus 200… 180"), so re-derive it from the code.
  - `execl` replaces the image, so any statement after a successful `execl` never runs.
  - **The midsem will be "much more complicated"; practice many of these.**
- **PID facts:**
  - Unique for the process's lifetime; used for all signalling.
  - Range **2…32767** by default on 32-bit; up to **4,194,303** on 64-bit; set in `/proc/sys/kernel/pid_max`.
  - **PID 0 = swapper/sched (idle)**, **PID 1 = init**. Killing init takes the whole tree down (`init 0` = shutdown).
- **PID search:** next-fit, circular from the last allocated PID. That usually gives a larger PID than the parent's, but not always.
- **pidmap bitmap:** finding the next free PID is ~O(1) bit operations.
- **Zombies still hold their PID** and count toward per-user limits. Reap your children. This is also one reason even servers get rebooted occasionally.
- **Tangents (skip for the exam):** datacenter redundancy (N+1 UPS, active-active firewalls, dual ISPs, ring fiber, splicing).
- PCBs are never swapped (again). Placement vs replacement (again). Formatting only clears metadata; overwrite to erase.
- **`real_parent` vs `parent`:**
  - `real_parent` = the creator (or init if the creator is gone).
  - `parent` = who receives SIGCHLD.
  - They differ only under **ptrace** (a debugger). They're the same 99% of the time.
- **Family links:** the parent points to its **first child and last child**. Siblings are doubly linked (next/prev), with the list closing back at the parent. That's enough to traverse the whole tree with fixed-size structs.
- **Group syscall assignment:** 12 problems involving kernel process-tree traversal; he runs a viva and asks for each member's contribution; due after the midsem.

**Read**
- **T1:**
  - 3.3.1: Linux process tree figure and `pstree`.
  - 3.3.2.
  - 20.4.
- **ULK Ch 3:** "Identifying a Process" (PID, pidmap, pid_max), "**Relationships Among Processes**" (real_parent, parent, children, sibling), "Process 0 and Process 1". Silberschatz barely covers these.

**Transcript fixes:** "LC / L sieve" → else-if · "exccl" → execl · "pplp" → PPID · "2132767" → 2 … 32767 · "sketch / scan" → sched (swapper) · "p-trace" → ptrace.

---

### Lec 17: `fork()` internals: file tables, COW, `do_fork()` step by step; inode pointer arithmetic

**Outline**
- **His pace note:** he wants **3 hours of self-study after each lecture**. The PPTs only give the flow, so the lecture content (i.e., these transcripts) is the real material. The midsem will be hard if you start a week before.
- **fork** duplicates the process and creates a new entry in the system-wide process table. The main differences from the parent are the PID and the file-table bookkeeping.
- **File tables:**
  - The per-process fd table (0/1/2 = std streams; **offset per process**).
  - The **system-wide open-file table** (one entry per file; **open count**).
  - fork **increments the open count** for every inherited file (unless a file restricts its number of openers).
  - **Threads share the per-process table, so there's no count increment.** This is a key difference between a process and a thread.
  - `fclose` really closes only when the count reaches 0.
- **COW** (recap): applies to data only; code is read-only and always shared.
- **fork and clone both go through `do_fork()`** in `kernel/fork.c`, with different flags.
- **UNIX fork steps (Stallings' list):**
  1. Allocate a process-table slot.
  2. Assign a unique PID.
  3. Copy the parent's image **except shared memory**.
  4. Increment the counts of open files.
  5. Put the child in ready-to-run.
  6. Return the child's PID to the parent and 0 to the child.

  Then the dispatcher does one of three things: return to the parent, switch to the child, or switch to another process entirely.
- **Who runs first:**
  - On a **uniprocessor with equal priority, the child runs first** (it's queued first), with half the parent's remaining quantum.
  - Otherwise it depends on priority and core.
  - Often *neither* runs next, because someone else is at the head of the queue.
  - **fork returns twice.** −1 means no child was created.
- **`do_fork()` steps** (ULK / Linux 2.2-era, as on his slides):
  1. `CLONE_PID` check. Only PID 0 has a null parent.
  2. `alloc_task_struct()` gets an **8 KB area (task_struct + kernel-mode stack)**. **If kernel memory can't supply 8 KB, fork fails even if user memory is free.**
  3. Copy the parent's descriptor.
  4. Resource checks: per-user process limits (**soft limit** → warning; **hard limit** → blocked until an admin intervenes; fork bombs on servers); `NR_TASKS` / tasks reserved for root; `find_empty_process`.
  5. Flags:
     - clear `PF_SUPERPRIV` (superuser privilege isn't inherited automatically)
     - clear `PF_USEDFPU` (the FPU is a separate slow unit, not assumed used)
     - clear `PF_PTRACED`
     - set `PF_FORKNOEXEC` (the child hasn't exec'd yet)
     - `PF_VFORK` if applicable
  6. Increment kernel-module reference counts.
  7. `get_pid()`: next free PID from the remembered last one.
  8. Set the fields that can't be inherited.
  9. `copy_files`, `copy_fs`, **`copy_sighand`** (handlers are copied; the *mask* can differ, e.g. per thread), **`copy_mm`** (COW).
  10. `copy_thread`: initialize the child's kernel stack from the saved registers.
  11. **`SET_LINKS`**: insert into the process list.
  12. **`hash_pid`**: insert into the PID hash table (chains are doubly linked circular lists). Only a small subset of PIDs are in use, so hash rather than scan.
- **"Make the common case fast":**
  - Hashing.
  - Page size: 4 KB normally, huge pages (~4 MB) for multimedia and big data, giving smaller page tables.
  - **UNIX inode:** 10 direct + single + double + triple indirect pointers, with 4 KB blocks and 4-byte pointers (1K per block) → **40 KB + 4 MB + 4 GB + 4 TB**. Most files are small, so they're served by the direct pointers (80/20).
- **Next:** AOS proper, i.e., distributed systems ("hundreds of **Lamport's** algorithms").

**He asked / stressed**
- "What happens to open files on fork vs on thread creation?"
- "Why can fork fail when memory is free?" (Kernel 8 KB.)
- "Does the child inherit superuser?"
- "Who runs first, and under what conditions?"
- The inode max-file-size arithmetic.

**Read**
- **T1:**
  - 3.3.1.
  - **4.6.1: fork/exec semantics with threads**.
  - **4.7.2: Linux threads, `clone()` flags** (CLONE_FS/VM/SIGHAND/FILES). This is his process-vs-thread file-table point.
  - 10.3: COW.
  - **13.1.2: per-process vs system-wide open-file tables**.
  - 14.2.
  - **14.4.3: the UNIX inode combined scheme.** Silberschatz draws **12** direct pointers; he used **10**. Use his number in his exam.
  - 10.9.2: page size.
  - 20.4.
- **ULK Ch 3:** "Creating Processes: `clone()`, `fork()`, `vfork()`", `do_fork()`, `copy_process()`.
- **Stallings 3.6:** the fork step list, verbatim.

**Transcript fixes:** "four / port" → fork · "2004" → do_fork · "sketch / swapper" → sched/swapper · "nr underscore task underscore min" → NR_TASKS / MIN_TASKS_LEFT_FOR_ROOT · "pf underscore use the FPU" → PF_USEDFPU · "excc / EFCC" → exec · "W linked" → doubly linked · "piles" → files · "crippling" → triple indirect · "lamborghs" → Lamport's · "nice … plus 90" → +19. **Ignore everything after the inode section** (student chatter).

---

## 2. Consolidated reading list for Silberschatz 10e

This is the list above, deduplicated and in chapter order, so you can read the book front to back. ★ marks sections where he spent a lot of time or asked the class directly.

| Ch | Sections | Lectures |
|---|---|---|
| 1 Introduction | 1.2.1–1.2.3 · ★1.3.1–1.3.3 · 1.4.1 · ★1.4.2 · ★1.4.3 · 1.5.5 · 1.7 · 1.8 | 3, 7 |
| 2 OS Structures | ★2.3.1–2.3.3 · 2.5 · ★2.8.1–2.8.5 · 2.9.1 | 4, 5, 6, 8, 10 |
| 3 Processes | ★3.1.1–3.1.3 (+ task_struct sidebar) · 3.2.1–★3.2.3 · ★3.3.1–3.3.2 · 3.8.2 · **Ch 3 fork-count exercises** | 5, 7–9, 11–17 |
| 4 Threads | 4.4.1 · ★4.6.1 · ★4.7.2 | 6, 17 |
| 5 CPU Scheduling | 5.1.1–5.1.4 · ★5.3.2 · 5.3.3–5.3.4 · ★5.5.1–5.5.5 · ★5.7.1 | 3, 10, 12, 13 |
| 9 Main Memory | ★9.1.1–9.1.5 · ★9.2.1–9.2.3 · ★9.3.1–9.3.3 · 9.5 · ★9.6.1 | 5, 8, 9, 11 |
| 10 Virtual Memory | 10.2.1 · ★10.3 · 10.4.1 · ★10.6 · 10.9.2 · 10.9.5 | 5, 6, 9, 11, 17 |
| 11 Mass Storage | 11.1 · 11.5.1 · ★11.6 | 5, 10, 11 |
| 12 I/O | 12.2.3–12.2.4 · 12.3.1 · 12.4.2–12.4.3 | 4, 7, 10 |
| 13 FS Interface | ★13.1.2 · 13.3 | 4, 13, 17 |
| 14 FS Implementation | 14.2 · 14.4.3 · 14.6 | 4, 17 |
| 17 Protection | 17.3 | 7 |
| 18 Virtual Machines | 18.4–18.5 | 7 |
| 20 Linux | 20.2–20.5 | 4, 5, 9, 12, 16, 17 |

**Not in Silberschatz. Read these for the listed lectures:**

| Source | What | Lectures |
|---|---|---|
| **T2** Singhal & Shivaratri **16.1–16.5** | Tightly/loosely coupled, UMA/NUMA/NORMA, interconnects, caching and coherence | 3 |
| **Stallings Ch 3** (3.2, 3.3, 3.5, 3.6) | Suspend and 7-state model; the 3 PCB categories; mode vs process switch; UNIX SVR4 states; fork steps | 8, 11, 13, 17 |
| **R7 ULK Ch 3** | Linux task states, pidmap/pid_max, real_parent/parent/children/sibling, process 0/1, `do_fork`/`copy_process` | 12, 13, 16, 17 |
| **R7 ULK Ch 7** | O(1) static/dynamic priority, base-quantum formula, FIFO/RR | 9, 10, 13 |
| **R7 ULK Ch 2 and 10** | Linux segmentation (GDT/LDT); `int 0x80` handler, parameter passing, pointer verification | 4, 8, 10 |
| Kernel source | `include/linux/sched.h` (task states, `task_struct`), `arch/x86/entry/syscalls/syscall_64.tbl`, `kernel/fork.c` | 9, 12, 13, 17 |

Stallings isn't on the handout, but his PCB wording, 7-state figure, SVR4 diagram and fork list match it word for word. If you can't get it, the lecture outlines above cover what he said.

---

## 3. Where his lecture differs from the textbook (give his answer in his exam)

1. **NUMA.** He defines NUMA through the cache hierarchy (your own L1 vs another core's L1, then L2/L3) and says "UMA is never used." Silberschatz 1.3.2 and T2 16.3 define NUMA as memory local to each CPU/node and present UMA/SMP as a real design. Know both, and lead with his.
2. **TLB miss = OS exception + page-table walk + re-executed instruction** (so 1000 accesses with 100 misses = 1100 TLB accesses). That's the software-managed-TLB model (e.g., MIPS). x86 walks the page table in hardware, and Silberschatz's EAT formula doesn't count the retry. He explicitly said "textbooks get this wrong," so use his counting.
3. **Inode direct pointers: he used 10**; Silberschatz Fig 14.8 uses 12.
4. **Priority and quantum formulas are the O(1) scheduler's**; Silberschatz 5.7.1 centres on CFS. Know the formula *and* CFS.
5. **i3/i5/i7/i9 = hyper-threading/turbo combinations** is his own framing and isn't in any text. It's historically loose, so reproduce it only if he asks.
6. **"Only hard and symbolic links exist; there's no such thing as a soft link."** Use his terminology.
7. **Child gets half the parent's *remaining* quantum and runs first (on a uniprocessor with equal priority).** This is 2.6-era Linux behaviour; Silberschatz only says the order is unspecified.
8. **The 7-state and SVR4 models aren't in Silberschatz** (see §2).

---

## 4. Midsem priority (08 Oct 2026, closed book, 25%)

The midsem syllabus is whatever he covers up to then (threads are next). From what's been taught, he returns most often to:

1. **Syscall mechanism end to end:** `int 0x80`, EAX, the 5 argument registers, the table ×4/×8, SAVE_ALL/RESTORE_ALL, pointer validation, mode vs context switch, why a syscall can cost you the CPU (Lec 4, 7–10).
2. **Process state models:** 5 → 7 (suspend) → SVR4 → Linux task states, including which transitions exist and *why* (Lec 11, 12).
3. **Fork tracing with variables, `wait`, `exit` and `exec`.** He said the midsem questions will be harder than the class samples, so do the Silberschatz Ch 3 exercises and invent your own (Lec 14, 16).
4. **PCB and priorities:** three PCB categories, PID allocation, the nice → static priority → quantum arithmetic (Lec 13).
5. **fork internals, COW, vfork, file tables, process vs thread** (Lec 9, 17).
6. **Context switch cost, TLB, placement vs replacement** (Lec 9).
7. **Memory protection:** segmentation (LDT/GDT numbers), paging entries, fragmentation (Lec 8).
8. **Layered vs monolithic vs microkernel vs hybrid** (Lec 4, 5).
9. **Multiprocessor basics:** coupling, coherence, NUMA/NORMA, per-core run queues, push/pull migration, affinity (Lec 3, 12).
