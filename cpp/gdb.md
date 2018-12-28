[HOME](README.md)

# Table of contents
* [Signals](#Signals)
* [Invoke gdb](#Invoke-gdb)

# Signals 
Using the follwoing statements to get the signals information in gdb
* info sigals
* info handle  
Using the follwoing statement to told GDB to handle it.
* handle *signal* keywords..  
The keywords are:
* nostop : not stop
* stop : stop 
* print : print signal
* noprint : imply nostop
* pass : allow your program handle the signal
* nopass : not allow your program handle the signal

# Invoke gdb
When invoke gdb, it will run with a default shell for you, so if you
have addtional environment variables need to use in debug, make sure
you have the correct way to use gdb.

