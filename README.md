# valgrind-memory-debugging-tool-linux
Using valgrind to find memory leaks and invalid memory use.

## Valgrind installation

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

## Example C Program with valgrind

For demonstration, consider following C code.

```
#include <stdlib.h>
int main()
{
    char *x = malloc(100); /* or, in C++, "char *x = new char[100] */
    return 0;
}
```

Compile your C program with -g option. This allows the compiler to collect the debugging information.

```
cd valgrind-memory-debugging-tool-linux
gcc -g -o example1 example1.c
./example1
```

Finding memory leaks with valgrind.

```
valgrind --tool=memcheck --leak-check=yes ./example1
```

This will result in following information about the program.

```
==11587== Memcheck, a memory error detector
==11587== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==11587== Using Valgrind-3.16.0 and LibVEX; rerun with -h for copyright info
==11587== Command: ./example1
==11587== 
==11587== 
==11587== HEAP SUMMARY:
==11587==     in use at exit: 100 bytes in 1 blocks
==11587==   total heap usage: 1 allocs, 0 frees, 100 bytes allocated
==11587== 
==11587== 100 bytes in 1 blocks are definitely lost in loss record 1 of 1
==11587==    at 0x4C2DF66: malloc (vg_replace_malloc.c:307)
==11587==    by 0x400537: main (example1.c:4)
==11587== 
==11587== LEAK SUMMARY:
==11587==    definitely lost: 100 bytes in 1 blocks
==11587==    indirectly lost: 0 bytes in 0 blocks
==11587==      possibly lost: 0 bytes in 0 blocks
==11587==    still reachable: 0 bytes in 0 blocks
==11587==         suppressed: 0 bytes in 0 blocks
==11587== 
==11587== For lists of detected and suppressed errors, rerun with: -s
==11587== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

## Finding invalid pointer use with valgrind

Valgrind can also find the use of invalid heap memory using the memcheck tool. For instance, if you allocate an array with malloc or new and then try to access a location past the end of the array.

```
char *x = malloc(10);
x[10] = 'a';
```

Valgrind will detect it. For instance, running the following program, example2, through valgrind

```
#include <stdlib.h>

int main()
{
    char *x = malloc(10);
    x[10] = 'a';
    return 0;
}

```

Compile your C program with -g option. This allows the compiler to collect the debugging information.

```
cd valgrind-memory-debugging-tool-linux
gcc -g -o example2 example2.c
./example2
```

Finding memory leaks with valgrind.

```
valgrind --tool=memcheck --leak-check=yes ./example2
```

This will result in following information about the program.

```
==11673== Memcheck, a memory error detector
==11673== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==11673== Using Valgrind-3.16.0 and LibVEX; rerun with -h for copyright info
==11673== Command: ./example2
==11673== 
==11673== Invalid write of size 1
==11673==    at 0x400544: main (example2.c:6)
==11673==  Address 0x520504a is 0 bytes after a block of size 10 alloc'd
==11673==    at 0x4C2DF66: malloc (vg_replace_malloc.c:307)
==11673==    by 0x400537: main (example2.c:5)
==11673== 
==11673== 
==11673== HEAP SUMMARY:
==11673==     in use at exit: 10 bytes in 1 blocks
==11673==   total heap usage: 1 allocs, 0 frees, 10 bytes allocated
==11673== 
==11673== 10 bytes in 1 blocks are definitely lost in loss record 1 of 1
==11673==    at 0x4C2DF66: malloc (vg_replace_malloc.c:307)
==11673==    by 0x400537: main (example2.c:5)
==11673== 
==11673== LEAK SUMMARY:
==11673==    definitely lost: 10 bytes in 1 blocks
==11673==    indirectly lost: 0 bytes in 0 blocks
==11673==      possibly lost: 0 bytes in 0 blocks
==11673==    still reachable: 0 bytes in 0 blocks
==11673==         suppressed: 0 bytes in 0 blocks
==11673== 
==11673== For lists of detected and suppressed errors, rerun with: -s
==11673== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
```

