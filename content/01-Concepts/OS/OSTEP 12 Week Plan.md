# OSTEP Mastery Plan: 12 Weeks + Prep Week

**Book:** Operating Systems: Three Easy Pieces (OSTEP) v1.10
**Lectures:** CS-537 Spring 2018 (Remzi Arpaci-Dusseau). Chosen because the project discussion videos (D1, D2, D3, D5, D11, D12) are there.
**Pace:** 12-15 hours/week
**Goal:** Real understanding of virtualization, concurrency, and persistence, backed by working C code, for Backend / DevOps / Cloud work.

> A "video" below means one Part of a lecture (usually 20-40 min). Watch at 1.25x-1.5x speed.

---

## 0. The Rules

1. **Loop for every chapter:** Lecture video -> read the chapter -> type and run the code snippets -> homework -> 5-line summary in your own words (in `notes/`).
2. **Homework is not optional** for the core chapters.
3. **Time-box projects:** stuck on one bug for more than 2 hours? Read the reference solution, understand it, close it, redo it from scratch.
4. **Linux lens:** every week, run one real command that matches the topic and write down what you saw.
5. **Git repo from day 1:** `ostep/` with `notes/`, `homework/`, `projects/`. Commit after every session.
6. **Anki:** one card per definition and per hint/tip box. 15 min/day.
7. **End-of-week check:** explain the topic out loud, no notes, 5 minutes. If you can't, repeat the weak part before moving on.
8. **Do not go below 10-11 weeks.** If you fall behind, cut the skim chapters, never the homework or the core projects.

### Weekly rhythm (example)

| Session | Time | Task |
|---|---|---|
| 1 | 2 h | Lecture videos + start the chapter |
| 2 | 2 h | Finish chapter + type code snippets |
| 3 | 3 h | Homework |
| 4 | 3-4 h | Project work (or more homework) |
| 5 | 1-2 h | Summary notes, Anki, Linux lens, end-of-week check |

---

## Lectures at a glance

| Week | Videos to watch | Count | Load |
|---|---|---|---|
| 0 | D1 | 1 | light |
| 1 | L1 P1-3, L2 P1, D3 | 5 | medium |
| 2 | L2 P2-3, L3 P1 | 3 | light |
| 3 | D2, D5 | 2 | light |
| 4 | L3 P2-3, L4 P1 | 3 | light |
| 5 | L4 P2-3, L5 P1 | 3 | light |
| 6 | L5 P2 | 1 | very light |
| 7 | L6 P1-3, L7 P1 | 4 | medium |
| 8 | L7 P2-3, L8 P1-2, L9 P1-2 | 7 | heavy |
| 9 | L10 P1-3, D11, D12 | 5 | medium |
| 10 | L11 P1-4, L12 P1-3 | 7 | heavy |
| 11 | L13 P1-3, L14 P1-2 | 5 | medium |
| 12 | none | 0 | review |

Total: about 46 videos out of the full ~60 (skipped: D7, D10, L14 P3-4, extra review lectures). Roughly 18-20 hours of video, spread over 12 weeks.

---

## 1. Week 0: Setup + C Bridge (4-5 days)

You know C++, so only learn what is *different* in C.

**Videos (1):** D1 (project intro, optional)

### Checklist
- [ ] Ubuntu ready (WSL2 or VM)
- [ ] Installed: `gcc`, `gdb`, `make`, `valgrind`, `git`, `python3`, `qemu`
- [ ] Cloned `ostep-homework` and `ostep-projects`
- [ ] Created your own `ostep/` Git repo with `notes/`, `homework/`, `projects/`
- [ ] C: pointers, arrays, `char *` vs `char[]`
- [ ] C: null-terminated strings (`strlen`, `strcpy`, `strncpy`, `strcmp`)
- [ ] C: `malloc` / `calloc` / `realloc` / `free`, and checking `NULL`
- [ ] C: `struct` + functions instead of classes
- [ ] C: `printf`, `fprintf(stderr, ...)`, `argc` / `argv`
- [ ] C: file I/O (`fopen`, `getline`, `fread`, `fwrite`, `fclose`)
- [ ] Makefile basics ("Getting Started" and "Targets" only)
- [ ] `gdb`: `break`, `run`, `next`, `step`, `print`, `bt`
- [ ] `valgrind ./a.out`: caused and read a leak report on purpose
- [ ] **Litmus test:** `initial-utilities` (wcat, wgrep, wzip, wunzip) done in about 2 hours of writing time
- [ ] Anki deck created

**Resources:** *Modern C* (Gustedt, free, early chapters), CS50 Manual Pages, `cdecl`. Avoid GeeksforGeeks / TutorialsPoint for C.
**Linux lens:** compile with `-Wall -Wextra -g`, break something on purpose (double free, out of bounds), read the error.

---

# PART 1: VIRTUALIZATION

## Week 1: Processes and CPU Virtualization
**Videos (5):** L1 P1, P2, P3 | L2 P1 | D3 (shell project)
**Chapters:** 1, 2, 3 (Dialogues), 4 (Processes), 5 (Process API), 6 (Direct Execution)

### Checklist
- [ ] Watched all 5 videos
- [ ] Read Ch 1-6
- [ ] Typed and ran the `fork` / `exec` / `wait` examples from Ch 5
- [ ] Homework `cpu-intro` (process-run.py)
- [ ] Homework `cpu-api` (fork.py + the C code questions)
- [ ] Started project `processes-shell`
- [ ] Linux lens: `ps -ef`, `pstree`, `strace -f ls`, `cat /proc/<pid>/status`; found fork/exec/wait in the strace output
- [ ] 5-line summaries + Anki cards
- [ ] **Check:** explain what happens from typing `ls` in a shell to seeing output (fork, exec, wait, why the shell is a normal program)

## Week 2: CPU Scheduling
**Videos (3):** L2 P2, P3 | L3 P1
**Chapters:** 7 (Scheduling), 8 (MLFQ), 9 (Lottery), 10 (Multi-CPU, **skim**), 11 (Summary)

### Checklist
- [ ] Watched all 3 videos
- [ ] Read Ch 7, 8, 9, 11 (Ch 10 skimmed)
- [ ] Homework `cpu-sched` (scheduler.py)
- [ ] Homework `cpu-sched-mlfq`
- [ ] Homework `cpu-sched-lottery`
- [ ] **Finished `processes-shell`** (`exit`, `cd`, `path`, redirection `>`, parallel `&`)
- [ ] Linux lens: `nice`, `renice`, `top`/`htop`, `taskset`, `chrt -p <pid>`
- [ ] Summaries + Anki
- [ ] **Check:** compare FIFO, SJF, STCF, RR, MLFQ by turnaround and response time; explain why MLFQ can be gamed

## Week 3: First Contact with xv6
**Videos (2):** D2 (add a syscall to xv6), D5 (xv6 scheduler)
**Reading:** xv6 book (Ch 0-1) and the annotated xv6 guide up to "System Calls: Processes"

### Checklist
- [ ] Watched D2 and D5
- [ ] Read the xv6 reading above
- [ ] xv6 builds and boots in `qemu` (use a separate `src/` per project: `mkdir src && git clone https://github.com/mit-pdos/xv6-public src`)
- [ ] Project `initial-xv6` (new syscall) passes
- [ ] Project `scheduling-xv6-lottery` passes
- [ ] Linux lens: `strace -c ls` (count the syscalls of a tiny command)
- [ ] Summaries + Anki
- [ ] **Check:** trace a syscall step by step (trap, kernel stack, handler, return)

## Week 4: Address Spaces and Memory Mechanisms
**Videos (3):** L3 P2, P3 | L4 P1
**Chapters:** 12 (Dialogue), 13 (Address Spaces), 14 (Memory API), 15 (Address Translation), 16 (Segmentation), 17 (Free Space Management)

### Checklist
- [ ] Watched all 3 videos
- [ ] Read Ch 12-17
- [ ] Wrote a C program that prints addresses of stack, heap, code, and a global; ran it several times
- [ ] Homework `vm-intro`
- [ ] Homework `vm-mechanism` (relocation.py)
- [ ] Homework `vm-segmentation`
- [ ] Homework `vm-freespace`
- [ ] Linux lens: `cat /proc/<pid>/maps`, `pmap <pid>`, `ldd`, ASLR (`cat /proc/sys/kernel/randomize_va_space`)
- [ ] Summaries + Anki
- [ ] **Check:** draw a process address space; explain base/bounds, segmentation, external fragmentation

## Week 5: Paging and TLBs
**Videos (3):** L4 P2, P3 | L5 P1
**Chapters:** 18 (Paging), 19 (TLBs), 20 (Advanced Page Tables)

### Checklist
- [ ] Watched all 3 videos
- [ ] Read Ch 18-20
- [ ] Homework `vm-paging`
- [ ] Homework `vm-tlbs`
- [ ] Homework `vm-smalltables`
- [ ] Linux lens: `getconf PAGESIZE`, `/proc/<pid>/smaps`, `/proc/meminfo`, `vmstat 1`
- [ ] Summaries + Anki
- [ ] **Check (important):** translate a virtual address through a 2-level page table with a TLB by hand, no notes; compute page table size for a given config

## Week 6: Swapping + Part 1 Review
**Videos (1):** L5 P2
**Chapters:** 21 (Swapping Mechanisms), 22 (Swapping Policies), 23 (Complete VM Systems), 24 (Summary)

### Checklist
- [ ] Watched L5 P2
- [ ] Read Ch 21-24
- [ ] Homework `vm-beyondphys`
- [ ] Homework `vm-beyondphys-policy` (paging-policy.py: FIFO, LRU, OPT, clock)
- [ ] Redid the weakest homework from Weeks 4-5
- [ ] Linux lens: `free -h`, `vmstat 1` (watch `si`/`so`), `swapon --show`, `sar -B`
- [ ] Reviewed all Part 1 Anki cards
- [ ] **Milestone 1:** explain the full path of `int x = *ptr;` (virtual address -> TLB -> page table -> page fault -> disk -> resume)

---

# PART 2: CONCURRENCY

## Week 7: Threads and Locks
**Videos (4):** L6 P1, P2, P3 | L7 P1
**Chapters:** 25 (Dialogue), 26 (Concurrency and Threads), 27 (Thread API), 28 (Locks)

### Checklist
- [ ] Watched all 4 videos
- [ ] Read Ch 25-28
- [ ] Wrote a racy counter with `pthread`, saw it break, fixed it with a mutex
- [ ] Implemented a spin lock with atomic exchange (C11 atomics or `__sync_lock_test_and_set`)
- [ ] Homework `threads-intro`
- [ ] Homework `threads-api`
- [ ] Homework `threads-locks`
- [ ] Linux lens: `ps -eLf`, `top -H`, context switches in `/proc/<pid>/status`
- [ ] Summaries + Anki
- [ ] **Check:** why `counter++` isn't atomic (3 steps); compare spin lock, ticket lock, park/unpark (futex)

## Week 8: Condition Variables, Semaphores, Bugs (heavy week)
**Videos (7):** L7 P2, P3 | L8 P1, P2 | L9 P1, P2
**Chapters:** 29 (Locked Data Structures), 30 (Condition Variables), 31 (Semaphores), 32 (Concurrency Bugs), 33 (Event-based, **skim**), 34 (Summary)

### Checklist
- [ ] Watched all 7 videos (1.5x speed)
- [ ] Read Ch 29-34 (Ch 33 skimmed)
- [ ] Code: bounded buffer with mutex + 2 condition variables
- [ ] Code: the same bounded buffer with semaphores
- [ ] Code: a deliberate deadlock, then fixed with lock ordering
- [ ] Homework `threads-cv`
- [ ] Homework `threads-sema`
- [ ] Homework `threads-bugs`
- [ ] Linux lens: `gdb -p <pid>` then `thread apply all bt` on a hung program; `valgrind --tool=helgrind`
- [ ] Summaries + Anki
- [ ] **Check:** the 4 deadlock conditions and how to break each; why CVs need `while`, not `if`

## Week 9: MapReduce Project + Start of Persistence
**Videos (5):** L10 P1, P2, P3 | D11 | D12
**Chapters:** 35 (Dialogue), 36 (I/O Devices), 37 (Hard Disk Drives)

### Checklist
- [ ] Watched all 5 videos
- [ ] Read Ch 35-37
- [ ] Homework `file-disks` (disk.py)
- [ ] **Project `concurrency-mapreduce`** (thread pool, work queue, partitioned reduce) passes tests
- [ ] Ran the project under `helgrind` (no errors) and `valgrind` (no leaks)
- [ ] Redid the weakest threads homework
- [ ] Linux lens: `lsblk`, `iostat -x 1`, `cat /sys/block/<dev>/queue/scheduler`
- [ ] Summaries + Anki
- [ ] **Milestone 2:** can explain a thread pool + work queue design without notes
- [ ] **Check:** compute seek + rotation + transfer time for a disk request; explain SSTF vs SCAN

## Week 10: RAID, Files, and File System Implementation (heavy week)
**Videos (7):** L11 P1-4 | L12 P1-3
**Chapters:** 38 (RAID), 39 (Files and Directories), 40 (File System Implementation), 41 (Fast File System)

### Checklist
- [ ] Watched all 7 videos (1.5x speed)
- [ ] Read Ch 38-41
- [ ] Code: C program using `open`, `read`, `write`, `lseek`, `fsync`, `stat`, `readdir`
- [ ] Homework `file-raid` (raid.py)
- [ ] Homework `file-implementation` (vsfs.py)
- [ ] Homework `file-ffs`
- [ ] Linux lens: `stat file`, `ls -i`, `df -i`, `debugfs` or `dumpe2fs` on a loop-device image
- [ ] Summaries + Anki
- [ ] **Check:** draw the on-disk layout (superblock, bitmaps, inodes, data); trace everything `read("/a/b/c")` touches; compare RAID 0/1/4/5

## Week 11: Crash Consistency, Data Integrity, Final Project
**Videos (5):** L13 P1, P2, P3 | L14 P1, P2 (rest skipped)
**Chapters:** 42 (FSCK and Journaling), 43 (LFS, **skim**), 44 (SSDs, **skim**), 45 (Data Integrity), 46 (Summary)

### Checklist
- [ ] Watched all 5 videos
- [ ] Read Ch 42, 45, 46 (Ch 43-44 skimmed)
- [ ] Homework `file-journaling`
- [ ] Homework `file-integrity`
- [ ] (Optional, recommended) Project `filesystems-checker`
- [ ] Linux lens: `fsck -n` on a loop image, `mount | grep ext4`, journal modes (`data=ordered` vs `data=journal`), `sync` vs `fsync`
- [ ] Summaries + Anki
- [ ] **Check:** for a crash between each step of an append (data block, inode, bitmap), say the inconsistency and how fsck vs journaling recovers

## Week 12: Buffer + Consolidation
**Videos (0)**

### Checklist
- [ ] Caught up on anything missed (plan on needing this week)
- [ ] Redid the 2 weakest homeworks from scratch
- [ ] Final summary doc (3-5 pages): virtualization, concurrency, persistence
- [ ] Added section: "How this shows up in Backend / DevOps / Cloud" (containers = namespaces + cgroups over processes, OOM killer, page cache, `fsync` and database durability, thread pools, file descriptor limits)
- [ ] GitHub repo clean, with a README (what you built, how to run it, what you learned)
- [ ] All Anki cards reviewed
- [ ] **Final test:** 10 minutes on each of the three pieces, no notes

---

## 2. Skipped on purpose (come back later)

| Item | Why skipped |
|---|---|
| Ch 47-51 (Distributed, NFS, AFS) | Better learned in a distributed systems course |
| Ch 52-57 (Security) | Read separately when needed |
| `vm-xv6-intro`, `concurrency-xv6-threads` | Heavy kernel work, low payoff for your path |
| x86 assembly, Bomb Lab, Attack Lab | Only for a kernel/security track |
| `initial-reverse` | Extra C practice, only if `initial-utilities` felt hard |
| D7, D10, L14 P3-4, review lectures | Redundant with chapters |

### Level-up (after Week 12)
1. `vm-xv6-intro`, then `concurrency-xv6-threads`
2. Ch 47-51
3. CS:APP "Machine-Level Programming" lectures + Bomb Lab
4. Write a mini `malloc`, or a shell with pipes
5. *The Linux Programming Interface* (Kerrisk) as the next book

---

## 3. Final completion criteria
- [ ] All core homeworks done (cpu, vm, threads, file)
- [ ] Projects done: `initial-utilities`, `processes-shell`, `initial-xv6`, `scheduling-xv6-lottery`, `concurrency-mapreduce` (+ `filesystems-checker` if time)
- [ ] Notes repo on GitHub
- [ ] Can walk through fork/exec, page fault, deadlock, and crash recovery without notes

---

## 4. Resources
- Book: https://pages.cs.wisc.edu/~remzi/OSTEP/
- Homework code: https://github.com/remzi-arpacidusseau/ostep-homework
- Projects: https://github.com/remzi-arpacidusseau/ostep-projects
- xv6: https://github.com/mit-pdos/xv6-public (+ xv6 book and the annotated guide)
- C: *Modern C* (Gustedt), CS50 man pages, `cdecl`
- OSSU OS page (the roadmap this plan is based on): https://github.com/ossu/computer-science
- Homework solutions: search "ostep homework solutions" (only after a real attempt)
- OSDev Wiki

> Homework and project folder names may differ slightly from the repo; check the directory listing when you start each one.
