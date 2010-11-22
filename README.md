# Prerequisites #

0. Choose an install location like `/opt/mementos` and make a `build` directory.
 <pre>
$ mkdir -p /opt/mementos
$ cd /opt/mementos
$ mkdir src build
</pre>

1. Add `/opt/mementos/bin` to your path.  (You don't technically have to do this--the supplied `configure` scripts will find the necessary LLVM and mspgcc tools in your path--but if you're installing the prerequisites for the sole purpose of experimenting with Mementos, you might as well concentrate everything in one directory.)

2. Build and install [LLVM](http://llvm.org/releases/) and [clang](http://clang.llvm.org/).  Configure LLVM with `--disable-optimized --prefix=/opt/mementos`.  Don't discard your source tree or build tree.  Note that you can pass `-jN` to the final `make` to spawn an N-way parallel make.
 <pre>
$ cd /opt/mementos/src
$ svn co http://llvm.org/svn/llvm-project/llvm/trunk llvm
$ cd llvm/tools
$ svn co http://llvm.org/svn/llvm-project/cfe/trunk clang
$ cd /opt/mementos/build
$ mkdir llvm
$ cd llvm
$ ../../src/llvm/configure --disable-optimized --prefix=/opt/mementos
$ make -j2
$ make install
</pre>

3. Build and install [mspgcc4](http://mspgcc4.sourceforge.net/).  If you're on OS X, don't build Insight.  Specify `/opt/mementos` when asked for an installation path.
 <pre>
$ cd /opt/mementos/src
$ git clone git://mspgcc4.git.sourceforge.net/gitroot/mspgcc4/mspgcc4 mspgcc4
$ cd mspgcc4
$ perl buildgcc.pl
[...]
[make sure not to build Insight if you're on OS X]
[enter /opt/mementos for target toolchain path]
[...]
</pre>

4. Clone the _mementos_ repository and build and install its LLVM component:
 <pre>
$ cd /opt/mementos/src
$ git clone git://github.com/ransford/mementos.git mementos
$ cd mementos/llvm
$ ./configure --prefix=/opt/mementos
$ make
$ make install
</pre>

5. Configure Mementos's non-LLVM component:
 <pre>
$ cd /opt/mementos/src/mementos
$ ./configure
</pre>

# Mementosizing a Program #

By _Mementosize_ a program, we mean instrument the program with energy checks called _trigger points_.  By default, Mementos will build several variants of your program around several different instrumentation strategies: loop-latch instrumentation, function-return instrumentation, and timer-aided loop-latch instrumentation.  We'll call these variants `myfoo+latch`, `myfoo+return`, and `myfoo+timer`.  It additionally tries to build separate "plain" (uninstrumented) versions with clang and mspgcc.

XXX We'll write more here soon.

1. Make sure the program is in a single `.c` file; let's call it e.g. `myfoo.c`.
2. Put the program in `src/mementos` (see below).
2. Build it against Mementos:
 <pre>
$ cd /opt/mementos/src/mementos
$ make TARGET=myfoo
</pre>
 If the build succeeds, you now have several executables representing different versions of your program.  These are ready to run against mspsim.

XXX We'll write more here soon.

# Caveats #

Things known not to work:

* If you have your own TimerA interrupt defined, it will clash with the one Mementos tries to introduce in the `myfoo+timer` variant.