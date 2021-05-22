## Special bash parameters and their meaning



| **Special bash parameter** | **Meaning**                                                  |
| -------------------------- | ------------------------------------------------------------ |
| **$!**                     | $! bash script parameter is used to reference the process ID of the most recently executed command in background. |
| **$$**                     | $$ is used to reference the process ID of bash shell itself  |
| **$#**                     | $# is quite a special bash parameter and it expands to a number of positional parameters in decimal. |
| **$0**                     | $0 bash parameter is used to reference the name of the shell or shell script. so you can use this if you want to print the name of shell script. |
| **$-**                     | $- (dollar hyphen) bash parameter is used to get current option flags specified during the invocation, by the set built-in command or set by the bash shell itself. Though this bash parameter is rarely used. |
| **$?**                     | $0 is one of the most used bash parameters and used to get the exit status of the most recently executed command in the foreground. By using this you can check whether your bash script is completed successfully or not. |
| **$_**                     | $_ (dollar underscore) is another special bash parameter and used to reference the absolute file name of the shell or bash script which is being executed as specified in the argument list. This bash parameter is also used to hold the name of mail file while checking emails. |
| **$@**                     | $@ (dollar at the rate) bash parameter is used to expand into positional parameters starting from one. When expansion occurs inside double-quotes, every parameter expands into separate words. |
| **$\***                    | $* (dollar star) this is similar to $@ special bash parameter only difference is when expansion occurs with double quotes, it expands to a single word with the value of each bash parameter separated by the first character of the IFS special environment variable. |