# Guideline for evals

## Introduction

These guidelines are designed for Hive Helsinki students, but it can be extended for any school in the 42 Network. It is a tool to help and guide evaluations, covering **some** edge cases for some of the projects of the common core, but it obviously does not cover the entirety of the edge cases of the projects. It should also offer somewhat of a safer embark to the new students that are not quite sure of some of the things to be tested.

In here, you will find basic things to be tested (compilation with makefile, number of arguments, etc) and **some** edge cases to help you understand what to test FOR YOURSELF!

## Disclaimer

This document is not a substitute for your own critical thinking. This guideline is mostly an auxiliary tool for those who are going through a project that contains topics that were not visited yet; or just a little list with reminders of stuff to be checking for. However, as said, this is no substitute for your own tests and critical thinking.

## Index
(under construction)
1. [Makefile](https://github.com/FjjDessoyCaraballo/guideline_for_evals/blob/main/README.md#1-makefile);
3. [Illegal stuff](https://github.com/FjjDessoyCaraballo/guideline_for_evals/blob/main/README.md#2-illegal-stuff);


### General Guidelines for evaluations

For most times, you will be evaluating a program that will require input of all sorts. Below you will find a list of simple tests for basic things that should remain constant throughout the curriculum.

#### 1. Makefile

Makefile is perhaps the most important part because it compiles the whole project together.
##### 1.1. Makefile - simply making

This is the get go of every evaluation, because if the project cannot be made, it's an automatic failure. Therefore we should test the rules and possibility of relinking in the Makefile:

Running just make in these cases should return you the same result, regardless of the project:
```shell
$> make
``` 

```shell
$> make <executable_name>
```

```shell
$> make all
```

If all of these are returning a compiled project, then you should be able to move forward for more testing. However, if you find problems, you may need to read the Makefile more closely.
##### 1.2. Makefile - checking rules

Another simple set here is to run the necessary rules for almost all projects: `clean`, `fclean`, and `re`.  Try running multiple rules, such as:

```shell
$> make all clean
```

##### 1.3. Makefile - relinking

Checking for relinking can be simple if the user has not inserted `@` into the rules actions. But even if that is the case and you see that it only displays messages that are the result of an `echo`, you can run the command `make -n` (check the end of the item for what it does) before and after actually running make:

Negative case of relinking
```shell
$> make -n
echo "getting your stuff nice and tidy megaphone"
clang++ -Wall -Wextra -Werror -std=c++11 -o megaphone megaphone.cpp
$> make
getting your stuff nice and tidy megaphone
$> make: Nothing to be done for 'all'.
$> 
```

Positive case of relinking
```shell
$> make -n
echo "getting your stuff nice and tidy megaphone"
clang++ -Wall -Wextra -Werror -std=c++11 -o megaphone obj/megaphone.o
$> make
getting your stuff nice and tidy megaphone
$> make -n
echo "getting your stuff nice and tidy megaphone"
clang++ -Wall -Wextra -Werror -std=c++11 -o megaphone obj/megaphone.o
$> 
```

The specific thing one has to look at here is what the positive case shows in the second `make -n` run: `clang++ -Wall -Wextra -Werror -std=c++11 -o megaphone obj/megaphone.o`. This line specifically is showing you that the project was compiled _twice_, which should not be allowed under our standards.

- `Make -n`: This specific command is doing a "dry-run" on your Makefile. In lay-mans terms, it means that it's showing what it would do, without actually doing it.

##### 1.4. Makefile - must haves

This one is covered by the previous steps, but one must be completely sure of what is going on under the hood. For that, go to the Makefile and read it properly to see that `all`, `fclean`, `clean`, and `re` are all there and that there is a `$(NAME)` executable.

Yes, the executable has have a rule `$(NAME)` and not `$(EXECUTABLE)`, or anything else. Moreover, check that the command `.PHONY` targets are all there (`all`, `re`, `clean`, `fclean`).

Last, but not least, the Makefile must be with capital `M` as a established standard in the industry.
#### 2. Illegal stuff

##### 2.1. Checking for not allowed functions

If you have a big project ahead of you, it will be hard to check every single function that has been used throughout it. For that, one can search for specific functions in VS Code for `strlen()` and whatnot. However, there is an easier way: `nm -u`.

What does it do? It helps you find out which external symbols your file depends on. In short, it gives you most of the functions that are used that require external libraries. Check the example below:

```shell
$>$ nm -u ./minishell
                 U access@GLIBC_2.2.5
                 U add_history
                 U chdir@GLIBC_2.2.5
                 U clear_history
                 U close@GLIBC_2.2.5
                 U __ctype_b_loc@GLIBC_2.3
                 U dup2@GLIBC_2.2.5
                 U __errno_location@GLIBC_2.2.5
                 U execve@GLIBC_2.2.5
                 U exit@GLIBC_2.2.5
                 U fork@GLIBC_2.2.5
                 U free@GLIBC_2.2.5
                 U getcwd@GLIBC_2.2.5
                 U getpid@GLIBC_2.2.5
                 w __gmon_start__
                 U kill@GLIBC_2.2.5
                 U __libc_start_main@GLIBC_2.34
                 U malloc@GLIBC_2.2.5
                 U open@GLIBC_2.2.5
                 U perror@GLIBC_2.2.5
                 U pipe@GLIBC_2.2.5
                 U printf@GLIBC_2.2.5
                 U read@GLIBC_2.2.5
                 U readline
                 U rl_on_new_line
                 U rl_redisplay
                 U rl_replace_line
                 U signal@GLIBC_2.2.5
                 U wait@GLIBC_2.2.5
                 U write@GLIBC_2.2.5
$>

```

The result of the command is showing us that this project used `write()`, `wait()`, `malloc()`, `perror()`, `exit()`, and other functions. If you find functions that are not supposed to be there, that is hardly arguable and definitely a failure.
