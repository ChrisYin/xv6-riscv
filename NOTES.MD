# xv6 notes

## Building System

Makefile

## Linker Scripts: Physical Map

### Qemu: virt machine

+ RAM
+ ROM
+ serial connection to keyboard/screen
+ disk

### RAM: Physical Memory

+ KERNBASE: 0x8000_0000
+ PHYTOP: 0x8640_0000
+ Device CSR

## Kernel Organization

+ Type: [Monolithic Kernel]/Micro Kernel
+ Feature: Multi-core

## Initialization

+ bootloader
+ entry.S:_entry
  setting init stack for each cpu
+ start.c:start()
  + delegate interrupt to supervisor mode 
  + disable paging
  + timer interrupt in machine mode
  + prepare mret environment for entering supervisor mode
+ main.c:main()
  + init devices and subsystems
    + console
    + physical page allocator
    + kernel pagetable
    + paging
    + process pagetable
    + trap vectors
    + interrupt 
    + cache
    + file system
  + init first process
  
## Physical Memory

+ Split the memory into pages.
+ Minimal unit is one "page": 4096 bytes.
+ Using list to manage physical memory
+ Remember to use *lock* to protect accssing list

```C
struct {
  struct spinlock lock;
  struct run *freelist;
} kmem;
void*           kalloc(void);
void            kfree(void *);
void            kinit();
```

## Virtual Memory

### PageTable Struct

+ Using a pointer to represent pagetable

```C
typedef uint64 *pagetable_t; // 512 PTEs
//interface
void            kvminit(void);
void            kvminithart(void);
uint64          kvmpa(uint64);
void            kvmmap(uint64, uint64, uint64, int);
int             mappages(pagetable_t, uint64, uint64, uint64, int);
pagetable_t     uvmcreate(void);
void            uvminit(pagetable_t, uchar *, uint);
uint64          uvmalloc(pagetable_t, uint64, uint64);
uint64          uvmdealloc(pagetable_t, uint64, uint64);
int             uvmcopy(pagetable_t, pagetable_t, uint64);
void            uvmfree(pagetable_t, uint64);
void            uvmunmap(pagetable_t, uint64, uint64, int);
void            uvmclear(pagetable_t, uint64);
uint64          walkaddr(pagetable_t, uint64);
int             copyout(pagetable_t, uint64, char *, uint64);
int             copyin(pagetable_t, char *, uint64, uint64);
int             copyinstr(pagetable_t, char *, uint64, uint64);
```

### Kernel Address Space

+ Trampoline
+ Guard Page
+ Kstack 0(Process0)
+ Guard Page
+ Kstack 1(Process1)
+ ...
+ Free Memory
+ Kernel Data
+ Kernel Text
+ I/O Devices

### Process Address Space

+ trampoline
+ trapframe
+ heap
+ stack
+ guardpage
+ data
+ text

## Thread

## Process

## Trap
