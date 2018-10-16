[HOME](../README.md)

# Table of Contents
* [Build system](#Build-system)

# Build System
A simple case
```
CC = gcc
AR = ar
LDFLAGS = -L.
tests : prog.c libtests.a
	$(CC) $(LDFLAGS) -o prog prog.c -ltests

libtests.a : test1.o test2.o
	$(AR) -cvq libtests.a test1.o  test2.o

%.o : %.c
	$(CC) -c $(CFLAGS) $< -o $@

clean:
	rm *.o libtests.a prog.exe 
```
* Use Variable
  * set varible: **CC= gcc**
  *  
