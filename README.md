# valgrind-memory-debugging-tool-linux
Using valgrind to find memory leaks and invalid memory use.

### Valgrind installation

There are two ways you can install Valgrind on linux.

### Install pre-built valgrind stable release

**On Debian and Ubuntu:**

Download stable release valgrind 3.16.0.

```
https://www.valgrind.org/downloads/current.html
```

Decompress and untar valgrind 3.16.0 using bzip2.

```
bzip2 -d valgrind-3.16.0.tar.bz2 
tar -xf valgrind-3.16.0.tar 
```

Configure, compile, and install it.

```
cd valgrind-3.16.0
./configure
make
sudo make install
```

By default this will install valgrind binaries in /usr/local/bin and libs in /usr/local/lib.

To check version and test whether valgrind installed correctly.

```
valgrind --version
```


### compile valgrind from source, compile and install

**On Debian and Ubuntu:**

Download source code.

```
 git clone git://sourceware.org/git/valgrind.git
```

Extract the source code. To follow the instructions in the README file that the clone should give you. 

Configure, Compile, and install it.

```
cd valgrind
./autogen.sh
./configure --prefix=...
make
sudo make instal
```

### Example Debug C Program

Compile your C program with -g option. This allows the compiler to collect the debugging information.

```
cd valgrind
gcc -g -o example example.c
./example
```

Finding memory leaks with valgrind.


```
$ valgrind --tool=memcheck --leak-check=yes ./example

```

This will result in following information about the program.

```
==10745== Memcheck, a memory error detector
==10745== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==10745== Using Valgrind-3.16.0 and LibVEX; rerun with -h for copyright info
==10745== Command: ./example2
==10745== 
==10745== 
==10745== HEAP SUMMARY:
==10745==     in use at exit: 100 bytes in 1 blocks
==10745==   total heap usage: 1 allocs, 0 frees, 100 bytes allocated
==10745== 
==10745== 100 bytes in 1 blocks are definitely lost in loss record 1 of 1
==10745==    at 0x4C2DF66: malloc (vg_replace_malloc.c:307)
==10745==    by 0x400537: main (example.c:4)
==10745== 
==10745== LEAK SUMMARY:
==10745==    definitely lost: 100 bytes in 1 blocks
==10745==    indirectly lost: 0 bytes in 0 blocks
==10745==      possibly lost: 0 bytes in 0 blocks
==10745==    still reachable: 0 bytes in 0 blocks
==10745==         suppressed: 0 bytes in 0 blocks
==10745== 
==10745== For lists of detected and suppressed errors, rerun with: -s
==10745== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

### Finding invalid pointer use with valgrind
