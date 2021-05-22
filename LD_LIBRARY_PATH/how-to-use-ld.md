## **LD_LIBRARY_PATH – or: How to get yourself into trouble!**



This little note is about one of the most “misused” environment variables on Unix systems: LD_LIBRARY_PATH . If used right, it can be very useful, but very often – not to say, most of the time – people apply it in the wrong way, and that is were they are calling for trouble.

#### So, what does it do?

LD_LIBRARY_PATH tells the dynamic link loader (ld. so – this little program that starts all your applications) where to search for the dynamic shared libraries an application was linked against. Multiple directories can be listed, separated by a colon (:), and this list is then searched *before* the compiled-in search path(s), and the standard locations (typically /lib, /usr/lib, …).

This can be used for

- testing new versions of a shared library against an already compiled application
- re-locating shared libraries, e.g. to preserve old versions
- creating a self-contained, relocatable(!) environment for larger applications, such that they do not depend on (changing) system libraries – many software vendors use that approach.

#### Sounds very useful, where is the problem?

Yes, it is useful – if you apply it in the way it was invented for, like the three cases above. However, very often it is used as a crutch to fix a problem that could have been avoided by other means (see below). It is even getting worse, if this crutch is applied globally into an user’s (or the system’s!) environment: applications compiled with those settings get dependent on this crutch – and if it is eventually taken away, they start to stumble (i.e. fail to run).

There are other implications as well:

1. **Security:** Remember that the directories specified in LD_LIBRARY_PATH get searched before(!) the standard locations? In that way, a nasty person could get your application to load a version of a shared library that contains malicious code! That’s one reason why setuid/setgid executables do neglect that variable!
2. **Performance:** The link loader has to search all the directories specified, until it finds the directory where the shared library resides – for ALL shared libraries the application is linked against! This means a lot of system calls to open(), that will fail with “ENOENT (No such file or directory)”! If the path contains many directories, the number of failed calls will increase linearly, and you can tell that from the start-up time of the application. If some (or all) of the directories are in an NFS environment, the start-up time of your applications can really get long – and it can slow down the whole system!
3. **Inconsistency:** This is the most common problem. LD_LIBRARY_PATH forces an application to load a shared library it wasn’t linked against, and that is quite likely not compatible with the original version. This can either be very obvious, i.e. the application crashes, or it can lead to wrong results, if the picked up library not quite does what the original version would have done. Especially the latter is sometimes hard to debug.

#### How can I check which dynamic libraries are loaded?

There is the ldd command, that shows you which libraries are needed by a dynamically linked executable, e.g.

```
$ ldd /usr/bin/file
        linux-vdso.so.1 =>  (0x00007fff9646c000)
        libmagic.so.1 => /usr/lib64/libmagic.so.1 (0x00000030f9a00000)
        libz.so.1 => /lib64/libz.so.1 (0x00000030f8e00000)
        libc.so.6 => /lib64/libc.so.6 (0x00000030f8200000)
        /lib64/ld-linux-x86-64.so.2 (0x00000030f7a00000)
```

This is a ‘static’ view, since ldd doesn’t resolve dependencies and libraries that will get loaded at runtime, e.g. by a library that depends on others. To get an overview of libraries loaded at runtime, you can use the pldd command:

```
$ ldd /bin/bash
        linux-vdso.so.1 =>  (0x00007ffff63ff000)
        libtinfo.so.5 => /lib64/libtinfo.so.5 (0x0000003108a00000)
        libdl.so.2 => /lib64/libdl.so.2 (0x00000030f8600000)
        libc.so.6 => /lib64/libc.so.6 (0x00000030f8200000)
        /lib64/ld-linux-x86-64.so.2 (0x00000030f7a00000)
$ pldd 24362
24362:  -bash
/lib64/ld-2.12.so
/lib64/libc-2.12.so
/lib64/libdl-2.12.so
/lib64/libtinfo.so.5.7
/usr/lib64/gconv/ISO8859-1.so
/lib64/libnss_files-2.12.so
```

As you can see, there are two more .so-files loaded at runtime, that weren’t on the ‘static’ list.

Note: pldd is originally a Solaris command, that usually is not available on Linux. However, there is a Perl-script available (and installed on our machines) that extracts this information from the /proc/<PID>/maps file.

#### How to avoid those problems with LD_LIBRARY_PATH?

A very simplistic answer would be: “just don’t use LD_LIBRARY_PATH!” The more realistic answer is, “the less you use it, the better off you will be”.

Below comes a list of ways how to avoid LD_LIBRARY_PATH, inspired by reference [1] below. The best solution is on the top, going down to the last resort.

- If you compile your application(s) yourself, you can solve the problem by specifying the correct location of the shared libraries and tell the linker to add those to the runpath of your executable, specifying the path in the ‘-rpath’ linker option:

  ```
  cc -o myprog obj1.o ... objn.o -Wl,-rpath=/path/to/lib \
     -L/path/to/lib -lmylib
  ```

  The linker also reads the LD_RUN_PATH environment variable, if set, and thus you can specify more than one path in an easy way, without having to use the above linker option:

  ```
  export LD_RUN_PATH=/path/to/lib1:/path/to/lib2:/path/to/lib3
  cc -o myprog obj1.o ... objn.o -L/path/to/lib1 -lmylib1 \
     -L/path/to/lib2 -lmylib2 ...
  ```

  In both cases, you can check with ldd, that your executable will find the right libraries at start-up (see above). If there is a ‘not found’ message in the ldd output, you have done something wrong and should review your Makefile and/or your LD_RUN_PATH settings.

- There are tools around, to fix/change the runpath in a binary executable, e.g. chrpath under Linux. The problem with this method is, that the space in the executable that contains this information (i.e. the string defining the path) cannot be extended, i.e. you cannot add additional information – only overwrite an existing path. Furthermore, if no runpath exists in the executable, there is no way to change it. Read the man page for chrpath for more information.

- If you can’t fix the executable, create a wrapper script that calls the executable with the right LD_LIBRARY_PATH setting. In that way, the setting gets exposed to this application, only – and the applications that get started by that. The latter can lead to the inconsistency problem above, though.

  ```
  #!/bin/sh
  LD_LIBRARY_PATH=/path/to/lib1:/path/to/lib2:/path/to/lib3
  export LD_LIBRARY_PATH
  exec /path/to/bin/myprog $@
  ```

- Testing a LD_LIBRARY_PATH from the command line:

  ```
  $ env LD_LIBRARY_PATH=/path/to/lib1:/path/to/lib2:/path/to/lib3 ./myprog
  ```

  <details>
  <summary>env usage</summary>   
      env -i VAR="RANDOM" /bin/bash
  	  Starts a new bash session with zero environment variables except $VAR
  </details>

  This sets LD_LIBRARY_PATH for this command only. Do NOT do:

  ```
  $ export LD_LIBRARY_PATH=/path/to/lib1:/path/to/lib2:/path/to/lib3
  $ ./myprog
  ```

  since this will pollute the shell environment for all consecutive commands!

- Never put LD_LIBRARY_PATH in your login profiles! In that way you will expose all the applications you start to this – probably problematic – path!

Unfortunately, some ISVs ship software, that puts global LD_LIBRARY_PATH settings into the system profiles during the installation, or they ask the user to add those settings to their profiles. Just say no! Try if you can solve the problem by other means, e.g. by creating a wrapper script, or tell the vendor to fix this problem.



