

# Stream Buffers

By default, stdio is fully buffered, unless it's writing to a terminal, in which case it's line-buffered, or stderr, which is not buffered at all.

To flush all the contents of the buffer fflush function can be used. 



```#include <stdio.h> ```
```int fflush(FILE *stream);```

## MAN page of fflush

```c
       For output streams, fflush() forces a write of all user-space
       buffered data for the given output or update stream via the
       stream's underlying write function.

       For input streams associated with seekable files (e.g., disk
       files, but not pipes or terminals), fflush() discards any
       buffered data that has been fetched from the underlying file, but
       has not been consumed by the application.

       The open status of the stream is unaffected.

       If the stream argument is NULL, fflush() flushes all open output
       streams.
```

## File Descriptor numbers of standard stream

| Integer value |                          Name                           | <[unistd.h](https://en.wikipedia.org/wiki/Unistd.h)> symbolic constant[[1\]](https://en.wikipedia.org/wiki/File_descriptor#cite_note-1) | <[stdio.h](https://en.wikipedia.org/wiki/Stdio.h)> file stream[[2\]](https://en.wikipedia.org/wiki/File_descriptor#cite_note-2) |
| :-----------: | :-----------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|       0       |  [Standard input](https://en.wikipedia.org/wiki/Stdin)  |                         STDIN_FILENO                         |                            stdin                             |
|       1       | [Standard output](https://en.wikipedia.org/wiki/Stdout) |                        STDOUT_FILENO                         |                            stdout                            |
|       2       | [Standard error](https://en.wikipedia.org/wiki/Stderr)  |                        STDERR_FILENO                         |                            stderr                            |

## _(exit) before fflush

On call to exit or return from the main, the returned value is not returned to the operating system but to the startup function. At the end of the startup function of the process another exit(return(x)) value resides which returns the given value to the parent process. Again the return value is designed for the caller process not the operating system. Within that return value, the reason of termination and the value of the return code can be found.



the exit function calls the POSIX _exit() function itself .Before the call to the posix function it flushes the open file descriptors . You can observer from the example below that if we call _exit() directly rather than the C standard library exit, contents of the buffer wont be flushed to the terminal and will be permanently deleted.



```void exit_sys(const char *msg);

int main(void)
{
    FILE *f;

    if ((f = fopen("test.txt", "w")) == NULL) 
        exit_sys("fopen");

    fprintf(f, "this is a test\n");
    fclose(f);

    _exit(0);

    return 0;
}

void exit_sys(const char *msg)
{
    perror(msg);

    exit(EXIT_FAILURE);
}
```



Programmer can either call exit() instead or call fflush(f) | fclose(f) before exiting. 