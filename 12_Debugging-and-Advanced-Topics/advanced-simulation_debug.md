# 仿真调试

官网英文原文地址：http://dev.px4.io/simulation-debugging.html

As the simulation is running on the host machine, all the desktop development tools are available.

## CLANG Address Sanitizer (Mac OS, Linux)

The Clang address sanitizer can help to find alignment (bus) errors and other memory faults like segmentation fauls. The command below sets the right compile options.

```shmake clean # only required on first address sanitizer run after a normal buildMEMORY_DEBUG=1 make posix jmavsim```

## Valgrind

```shbrew install valgrind```

or

```shsudo apt-get install valgrind```

<aside class="todo">Add instructions how to run Valgrind</aside>

## Start combinations

SITL can be launched with and without debugger attached and with either jMAVSim or Gazebo as simulation backend. This results in the start options below:

```sh
make posix_sitl_default jmavsimmake posix_sitl_default jmavsim___gdbmake posix_sitl_default jmavsim___lldb

make posix_sitl_default gazebomake posix_sitl_default gazebo___gdbmake posix_sitl_default gazebo___lldb

make posix_sitl_lpe jmavsimmake posix_sitl_lpe jmavsim___gdbmake posix_sitl_lpe jmavsim___lldb

make posix_sitl_lpe gazebomake posix_sitl_lpe gazebo___gdbmake posix_sitl_lpe gazebo___lldb
```

where the last parameter is the &lt;viewer\_model\_debugger&gt; triplet (using three underscores implies the default &#39;iris&#39; model).This will start the debugger and launch the SITL application. In order to break into the debugger shell and halt the execution, hit ```CTRL-C```:

```gdb
Process 16529 stopped* thread #1: tid = 0x114e6d, 0x00007fff90f4430a libsystem_kernel.dylib`__read_nocancel + 10, name = 'px4', queue = 'com.apple.main-thread', stop reason = signal SIGSTOP frame #0: 0x00007fff90f4430a libsystem_kernel.dylib`__read_nocancel + 10libsystem_kernel.dylib`__read_nocancel:-> 0x7fff90f4430a <+10>: jae 0x7fff90f44314 ; <+20> 0x7fff90f4430c <+12>: movq %rax, %rdi 0x7fff90f4430f <+15>: jmp 0x7fff90f3fc53 ; cerror_nocancel 0x7fff90f44314 <+20>: retq(lldb) 
```

In order to not have the DriverFrameworks scheduling interfere with the debugging session ```SIGCONT``` should be masked in LLDB and GDB:

```bash
(lldb) process handle SIGCONT -n false -p false -s false
```

Or in the case of GDB:

```
(gdb) handle SIGCONT noprint nostop
```

After that the The lldb or gdb shells behave like normal sessions, please refer to the LLDB / GDB documentation.

The last parameter, the &lt;viewer\_model\_debugger&gt; triplet, is actually passed to make in the build directory, so

```sh
make posix_sitl_lpe jmavsim___gdb
```

is equivalent with

```sh
make posix_sitl_lpe # Configure with cmakemake -C build_posix_sitl_lpe jmavsim___gdb
```

A full list of the available make targets in the build directory canbe obtained with:

```sh
make help
```

but for your convenience, a list with just the &lt;viewer\_model\_debugger&gt; tripletsis printed with the command

```sh
make list_vmd_make_targets
```

## Compiler optimization

It is possible to suppress compiler optimization for given executables and/ormodules (as added by cmake with `add_executable` or `add_library`) when configuringfor `posix_sitl_*`. This can be handy when it is necessary to step through codewith a debugger or print variables that would otherwise be optimized out.

To do so, set the environment variable `PX4_NO_OPTIMIZATION` to be a semi-colonseparated list of regular expressions that match the targets that needto be compiled without optimization. This environment variable is ignoredwhen the configuration isn&#39;t `posix_sitl_*`.

For example,

```sh
export PX4_NO_OPTIMIZATION='px4;^modules__uORB;^modules__systemlib$'
```

would suppress optimization of the targets: platforms\_\_posix\_\_px4\_layer, modules\_\_systemlib, modules\_\_uORB, examples\_\_px4\_simple\_app, modules\_\_uORB\_\_uORB\_tests and px4.

The targets that can be matched with these regular expressions can beprinted with the command:

```sh

make -C build_posix_sitl_* list_cmake_targets

```
