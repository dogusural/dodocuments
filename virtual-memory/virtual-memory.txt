Virtual Memory
---------------

The variable address on a C code, such as : 

int a;
printf("%ul",%a);

doesnt correspond to a real address on the RAM. Linux uses virtual look up tables to assign those virtual adresses to real values in either hard drive or RAM.
if we want to run multiple processes under the OS we may run out of RAM pretty fast that is why we need a mechanism to swap the contents out when that portion of the memory is not being utilized in the program.

+---------------------+----------------------+
| Virtual Page Number | Physical Page Number |
+---------------------+----------------------+
|    0xf923a          |      0x1000010       |
+---------------------+----------------------+
|                     |                      |
+---------------------+----------------------+
|                     |                      |
+---------------------+----------------------+

we basically only load the portion we use to the ram and we swap out the parts we do not use currently to the harddrive.This allows us to run many programs than our ram can handle. But bear in mind that if there are too many page faults, the processor needs to swap in and out too many contents all the time thus slowing down the whole system. Especially if SSD is not present in the system.

So the physical page number is just changed with an address on the hard drive whenever the virtual page wants to be swapped out.

When a program is compiled and run, the address space assigned o it as if it was the only thing the cpu has to load in to the memory. So multiple programs basically share the same virtual address space. Different variables in different processes may have the same address but the operating system assigns them to different physical addresses and since every process has its own virtual memory table, they dont collide.

* A process's memory is consecutive in virtual memory space but the physical addresses that they correspond to can be anywhere in the physical address space. Not necessarily in a succession.

* A program cant access another process's memory simply because same virtual addresses correspond to different physical memories. If a process tries to reach a region which doesnt exist in its virtual table OS will give an segmentation fault error and terminate the process to protect the system. 

-----------------------------------------------------------------------------------------------------------------------------------------------------
╔═══════════════╦═══════════════════════════════════════════════════════════════════════╗
║    Elf File   ║                                                                       ║
╠═══════════════╬═══════════════════════════════════════════════════════════════════════╣
║ ELF Header    ║ Metadata stays here.                                                  ║
╠═══════════════╬═══════════════════════════════════════════════════════════════════════╣
║ .text segment ║ all functions turned into machine code including class functions      ║
║               ║ and stored here                                                       ║
╠═══════════════╬═══════════════════════════════════════════════════════════════════════╣
║ .data segment ║ all initialized global and static variables stays here.               ║
╠═══════════════╬═══════════════════════════════════════════════════════════════════════╣
║ .ro segment   ║ constant variables and string literals stay here.                     ║
╠═══════════════╬═══════════════════════════════════════════════════════════════════════╣
║ .bss segment  ║ uninitialized global and static variables and their size this segment ║
║               ║ is zeroized when it is loaded into the memory.                        ║
╠═══════════════╬═══════════════════════════════════════════════════════════════════════╣
║ heap          ║ grows downwards                                                       ║
╠═══════════════╬═══════════════════════════════════════════════════════════════════════╣
║ stack         ║ grows upwards                                                         ║
╚═══════════════╩═══════════════════════════════════════════════════════════════════════╝

*  .text and .ro segments wont be swapped out to non volatile memory, sicne their content is static, they will be taken from the .elf file when they are needed.

Some CPU architectures very briefly “store” the machine language instruction(s) currently in the process being executed. Modern CPUs may be executing various stages of multiple machine language instructions in parallel.
While the CPU fetches instructions from RAM, those fetches might not have to go all the way out to RAM. The next instruction to be executed might already be sitting in a cache, which is high-speed storage that sits between the CPU and RAM. Some systems have cache that is dedicated to program instructions, while others have caches that contain a mix of instructions and data.
In a virtual memory environment, the entire program might not be in RAM all the time. Parts of it may get swapped out to secondary storage (e.g., hard disk, SSD, etc.) to make room for other programs or other parts of the same program. When the swapped out parts are needed again, they are pulled back into RAM.
In an embedded system, the program instructions might reside in ROM or flash memory, and only data resides in RAM. In this situation, the CPU (or microcontroller) fetches instructions from wherever the program instructions are stored.
In some systems, parts of the program reside in RAM and parts of the program reside in ROM (or some other non-volatile memory). The CPU fetches instructions from wherever the next program instruction is stored 