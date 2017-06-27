---
layout: post
title: "How to reproduce the segmentation faults of Ryzen bug"
description: ""
category: 
tags: [linux]
---
{% include JB/setup %}

It's been reported that sporadic segmentation faults were occurred on Linux/Ryzen
under heavy CPU loads especially during parallel-compiling large
OpenSource code such as Linux kernel, Mesa, LLVM, Chromium and WebKit.
But, it's difficult to reproduce the segmentation faults due to the
low probability and the uncleared reproducibility conditions.
I'd like to summarize the best practices in this post.


## Preparations

It's been reported that some conditions significantly reduce the probability.
On the other hand, others have reported such conditions change nothing.
There is nothing obvious at the moment.

Check your following UEFI BIOS settings.

* Enable uOP cache
* Enable SMT (Simultaneous Multithreading)

uOP cache setting can be found in `Advanced > AMD CBS > Zen Common Options > Opcache Control` in my UEFI.

This crashes happen not only on Linux,
but also on [Windows Subsystems for Linux](https://community.amd.com/thread/215773?start=75&tstart=0)
and on [NetBSD](https://twitter.com/oshimyja/status/872099591759507457).
Actually Linux is the most reported. This article uses it.

Check [ASLR (Address space layout randomization)](https://en.wikipedia.org/wiki/Address_space_layout_randomization) is enabled.
It's been reported that disabling ASLR significantly reduces the probability.

~~~
 $ sysctl kernel.randomize_va_space
kernel.randomize_va_space = 2
~~~
It's already enabled if the value is 2. Enable it otherwise.

~~~
$ sudo sysctl kernel.randomize_va_space=2
kernel.randomize_va_space = 2
~~~

Check your `gcc` and `bash` are not
[PIE (Position-independent executables)](https://en.wikipedia.org/wiki/Position-independent_code#Position-independent_executables).
In Ubunt 17.04, `gcc` and `bash` aren't PIE, but `clang` and `dash` are PIE.

You can check PIE of executables by checking `Type` field of the result of `readelf -h`: 

~~~
$ readelf -h $(which bash) 
~~~

It's PIE if the type is DYN, non-PIE if EXEC.

In Ubunt 17.04, `gcc` generates PIE as default. 
It generates non PIE by specifying `-no-pie` and `-fno-pie` switches.

~~~
$ echo 'int main() { return 0; }' > a.c
$ gcc a.c
$ readelf -h a.out | grep Type
  Type:                              DYN (Shared object file)
$ gcc -no-pie -fno-pie a.c
$ readelf -h a.out | grep Type
  Type:                              EXEC (Executable file)
~~~

Make the coredump size unlimited to get full coredumps.

~~~
$ ulimit -c unlimited
~~~

And, install debug info packages.


## Compile repeatedly

Let's compile repeatedly until it will fail.

~~~
$ while make -j$(nproc); do make clean; done
~~~

It takes about ten minutes if you are lucky, about two hours if ordinary, never happens if unlucky.

If it'd happen, collect a coredump file `core` if it'd be generated.
`gcc` usually doesn't generate coredump because it catches the segmentation faults by itself and output an own error report.

`libtool` is implemented as a shell script.
I'd like to recommend to compile code using `libtool` because it's easy to get coredump of `bash`.

Linux kernel may output some messages. Take the result of `dmesg` command.

Satoru Takeuchi created [a useful script](https://gist.github.com/satoru-takeuchi/23afbf565c2d97c3ef16e5d46d11f5bf) to compile repeatedly.


## Postmortem Examination

There is a particular pattern in some Ryzen's crashes.
According to [Hideki EIRAKU's investigation](http://www.e-hdk.com/diary/d201706c.html#20-2),
Ryzen seems to execute 64 bytes ahead instructions than where RIP register is pointing immediately after jump.

[Here](https://gist.github.com/fujii/a5411f523b0072beae22cda0f3858e58) is my coredump of bash.
Let's check the coredump.

Current `rip` was `0x4370d0`.

~~~
rip            0x4370d0	0x4370d0 <execute_builtin+720>
~~~
SIGSEGV was raised while copying `[rsp+0x8]` to `eax` immediately after returning from `run_unwind_frame`.

~~~
   0x00000000004370cb <+715>:	call   0x465ef0 <run_unwind_frame>
=> 0x00000000004370d0 <+720>:	mov    eax,DWORD PTR [rsp+0x8]
~~~

`rsp` was `0x7ffe79c8f4d0`.

~~~
rsp            0x7ffe79c8f4d0	0x7ffe79c8f4d0
~~~
Here is the stack dump:

~~~
(gdb) x/32g 0x7ffe79c8f450
0x7ffe79c8f450:	0x0000000000000001	0xe375df75c9fcd400
0x7ffe79c8f460:	0x0000000000000000	0x00000000018da5c8
0x7ffe79c8f470:	0x00000000004659c0	0xe375df75c9fcd400
0x7ffe79c8f480:	0x0000000000000000	0x0000000000000000
0x7ffe79c8f490:	0x0000000000000001	0x0000000000000000
0x7ffe79c8f4a0:	0x0000000000000000	0x0000000000000000
0x7ffe79c8f4b0:	0x0000000000484d10	0x0000000000465f10
0x7ffe79c8f4c0:	0x0000000000000001	0x00000000004370d0
0x7ffe79c8f4d0:	0x00000000018dd548	0x0000000000000000
0x7ffe79c8f4e0:	0x0000000000000001	0x000000000180e5c8
0x7ffe79c8f4f0:	0x0000000000000000	0x000000000180e5c8
0x7ffe79c8f500:	0x0000000000484d10	0x0000000000000000
0x7ffe79c8f510:	0x0000000000000000	0x0000000000000000
0x7ffe79c8f520:	0x0000000000000000	0x0000000000439426
0x7ffe79c8f530:	0x0000000000000001	0x00000000ffffffff
0x7ffe79c8f540:	0x00000000018ddea8	0x00000001008dc8c8
~~~
`[rsp-8]` was `0x00000000004370d0`.
This was the return address which the preceding `call` pushed.

Here is the code of `run_unwind_frame`:

~~~
(gdb) disassemble run_unwind_frame
Dump of assembler code for function run_unwind_frame:
   0x0000000000465ef0 <+0>:	cmp    QWORD PTR [rip+0x2a8a78],0x0        # 0x70e970 <unwind_protect_list>
   0x0000000000465ef8 <+8>:	je     0x465f17 <run_unwind_frame+39>
   0x0000000000465efa <+10>:	push   rbx
   0x0000000000465efb <+11>:	mov    ebx,DWORD PTR [rip+0x2a8a83]        # 0x70e984 <interrupt_immediately>
   0x0000000000465f01 <+17>:	mov    DWORD PTR [rip+0x2a8a79],0x0        # 0x70e984 <interrupt_immediately>
   0x0000000000465f0b <+27>:	call   0x465aa0 <unwind_frame_run_internal>
   0x0000000000465f10 <+32>:	mov    DWORD PTR [rip+0x2a8a6e],ebx        # 0x70e984 <interrupt_immediately>
   0x0000000000465f16 <+38>:	pop    rbx
   0x0000000000465f17 <+39>:	repz ret 
~~~
`pop rbx` was executed just before returning this function.
This value has been recoded in the stack.
`[rsp-16]` was 1.
And `rbx` was `1`.

~~~
rbx            0x1	0x1
~~~

The stack and th registers look consistent.
But, SIGSEGV was raised. It's a Mystery!

Let's use the hypothesis here.
The return address was `0x4370d0` and current `rip` was `0x4370d0`.
But, if Ryzen would execute 64byte ahead instructions, what would happen?
The address was `0x437090`.
Here is the disassembled instruction.

~~~
(gdb) x/i 0x437090
   0x437090 <execute_builtin+656>:	add    BYTE PTR [rax],al
~~~

`rax` was `0`.
If this instruction was really executed, `0` was the address where this SIGSEGV was occurred.

~~~
(gdb) p $_siginfo._sifields._sigfault.si_addr
$3 = (void *) 0x6dfb44
~~~
Unfortunately, It was `0x6dfb44`.
Surprisingly, no registers stores this value.
I don't know where this value came from.

Anyway, the hypothesis doesn't seem to match in this case.


## Links

* [AMD community forums](https://community.amd.com/thread/215773)
* [Gentoo discussion forums](https://forums.gentoo.org/viewtopic-t-1061546-start-0-postdays-0-postorder-asc-highlight-.html)
* [phoronix](https://www.phoronix.com/scan.php?page=news_item&px=Ryzen-Compiler-Issues)
* [phoronix forum](https://www.phoronix.com/forums/forum/hardware/processors-memory/955368-some-ryzen-linux-users-are-facing-issues-with-heavy-compilation-loads)
* [reddit](https://redd.it/6f08mb)
