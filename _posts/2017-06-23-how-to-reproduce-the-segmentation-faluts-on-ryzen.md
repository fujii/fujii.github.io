---
layout: post
title: "How to reproduce the segmentation faults of Ryzen bug"
description: ""
category: 
tags: []
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

This crashes happen not only on Linux. 
Sat reported he could reproduce this [on Windows Subsystems for Linux](https://community.amd.com/thread/215773?start=75&tstart=0).
Oshimaya did [on NetBSD](https://twitter.com/oshimyja/status/872099591759507457).
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

There is a particular pattern in Ryzen's crashes.
Let's check the generated coredumps.
According to [Hideki EIRAKU's investigation](http://www.e-hdk.com/diary/d201706c.html#20-2),
Ryzen seems to execute 64 bytes ahead instructions than where RIP register is pointing.

* ToDo: How to install dbg symbol pkg
* ToDo: How to use gdb


## Links

* [AMD community forums](https://community.amd.com/thread/215773)
* [Gentoo discussion forums](https://forums.gentoo.org/viewtopic-t-1061546-start-0-postdays-0-postorder-asc-highlight-.html)
* [phoronix](https://www.phoronix.com/scan.php?page=news_item&px=Ryzen-Compiler-Issues)
* [phoronix forum](https://www.phoronix.com/forums/forum/hardware/processors-memory/955368-some-ryzen-linux-users-are-facing-issues-with-heavy-compilation-loads)
* [reddit](https://redd.it/6f08mb)
