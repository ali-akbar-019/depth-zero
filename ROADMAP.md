# depth-zero — OS From Scratch: Complete Roadmap

> Repo: `depth-zero` on GitHub
> Goal: Build your own operating system kernel from scratch
> Start date: Today
> Stack: C, x86 Assembly, QEMU, NASM, GCC cross-compiler

---

## Repo Setup (Do This Right Now)

```bash
mkdir depth-zero && cd depth-zero
git init
mkdir -p phase-1-c phase-2-asm phase-3-theory phase-4-xv6 phase-5-bootloader phase-6-kernel phase-7-drivers phase-8-userspace
touch README.md ROADMAP.md
git add .
git commit -m "init: depth-zero — building an OS from scratch"
```

Then push to GitHub. Every phase has its own folder. Every project you finish = a commit.

---

## Tools to Install (WSL2/Linux)

```bash
sudo apt update
sudo apt install -y \
  build-essential \
  nasm \
  qemu-system-x86 \
  gdb \
  gcc \
  gcc-multilib \
  binutils \
  make \
  git \
  xorriso \
  grub-pc-bin \
  mtools
```

If on Windows → install WSL2 first, then Ubuntu, then run the above.

---

---

# PHASE 1 — C Language Mastery
**Duration:** 3 weeks
**Folder:** `phase-1-c/`
**Why:** The kernel is written in C. You must own it completely.

---

## What to Learn

### 1. Pointers & Memory
- What a pointer is, pointer arithmetic, pointer to pointer
- Stack vs Heap (malloc/free)
- Common bugs: null deref, use-after-free, double-free, buffer overflow
- The `volatile` keyword (used for hardware registers in kernel)

**Read:** K&R "The C Programming Language" — Chapters 1–5
(PDF freely available online, search "K&R C programming language PDF")

### 2. Structs, Unions, Bitfields
- Structs and typedef
- Union (one memory, multiple types)
- Bitfields — you'll use these for page table entries, CPU flags

**Read:** K&R Chapter 6

### 3. Function Pointers
- Declaring and calling function pointers
- Arrays of function pointers (syscall tables work like this)

### 4. Bit Manipulation
- OR to set a bit: `flags |= (1 << n)`
- AND to clear: `flags &= ~(1 << n)`
- XOR to toggle: `flags ^= (1 << n)`
- Check: `if (flags & (1 << n))`

**Read:** Any C bitwise operations tutorial online (30 min)

### 5. Makefiles
- Rules, targets, variables
- `$(CC)`, `$(CFLAGS)`, `all`, `clean`
- Pattern rules: `%.o: %.c`

**Read:** "A Simple Makefile Tutorial" — cs.colby.edu (google it, free)

### 6. Compilation Pipeline
- How `.c` → `.i` → `.s` → `.o` → binary works
- What the linker does
- What a linker script is (you'll write one for your kernel)

**Read:** Any GCC pipeline article (search "gcc compilation stages explained")

### 7. Memory Layout of a C Program
- Text, Data, BSS, Heap, Stack sections
- Why this matters: your kernel defines this layout manually

---

## What to Build (push each to `phase-1-c/`)

| Project | What it is | File |
|---|---|---|
| 1 | Linked list with create, append, delete, print, free | `linked_list.c` |
| 2 | Implement strlen, strcpy, strcmp, strcat from scratch — no string.h | `my_string.c` |
| 3 | Bitmap allocator — track used/free slots using bits, find_free, set, clear | `bitmap.c` |
| 4 | Simple memory allocator — pool-based malloc/free over a static array | `my_malloc.c` |
| 5 | Function pointer dispatch table — array of 5 functions, call by index | `dispatch.c` |

**Milestone commit:** `phase-1 complete: C mastery projects done`

---

---

# PHASE 2 — x86 Assembly
**Duration:** 2–3 weeks
**Folder:** `phase-2-asm/`
**Why:** The bootloader is pure assembly. The kernel entry point is assembly. CPU control registers require assembly. You must be able to read and write it.

---

## What to Learn

### 1. x86-64 Registers
- General purpose: rax, rbx, rcx, rdx, rsi, rdi, rsp, rbp, r8–r15
- Special: rip (instruction pointer), rflags (status flags)
- Sub-registers: rax → eax → ax → al/ah

### 2. NASM Syntax (Intel style)
- `mov dst, src` (destination first — Intel syntax)
- Data: `mov`, `push`, `pop`
- Arithmetic: `add`, `sub`, `mul`, `div`, `inc`, `dec`
- Bitwise: `and`, `or`, `xor`, `shl`, `shr`
- Compare & jump: `cmp`, `je`, `jne`, `jl`, `jg`, `jmp`
- Memory access with brackets: `mov rax, [rbx]` means "read from address in rbx"

### 3. Stack & Calling Convention
- How `call` and `ret` work (push/pop rip)
- Stack frame setup: push rbp, mov rbp rsp, sub rsp N
- Linux x86-64 calling convention: args in rdi, rsi, rdx, rcx, r8, r9 — return in rax

### 4. Sections in NASM
- `.text` — code
- `.data` — initialized data
- `.bss` — uninitialized data (zero'd)
- `global` and `extern` for linking with C

### 5. Mixing C and Assembly
- How to call ASM functions from C (`extern void my_func()`)
- How to call C functions from ASM (`extern printf`)
- Why you need this: kernel uses both

**Resource:** "x86 Assembly Guide" — cs.virginia.edu (google it, free, short and good)
**Resource:** NASM official documentation — nasm.us/doc (reference, not read-through)

---

## What to Build (push each to `phase-2-asm/`)

| Project | What it is | File |
|---|---|---|
| 1 | Hello World in pure NASM (Linux syscall, no libc) | `hello.asm` |
| 2 | Loop that adds 1 to 100 in ASM, prints result | `sum.asm` |
| 3 | ASM function called from C — add(a, b) returns a+b | `add_func.asm` + `main.c` |
| 4 | Read a string from stdin in pure ASM | `read_input.asm` |
| 5 | Implement strlen in pure ASM | `strlen.asm` |

**Milestone commit:** `phase-2 complete: x86 assembly basics done`

---

---

# PHASE 3 — OS Theory (No Coding)
**Duration:** 2 weeks
**Folder:** `phase-3-theory/` (put your notes here as .md files)
**Why:** You need the mental model before writing kernel code or nothing makes sense.

---

## What to Learn & Where

### 1. How a Computer Boots
- BIOS/UEFI → MBR → Bootloader → Kernel
- What happens in the first milliseconds after power on
- What the bootloader's job is

**Read:** OSDev Wiki "Boot Sequence" — wiki.osdev.org/Boot_Sequence

### 2. CPU Privilege Levels (Rings)
- Ring 0 = kernel (full hardware access)
- Ring 3 = user programs (restricted)
- How switching between rings works (syscalls, interrupts)

**Read:** OSDev Wiki "Security" and "Privilege Levels" pages

### 3. Memory Management
- Physical vs Virtual memory
- Paging — how virtual addresses map to physical
- Page tables (4-level on x86-64: PML4 → PDPT → PD → PT)
- TLB — translation lookaside buffer

**Read:** OSTEP Chapter 13–22 (free at ostep.org — "Memory Virtualization" section)

### 4. Interrupts & Exceptions
- Hardware interrupts (keyboard press, timer tick)
- Software interrupts (syscalls)
- Exceptions (divide by zero, page fault)
- IDT — Interrupt Descriptor Table
- PIC — Programmable Interrupt Controller

**Read:** OSDev Wiki "Interrupts" and "IDT" pages

### 5. Process Scheduling
- What a process is vs a thread
- Context switching — saving/restoring registers
- Scheduling algorithms: Round Robin, Priority

**Read:** OSTEP Chapter 4–9 (free at ostep.org — "Virtualization/CPU" section)

### 6. File Systems
- Inodes, blocks, directories
- FAT32 basics (you'll implement a simple one later)
- VFS (Virtual File System) layer

**Read:** OSTEP Chapter 39–40 (Persistence section)

### 7. System Calls
- How a user program asks the kernel to do something
- syscall instruction, syscall table, arguments in registers

**Read:** OSDev Wiki "System Calls" page

### 8. GDT & Segmentation
- Global Descriptor Table — describes memory segments
- Segmentation is mostly legacy on x86-64 but GDT still required
- You'll set this up in your kernel

**Read:** OSDev Wiki "GDT" and "GDT Tutorial"

---

## Notes to Write (put in `phase-3-theory/`)

Write a short `.md` note for each topic above in your own words. This forces you to actually understand it and becomes your personal reference.

**Milestone commit:** `phase-3 complete: OS theory notes written`

---

---

# PHASE 4 — Study xv6 (Read Real Kernel Code)
**Duration:** 2 weeks
**Folder:** `phase-4-xv6/` (clone xv6 here, add your notes)
**Why:** xv6 is MIT's teaching OS. It's a real, working kernel in ~10,000 lines of clean C. Reading it before writing your own is the smartest thing you can do.

---

## What to Do

### Step 1: Clone and Run xv6
```bash
cd phase-4-xv6
git clone https://github.com/mit-pdos/xv6-public.git
cd xv6-public
make
make qemu-nox   # runs in terminal without graphics
```

You'll see a shell boot. Type `ls`. You just ran a real OS.

### Step 2: Read These Files in This Order

| File | What it teaches |
|---|---|
| `bootasm.S` | The bootloader in assembly — first code that runs |
| `bootmain.c` | Loads kernel from disk into memory |
| `entry.S` | Kernel entry point — switches to protected mode |
| `main.c` | Kernel main() — initializes everything |
| `vm.c` | Virtual memory / paging setup |
| `kalloc.c` | Physical memory allocator |
| `trap.c` | Interrupt and exception handling |
| `proc.c` | Process creation and scheduling |
| `syscall.c` | System call dispatch table |
| `sysfile.c` | File system syscalls |
| `fs.c` | File system implementation |
| `ide.c` | Disk driver |
| `console.c` | Console/keyboard driver |

### Step 3: For Each File, Write Notes
In `phase-4-xv6/notes/` create one `.md` per file explaining what you understood.

**Resource:** xv6 book — pdos.csail.mit.edu/6.828/2012/xv6/book-rev7.pdf (free, explains every line)

**Milestone commit:** `phase-4 complete: xv6 studied and notes written`

---

---

# PHASE 5 — Bootloader
**Duration:** 2–3 weeks
**Folder:** `phase-5-bootloader/`
**Why:** This is the first code YOU write that runs on bare metal. No OS beneath you.

---

## What to Learn First
- How BIOS loads the MBR (first 512 bytes of disk)
- BIOS signature: last two bytes must be `0x55 0xAA`
- Real Mode — 16-bit mode CPU starts in
- Protected Mode — 32-bit mode you switch to
- Long Mode — 64-bit mode (final goal)
- A20 Line — a hardware quirk you must enable for full memory access

**Read:** OSDev Wiki "Bootloader" and "Real Mode" pages
**Read:** OSDev Wiki "Protected Mode" page
**Read:** OSDev Wiki "Long Mode" page

---

## What to Build (in order)

### Project 1: Print "Hello" from bootloader
Write a 512-byte MBR in NASM that uses BIOS interrupt 0x10 to print text.
File: `phase-5-bootloader/boot1/boot.asm`

Test: `nasm -f bin boot.asm -o boot.bin && qemu-system-x86_64 -drive format=raw,file=boot.bin`

### Project 2: Enable A20 line
Add A20 enabling code to your bootloader (required to access memory above 1MB).
File: `phase-5-bootloader/boot2/boot.asm`

Read: OSDev Wiki "A20 Line"

### Project 3: Switch to Protected Mode (32-bit)
Load a GDT, set the PE bit in CR0, far jump to 32-bit code.
File: `phase-5-bootloader/boot3/boot.asm`

Read: OSDev Wiki "Protected Mode" tutorial

### Project 4: Load kernel from disk
Read sectors from disk using BIOS int 0x13, copy kernel into memory.
File: `phase-5-bootloader/boot4/`

### Project 5: Switch to Long Mode (64-bit)
Set up page tables, enable PAE, set EFER MSR, enable paging.
File: `phase-5-bootloader/boot5/boot.asm`

Read: OSDev Wiki "Setting Up Long Mode"

**Milestone commit:** `phase-5 complete: bootloader enters 64-bit long mode`

---

---

# PHASE 6 — The Kernel
**Duration:** 3–4 months (ongoing, most of your time)
**Folder:** `phase-6-kernel/`
**Why:** This is the actual OS. Everything below was preparation.

---

## Step 1: Kernel Entry Point
- Bootloader jumps here
- Set up stack, call kernel main() in C
- Files: `kernel/boot/entry.asm`, `kernel/main.c`

Read: OSDev "Bare Bones" tutorial — wiki.osdev.org/Bare_Bones

### Step 2: VGA Text Output
- Write directly to memory at 0xB8000
- Implement `kprintf()` — your kernel's print function
- Files: `kernel/drivers/vga.c`, `kernel/lib/printf.c`

Read: OSDev Wiki "VGA Text Mode"

### Step 3: GDT Setup
- Define code and data segments
- Load with `lgdt` instruction
- Files: `kernel/cpu/gdt.c`, `kernel/cpu/gdt.asm`

Read: OSDev Wiki "GDT Tutorial"

### Step 4: IDT & Interrupt Handling
- Set up Interrupt Descriptor Table
- Handle CPU exceptions (0–31): divide by zero, page fault, etc.
- Handle hardware IRQs (32–47): timer, keyboard
- Files: `kernel/cpu/idt.c`, `kernel/cpu/idt.asm`, `kernel/cpu/isr.asm`

Read: OSDev Wiki "IDT", "Interrupts", "ISR"

### Step 5: PIC Setup
- Remap the PIC (IRQs conflict with CPU exceptions by default)
- Enable/disable specific IRQs
- Files: `kernel/cpu/pic.c`

Read: OSDev Wiki "8259 PIC"

### Step 6: Physical Memory Manager
- Detect available RAM using multiboot info or BIOS E820
- Implement a page frame allocator — track which 4KB pages are free
- Files: `kernel/mm/pmm.c`

Read: OSDev Wiki "Memory Map (x86)", "Physical Memory Allocation"

### Step 7: Virtual Memory / Paging
- Set up 4-level page tables (PML4)
- Map kernel into virtual address space
- Implement `map_page(phys, virt, flags)`
- Handle page faults
- Files: `kernel/mm/vmm.c`, `kernel/mm/paging.asm`

Read: OSDev Wiki "Paging", "Page Tables"

### Step 8: Heap Allocator
- Implement `kmalloc(size)` and `kfree(ptr)` for kernel use
- Simple first-fit or slab allocator
- Files: `kernel/mm/heap.c`

Read: OSDev Wiki "Heap"

### Step 9: Keyboard Driver
- Handle IRQ1 (keyboard interrupt)
- Read scancode from port 0x60
- Convert to ASCII
- Files: `kernel/drivers/keyboard.c`

Read: OSDev Wiki "PS/2 Keyboard"

### Step 10: PIT Timer
- Configure Programmable Interval Timer (IRQ0)
- Track system uptime (ticks)
- Use for scheduling later
- Files: `kernel/drivers/timer.c`

Read: OSDev Wiki "Programmable Interval Timer"

### Step 11: Process & Scheduler
- Define a process struct (pid, state, registers, stack, page table)
- Implement context switching in assembly (save/restore all registers)
- Round-robin scheduler
- Files: `kernel/proc/process.c`, `kernel/proc/scheduler.c`, `kernel/proc/switch.asm`

Read: OSDev Wiki "Context Switching", OSTEP Chapter 6

### Step 12: System Calls
- Define syscall numbers
- Implement syscall handler (interrupt 0x80 or `syscall` instruction)
- First syscalls: write, exit, getpid
- Files: `kernel/syscall/syscall.c`

Read: OSDev Wiki "System Calls"

### Step 13: VFS Layer
- Abstract file system interface: open, close, read, write
- Files: `kernel/fs/vfs.c`

### Step 14: FAT32 or Simple FS
- Implement a basic file system
- Or: implement a RAM disk with a simple flat FS
- Files: `kernel/fs/fat32.c` or `kernel/fs/ramfs.c`

Read: OSDev Wiki "FAT", fatgen103.doc (Microsoft FAT spec, free online)

### Step 15: Shell
- A basic command-line shell running in user space (or kernel space early on)
- Commands: ls, echo, clear, run a program
- Files: `kernel/shell/shell.c`

**Milestone commits after each step above.**

---

---

# PHASE 7 — Drivers & Hardware
**Duration:** 1–2 months
**Folder:** `phase-7-drivers/`

## What to Build
- ATA/IDE disk driver (read/write actual disk sectors)
- Serial port driver (for debugging output)
- Mouse driver (PS/2)
- VESA/framebuffer graphics (switch from text mode to pixel mode)
- Basic sound (PC speaker beep)

Read: OSDev Wiki has a dedicated page for every single one of these.

---

---

# PHASE 8 — User Space
**Duration:** Ongoing
**Folder:** `phase-8-userspace/`

## What to Build
- ELF loader — load and execute ELF binaries from your FS
- User space C library (libc) — printf, malloc, string functions
- First user space program — Hello World from ring 3
- Fork & exec syscalls
- Pipes & inter-process communication

Read: OSDev Wiki "ELF", "Creating a C Library"

---

---

# Master Resource List

## Books (all free online)
| Book | Where | When to read |
|---|---|---|
| K&R — The C Programming Language | Search "K&R C PDF" | Phase 1 |
| OSTEP — Operating Systems: Three Easy Pieces | ostep.org | Phase 3 |
| xv6 book (MIT) | pdos.csail.mit.edu/6.828/2012/xv6/book-rev7.pdf | Phase 4 |
| Intel x86-64 Manual Vol. 1–3 | intel.com/sdm | Reference during Phase 5–6 |

## Websites
| Site | Use |
|---|---|
| wiki.osdev.org | Your bible — reference for everything |
| os.phil-opp.com | Rust OS blog — read for concepts even if not using Rust |
| github.com/cfenollosa/os-tutorial | Step-by-step OS tutorial — supplement |
| lowlevel.eu | Low-level C and ASM articles |

## Tools Reference
| Tool | Purpose |
|---|---|
| `nasm` | Assemble `.asm` files |
| `gcc` | Compile C kernel code |
| `ld` | Linker — combines object files |
| `qemu-system-x86_64` | Run your OS in a VM — no rebooting your PC |
| `gdb` + `qemu -s -S` | Debug your kernel |
| `objdump` | Inspect compiled binaries |
| `readelf` | Inspect ELF files |
| `xorriso` | Create bootable ISO |
| `make` | Build system |

---

---

# Git Commit Discipline

Every time you finish something, commit it. Name commits clearly:

```
phase-1: linked list in C
phase-1: custom string functions
phase-2: hello world in NASM
phase-2: C + ASM calling convention
phase-3: notes on paging and virtual memory
phase-5: bootloader prints hello from real mode
phase-5: successfully entered protected mode
phase-6: VGA text output working
phase-6: GDT and IDT set up
phase-6: keyboard interrupts working
```

Your GitHub will show the entire journey. That's the point of depth-zero.

---

# Realistic Timeline

| Phase | Duration |
|---|---|
| Phase 1 — C | 3 weeks |
| Phase 2 — Assembly | 2–3 weeks |
| Phase 3 — Theory | 2 weeks |
| Phase 4 — xv6 | 2 weeks |
| Phase 5 — Bootloader | 2–3 weeks |
| Phase 6 — Kernel | 3–4 months |
| Phase 7 — Drivers | 1–2 months |
| Phase 8 — User Space | Ongoing |

**Total to something impressive: ~8–12 months of consistent work.**

You don't need to finish to be impressive. Phase 6 step 8 (working kernel with memory management) is already a portfolio piece most devs never reach.

---

*Start today. Create the repo. Install the tools. Open K&R.*
