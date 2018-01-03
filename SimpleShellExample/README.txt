# A simple disassembly example.
shell.c is the source code for the executable. 

I compiled it on 64-bit macOS Sierra and used it as practice/reference
for reverse engineering.

GDB is not installed on default installs of macOS. Instead, apple's debugger
of choice, LLDB (lldb) is used. This tool is equally as powerful and has 
many of the same commands. (See more at https://lldb.llvm.org/docs.html) 

I compiled shell.c with the following command
    $ gcc -o AnyName shell.c

not specifying the -o flag will result in the default executable name of 
a.out.

We can load the executable into lldb with the following
    $ lldb executableName

Once we're in lldb, we can set a breakpoint at our entry function with:
    $ b main

Then we can run the program with
    $ run

The executable should stop and we should see something along the lines of
    *   thread #1, queue = 'com.apple.main-thread'....[snip]

Which will show an arrow (->) pointing to the memory location of main, e.g.:
    ->  0x100000f60 <+0>:  pushq %rbp

followed by subsequent instructions. If we want a more detailed view of the 
function we can use:
    $ disas 

Which will show a complete disassembly of the function. If we want to inspect
a specific memory location listed here, for example the memory location of 
a call to a function, like callq we just type
    $ disas -s <memory address of instruction>

I used this to inspect a callq function, which gave us a further breakdown
of the system() function being used and returned the following

  shellExample`system:
    0x100000f88 <+0>:  jmpq   *0x82(%rip)       ; (void *)0x0000000100000fa0
    0x100000f8e:       addb   %al, (%rax)

If we want to see the content of all the general purpose registers we can type
    $ register read

Which will show us the content of all the general purpose registers. I suggest
reading up on on the LLDB documentation and apple's assembly language docs 
as necessary. There's also tons of great info if you dig around the web.
