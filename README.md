# L4Re native toolchain POC

These recipes build native L4Re cross compiling toolchains for amd64 and arm64.
As examples, a simple game written in C++ (2048) and Python 3.8 is built with
this cross toolchain. The example game uses CMake and has not been modified
while some patches were necessary to get Python built.

# How to build

First you have to clone the submodules:

    $ git submodule update --init

You need the [Bob Build Tool](https://bob-build-tool.readthedocs.io/en/latest/)
to build the project. The examples are built by

    $ bob dev l4re/toolchain-amd64 -j
    $ bob dev l4re/toolchain-arm64 -j

for the respective platforms. The `-j` option does a parallel build. You may
want to add the `--sandbox` switch if the build fails. It does the build in an
isolated sandbox environment to maximize reproducibility. Both options are
optinal.

The results will contain the game executable (`usr/bin/2048`), Python
(`usr/bin/python3.8` and standard library) and the L4Re shared libraries
(`usr/lib/*.so`). Include both `usr/lib` and `usr/bin` in the `Makeconf.boot`
`MODULE_SEARCH_PATH` of an existing L4Re build tree, add an entry in your
modules.list and start as usual:

    entry 2048
    roottask moe --init=rom/2048
    module l4re
    module 2048

# Using the toolchain directly

To build just the toolchains do a

    $ bob dev l4re/toolchain-amd64/devel::l4re::cross-toolchain/ -j
    $ bob dev l4re/toolchain-arm64/devel::l4re::cross-toolchain/ -j

The results will contain the cross-compiler (`usr/bin/aarch64-l4re-gcc` resp.
`usr/bin/x86_64-l4re-gcc`) together with their sysroot
(`usr/sysroots/aarch64-l4re` resp. `usr/sysroots/x86_64-l4re`). The sysroot has
the headers, static and shared libraries to compile and link with. The cross
compiler will find the sysroot automatically (i.e. no need to specify
`--sysroot`).

The toolchain can be copied to other hosts. It's file system position
independent and should run on any contemporary x86\_64 Linux distribution.
