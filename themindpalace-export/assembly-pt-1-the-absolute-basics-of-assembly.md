---
title: Assembly pt. 1 - The Absolute Basics of Assembly
slug: assembly-pt-1-the-absolute-basics-of-assembly
published_date: 2024-07-13T11:31:00+00:00
tags: computer-science, computer-science/assembly
publish: true
make_discoverable: true
is_page: false
---

- WARNING: This post has been migrated to [my portfolio](https://kostek.uk/computer-whizz) Where this series is continued.
## Introduction
When I was in my teenage years, I enjoyed video games. I still do sometimes, but in the past, it reached obsession levels. More than playing games, I enjoyed breaking them. Not in the way that other people usually break games; I liked to break them with cheats and debuggers. I would create trainers in [Cheat Engine](https://www.cheatengine.org/) and make simple scripts that let me do what I want in those games. One of the games where I have done this extensively is the game Dishonored. I still have a GitHub repo of that cheat.

Once I started to get deeper into how those games work, it's imminent that you will encounter assembly, whether it's in Cheat Engine or other debuggers/disassemblers like OllyDBG or Ghidra. At that time, I only had a vague idea of how to do different things; it was usually changing a register; the most complex action would be a trampoline jump. The knowledge of assembly you need for this is quite low. But I wanted to be a master of assembly. On popular forums like [GuidedHacking](https://guidedhacking.com/), I saw what incredible cheats and programs people could muster to bend those games to their will, and I wanted to know how to do that as well. But at the time, I was too ignorant to realize how much work it actually took to do and learn something like that. Ultimately, I failed at the more complex topics, still learning tons, but not enough to become a master.

Now, however, four years later, I am more open-minded, less ignorant, and more patient. I'd like to tackle assembly again, on its own, without the game hacking part. I'd like to realize that dream that I had.

*You will need to preferably know C++, or at least Python, in order to follow this and understand what is happening.*

## The Textbook
I will be following the textbook [Modern X86 Assembly](https://www.amazon.co.uk/Modern-X86-Assembly-Language-Programming/dp/1484200659). If you want to go more in-depth, or just follow along with me, then I'd strongly recommend this book.

## Basic Knowledge
Because of my prior experience with assembly, it would be unfair to throw you straight into programming, even though that is the fun part. We will start with the complete basics.

## Data Types
Assembly has three data types:
- **Fundamental**
  - Byte (1 byte)
  - Word (16 bytes)
  - Dword (32 bytes)
  - Qword (64 bytes)
  - Double Qword (128 bytes)
- **Numerical**
  - Signed int (positive and negative values)
  - Unsigned int (only positive values)
  - Floating point (decimal points)
- **SIMD**, this is a type that I'd like you to keep in the back of your mind, but we will skip this for now.

Those data types are stored in registers. Think of registers as predefined variables. They store different values, just like variables. However, they work a bit differently. They are not initialized like in C++, but *pushed onto the stack*.

## The Stack
This is how the CPU stores all the data in memory. A stack is a virtual tower that data is pushed onto. Think of the stack like a pile of numbered paper plates. You *push* new plates onto the stack, and each plate has a certain number.

Here is the thing with stacks. We cannot remove a plate from the bottom of the stack if there is another plate on top of it. You first have to remove the plates that are on top of it. This is called a stack *pop*. You pop the plate from the top of the stack.

```plaintext
               Only this can be popped.
┌─────────────────┐   ▲                
│RAX              ├───┘                
├─────────────────┤                    
│RDI              │                    
├─────────────────┤                
│RSI              │                    
├─────────────────┤                    
│RIP              │                    
├─────────────────┤                    
│...              │                    
└─────────────────┘                                     
```

## NASM, MASM & AT&T
Just before we get into the registers, I'd like to tell you that there are three types of assembly syntax today. Other than style of coding and a few syntax differences, there is completely no difference between any of them. We will be doing everything in NASM assembly.

## The Registers
This is the core of every CPU in the world. Like I outlined before, registers are just like variables, just with some caveats:
- Fundamentally, all registers will only store numbers. There are no strings, booleans, or even floats. It's always a pure integer. If it is something else, then it is not abstracted.
- There is a reachable limit of registers on the CPU. Depending on the architecture, it is usually about 30 to 40.
The CPU works its best to repurpose registers as soon as they become redundant. Let's get through those registers.

### Registers - 16-bit
We will start with the smallest registers. They are not used by themselves anymore, but it's a good starting point.

First, we have the **general-purpose registers**. These are your usual everyday use registers. They are called **AX, BX, CX, and DX.**

Then, we have **semi-general-purpose registers**. I'm saying semi; that is because nowadays they are general-purpose. In the past, they were mostly specialized.
- **SI**, Source Index. Mostly used when working with strings. Doesn't have to work with strings, since assembly doesn't explicitly see if something is a string.
- **DI**, Destination Index. Similarly, also works with strings.
- **SP**, Stack Pointer. This is used to help the CPU keep track of the stack's top position, i.e., knows where the program starts.
- **BP**, Base Pointer. This is used by the CPU to keep track of the base of the current stack frame, which basically means it tracks where local variables are.

There is only one purely specialized register:
- **IP**, Instruction Pointer. This tracks where the next instruction to be executed by the CPU is.

### Registers - 32-bit and 64-bit
In 32-bit, we have the exact same registers as in 16-bit, except that all of them are extended to 32-bit. To denote this, an "E" is added to the beginning of each of the registers: **ESI, EDI, ESP, EBP, EAX, etc.**

On top of that, we get an additional specialized register: **FLAGS**. This register tracks what operation is currently performed on the registers. As computers got more complex, the processor would more often lose track and mix up what it was doing between the different concurrent processes. The FLAGS register is there to remind the CPU of what calculation it was performing, whether it was an addition, bitwise shift, etc.

In 64-bit, things have grown quite a bit; now instead of an E, R is used. **RAX, RBX, RSI, RIP**...

And on top of that, we get 39 more registers. These are called **ZMM0 / XMM0 / YMM0** through **ZMM31 / XMM31 / YMM31**. These registers are used for floating point and the before-mentioned SIMD.

We also get the **MXCR** register, which keeps track of what is done with floating point numbers, and the K0 to K7 OPMASK Registers, which are good to mention but are not that important.

## What Do I Actually Need to Know?
While it's good to know all of this, the most vital things to remember are the general-purpose and semi-general-purpose registers, and the stack. The rest of this will fall into place once you actually code something in assembly.

Armed with all this knowledge, we can go ahead and piece something together. Nothing too complex, and it will be still some time before we can write in assembly alone. Don't worry; at the time of starting this, I am learning alongside you and cannot do that much as well.  
As a crutch, I will be using C++ to help us fill in the bits that we cannot code ourselves.

## The Setup
I will be using VSCode along with C++ Code Pack and NASM code support. The compiler used will be g++ and the NASM assembler. Later on, we will start using makefiles.

## Let's Do This
We will start off with C++, and I'll skim over most of it since I am assuming you already know how to code. We will make an app to calculate the arithmetic: $(a + b) - (c + d) + 7$. The C++ code will look like so:

```c++
// int_arithmetic.cpp
#include <iostream>

extern "C" int AddSubI32_a(int a, int b, int c, int d);

void DisplayResults(int a, int b, int c, int d, int r1, int r2) {
    std::cout << "Results: " << std::endl;
    std::cout << "a =  " << a << std::endl;
    std::cout << "b =  " << b << std::endl;
    std::cout << "c =  " << c << std::endl;
    std::cout << "d = " << d << std::endl;
    std::cout << "r1 =  " << r1 << std::endl;
    std::cout << "r2 =  " << r2 << std::endl;
}

int main() {
    int a = 15;
    int b = 16;
    int c = 9

;
    int d = 6;
    int r1 = (a + b) - (c + d) + 7;
    int r2 = AddSubI32_a(a, b, c, d);

    DisplayResults(a, b, c, d, r1, r2);
    return 0;
}
```

The only thing that needs explaining is `extern 'C'`. This is used before the function to tell g++ that this is a pure assembly function, which means that the compiler will leave the function alone and will not try to optimize it and do any of its funny changes. This gives *us* the reins in how we will handle this function. By using C++ as a crutch, we don't have to deal with the stack; we can just deal with the logic of the code.

Assembly files are written in .asm, and they are usually composed of segments called **directives**; when I originally started with assembly, I called them sections. Because that is what they are called in the code. Directives are used to divide an executable into sections (shocking!). There's a lot of different directives, but you just need to know two of them: `section .data`, which is a section dedicated to storing user-defined variables, and `section .text`, which is used for the actual code. That is usually how every assembly file starts out, `section .data` followed by `section .text`. In this case, however, we don't deal with any specific variables, so we are jumping straight to .text. We create a new file called "int_arithmetic.asm".

```assembly
section .text
    global AddSubI32_a
AddSubI32_a:
    ret
```

After the section, we define the function that we declared in C++. The beauty of doing it this way is that the C++ Linker will do all the work for us. It will figure out where the AddSubI32_a function is. This is where the function in C++ will be executed. All functions in Assembly start with the function name and then a comma.
This is what we will write here:

```assembly
; int_arithmetic.asm
section .text
    global AddSubI32_a
AddSubI32_a:
    add eax, ecx ; eax = a + b
    add edx, DWORD [esp+0x34] ; edx = c + d
    sub eax, edx ; (a + b) - (c + d)
    add eax, 7   ; + 7
    ret
```

The registers themselves might differ depending on your specific Windows system. This was a problem when I was trying this straight from the textbook. If you look at this code in the textbook, the registers were completely different, and I had to use [gdb](https://sourceware.org/gdb/) to figure out the right registers. Unfortunately, this post is long enough as is, so consider this a DIY challenge. Expect a tutorial for this in the future. For now, we are going to assume that the required data is put into the specified registers.

So what does this code actually do? First, we add the ecx register to eax. In assembly, any arithmetic is the other way around, instead of `add src,dst`, it's `add dst, src`. So keep this in mind. We assume that the `ecx` register is $b$ and `eax` is $a$, and we add b onto a using the predefined function `add`. Pretty self-explanatory.

The second line is where you might be a bit confused: `add edx, DWORD [esp+0x34] ; edx = c + d`.  
As discussed earlier, DWORD is a 32-bit number; the square brackets mean [dereferencing the pointer](https://www.w3schools.com/cpp/cpp_pointers.asp). Just like in C++. This dereferences the pointer that points to `[esp+0x34]`. When debugging this application with gdb, I have noticed that $d$ is stored at this position. Note that ESP is the 32-bit Stack Pointer Register, meaning the starting position of the stack of this application, so it is at address 0x34 in the application. And we add that onto `edx`, which I figured out is the position of the $c$ register. Now we know that `edx` contains c + d, and `eax` contains a + b. So we use the `sub` function to take away `edx` *from* `eax`. And then, we use the function `ret` to return the function. Pretty simple, right? Other than figuring out the actual registers, it's a piece of cake!

## Compiling

Now that we have the code, how do we actually compile it? Unfortunately, it's different from just running the app in VSCode or running a g++ command. First, we need to build an object file from the .asm file for nasm. We use the NASM compiler for that with this command: `nasm -f win32 --prefix _ -o int_arithmetic.o`, in our scenario we compile for Windows 32, if we want to compile for Linux 64-bit, we use `nasm -f elf64`. The `--prefix` tells the compiler to prefix each function with an underscore, due to some of g++'s semantics that I cannot really understand. You should get a file called int_arithmetic.o; then, we run g++: `g++ int_arithmetic.cpp int_arithmetic.o -m32 -o int_arithmetic.cpp`.

And now, when you run the program:

```plaintext
> .\program.exe
Results: 
a =  15
b =  16
c =  9
d = 6
r1 =  23
r2 =  23
```

Congratulations! You just compiled your first assembly program!

## Conclusion
In the next part, we will whizz through a few more basic arithmetic questions, in order to gain some basic understanding of how arithmetics work in assembly.

Please remember that I am also studying this alongside you. If you find any problems or inconsistencies in my blogs, please contact me! I want to learn just as much as you.

You can find the on-going Git Repository on [https://github.com/K0Stek122/Assembly-Tutorial](https://github.com/K0Stek122/Assembly-Tutorial)

Thank you. And good luck with your assembly journey!