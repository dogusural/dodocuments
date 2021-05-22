## What is zombie process ?

A process which has finished the execution but still has entry in the process table to report to its parent process is known as a zombie process. A child process always first becomes a zombie before being removed from the process table. The parent process reads the exit status of the child process which reaps off the child process entry from the process table.

In the following code, the child finishes its execution using exit() system call while the parent sleeps for 50 seconds, hence doesn’t call [wait()](https://en.wikipedia.org/wiki/Wait_(system_call)) and the child process’s entry still exists in the process table.

```
int main() 
{ 
    // Fork returns process id 
    // in parent process 
    pid_t child_pid = fork(); 
  
    // Parent process  
    if (child_pid > 0) 
        sleep(50); 
  
    // Child process 
    else        
        exit(0); 
  
    return 0; 
} 
```

When the parent dies after 50 second, the child process's ownership is transferred to init process and becomes a orphaned process. When a process is orphaned the init process does  **wait** on the child and removes it from the PCB (Process Control Block).

A zombie process can only happen when the parent is alive , the child is dead and hasn't been properly wait'ed.

To prevent it , three different measures can be taken

- Wait can be applied to each child that was forked.

  - sample.c

  ```
  #include <stdio.h>
  #include <stdlib.h>
  #include <unistd.h>
  #include <sys/wait.h>
  
  void exit_sys(const char *msg);
  
  int main(void)
  {
      pid_t pid1, pid2, pid3, pid_result;
      int status;
      int i;
  
      printf("sample starts...\n");
  
      if ((pid1 = fork()) == -1)
          exit_sys("fork");
  
      if (pid1 == 0 && execl("mample", "mample", (char *)NULL) == -1)
          exit_sys("execl");
  
      if ((pid2 = fork()) == -1)
          exit_sys("fork");
  
      if (pid2 == 0 && execl("/bin/ls", "/bin/ls", "-l", (char *)NULL) == -1)
          exit_sys("execl");
  
      if( (pid3 = fork()) == -1)
          exit_sys("fork");
  
      if(pid3 == 0 && execl("/bin/cat", "/bin/cat", "mample.c", (char *)(NULL)) == -1)
          exit_sys("execl");
      
          while ( (pid_result = wait(&status)) > 0)
          {
              printf("%d is the pid of the terminated process\n",pid_result);
              if (WIFEXITED(status))
                  printf("%s terminated normally: %d\n", pid_result == pid1 ? "mample" : "ls", WEXITSTATUS(status));
              else 
                  printf("%s Child terminated abnormally!..\n", pid_result == pid1 ? "mample" : "ls");
          }
          
   
      return 0;
  }
  
  void exit_sys(const char *msg)
  {
      perror(msg);
  
      exit(EXIT_FAILURE);
  }
  ```

  ------

  - mample.c

  ```
  #include <stdio.h>
  #include <stdlib.h>
  #include <unistd.h>
  
  int main(void)
  {
      int i;
  
      printf("mample starts\n");
      
      for (i = 0; i < 5; ++i) {
          printf("%d\n", i);
          sleep(1);
      }
  
      return 100;
  }
  
  ```

- When a child terminates , it sends SIGCHILD to the parent. Parent can handle the signal inside the signal handler function and instead of doing a blocking call, it can handle the child reaping, inside the handler in a nonblocking manner.

  ```
  #include <stdio.h>
  #include <signal.h>
  #include <sys/wait.h>
  #include <sys/resource.h>
  
  void proc_exit()
  {
  		int wstat;
  		union wait wstat;
  		pid_t	pid;
  
  		while (TRUE) {
  			pid = wait3 (&wstat, WNOHANG, (struct rusage *)NULL );
  			if (pid == 0)
  				return;
  			else if (pid == -1)
  				return;
  			else
  				printf ("Return code: %d\n", wstat.w_retcode);
  		}
  }
  main ()
  {
  		signal (SIGCHLD, proc_exit);
  		switch (fork()) {
  			case -1:
  				perror ("main: fork");
  				exit (0);
  			case 0:
  				printf ("I'm alive (temporarily)\n");
  				exit (rand());
  			default:
  				pause();
  		}
  }
  ```

- The child can be detached from the parent with the ``` sid = setsid();``` call. It will run the program in new session .

  

#### wait and waitpid difference

**wait() and waitpid()**

The **wait**() system call suspends execution of the calling process until one of its children terminates. The call *wait(&status)* is equivalent to:`waitpid(-1, &status, 0);`The **waitpid**() system call suspends execution of the calling process until a child specified by *pid* argument has changed state. By default, **waitpid**() waits only for terminated children, but this behavior is modifiable via the *options* argument, as described below.The value of *pid* can be:

`< -1 `

meaning wait for any child process whose process group ID is equal to the absolute value of *pid*.

------

`-1`

meaning wait for any child process.

------

`0`

meaning wait for any child process whose process group ID is equal to that of the calling process.

------

`< 0`

meaning wait for the child whose process ID is equal to the value of *pid*.

------



The value of *options* is an OR of zero or more of the following constants:

- **WNOHANG**

  return immediately if no child has exited.

- **WUNTRACED** 

  also return if a child has stopped (but not traced via **[ptrace](https://linux.die.net/man/2/ptrace)**(2)). Status for *traced* children which have stopped is provided even if this option is not specified.

- **WCONTINUED** (since Linux 2.6.10)

  also return if a stopped child has been resumed by delivery of **SIGCONT**.

  (For Linux-only options, see below.)If *status* is not NULL, **wait**() and **waitpid**() store status information in the *int* to which it points. This integer can be inspected with the following macros (which take the integer itself as an argument, not a pointer to it, as is done in **wait**() and **waitpid**()!):

- **WIFEXITED(***status***)**

  returns true if the child terminated normally, that is, by calling **[exit](https://linux.die.net/man/3/exit)**(3) or **[_exit](https://linux.die.net/man/2/_exit)**(2), or by returning from main().

- **WEXITSTATUS(***status***)**

  returns the exit status of the child. This consists of the least significant 8 bits of the *status* argument that the child specified in a call to **[exit](https://linux.die.net/man/3/exit)**(3) or **[_exit](https://linux.die.net/man/2/_exit)**(2) or as the argument for a return statement in main(). This macro should only be employed if **WIFEXITED** returned true.

- **WIFSIGNALED(***status***)**

  returns true if the child process was terminated by a signal.

- **WTERMSIG(***status***)**

  returns the number of the signal that caused the child process to terminate. This macro should only be employed if **WIFSIGNALED** returned true.

- **WCOREDUMP(***status***)**

  returns true if the child produced a core dump. This macro should only be employed if **WIFSIGNALED** returned true. This macro is not specified in POSIX.1-2001 and is not available on some UNIX implementations (e.g., AIX, SunOS). Only use this enclosed in #ifdef WCOREDUMP ... #endif.

- **WIFSTOPPED(***status***)**

  returns true if the child process was stopped by delivery of a signal; this is only possible if the call was done using **WUNTRACED** or when the child is being traced (see **[ptrace](https://linux.die.net/man/2/ptrace)**(2)).

- **WSTOPSIG(***status***)**

  returns the number of the signal which caused the child to stop. This macro should only be employed if **WIFSTOPPED** returned true.

- **WIFCONTINUED(***status***)**

  (since Linux 2.6.10) returns true if the child process was resumed by delivery of **SIGCONT**.

  

#### SIGHUP and the child process

When the parent terminates, the existing child processes are adopted by the init process, their lifetime is detached from the parent's by default. But if the parent process is the terminal emulator or the terminal, upon exit. It sends **SIGHUP** to all the existing children, whose default behavior is to terminate the process.

The children either needs to handle and ignore the **SIGHUP** signal or **nohup** should be used on the terminal when calling the application. 



### Process Control Block

While creating a process the operating system performs several operations. To identify the processes, it assigns a process identification number (PID) to each process. As the operating system supports multi-programming, it needs to keep track of all the processes. For this task, the process control block (PCB) is used to track the process’s execution status. Each block of memory contains information about the process state, program counter, stack pointer, status of opened files, scheduling algorithms, etc. All these information is required and must be saved when the process is switched from one state to another. When the process makes a transition from one state to another, the operating system must update information in the process’s PCB.

A process control block (PCB) contains information about the process, i.e. registers, quantum, priority, etc. The process table is an array of PCB’s, that means logically contains a PCB for all of the current processes in the system.

<img src="process-table.jpg" alt="img" style="zoom:40%;" />

- **Pointer –** It is a stack pointer which is required to be saved when the process is switched from one state to another to retain the current position of the process.
- **Process state –** It stores the respective state of the process.
- **Process number –** Every process is assigned with a unique id known as process ID or PID which stores the process identifier.
- **Program counter –** It stores the counter which contains the address of the next instruction that is to be executed for the process.
- **Register –** These are the CPU registers which includes: accumulator, base, registers and general purpose registers.
- **Memory limits –** This field contains the information about memory management system used by operating system. This may include the page tables, segment tables etc.
- **Open files list –** This information includes the list of files opened for a process.

PCB is saved as a linked-list in the kernel space. User can check under /proc to see individual folders created with exported information from the kernel space to get information about the inquired process.