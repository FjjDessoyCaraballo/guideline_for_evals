# Guideline for evals

## Introduction

These guidelines are designed for Hive Helsinki students, but it can be extended for any school in the 42 Network. It is a tool to help and guide evaluations, covering **some** edge cases for some of the projects of the common core, but it obviously does not cover the entirety of the edge cases of the projects. It should also offer somewhat of a safer embark to the new students that are not quite sure of some of the things to be tested.

In here, you will find basic things to be tested (compilation with makefile, number of arguments, etc) and **some** edge cases to help you understand what to test FOR YOURSELF!

## Disclaimer

This document is not a substitute for your own critical thinking. This guideline is mostly an auxiliary tool for those who are going through a project that contains topics that were not visited yet; or just a little list with reminders of stuff to be checking for. However, as said, this is no substitute for your own tests and critical thinking.

## Index
(under construction)
1. [Makefile](https://github.com/FjjDessoyCaraballo/guideline_for_evals/blob/main/README.md#1-makefile);
2. [Illegal stuff](https://github.com/FjjDessoyCaraballo/guideline_for_evals/blob/main/README.md#2-illegal-stuff);
3. Input sanitization


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

#### 3. Input sanitization

This section will be limited because it needs context most of the times. Therefore we are going through just two input handlings: number of arguments and sanitizing the argument values.

##### 3.1. Number of arguments

This is a rather easy check. So easy that some people writing their codes overlook it. As you pass from testing the makefile, you may want to dedicate two minutes to see the behavior of the code when given a handful of wrong arguments. To illustrate, we shall use the philosophers project as an example, which takes 4 to 5 arguments:

```shell
$>  ./philo 5 310 200 100
0 1 is thinking
0 5 is thinking
0 2 has taken a fork
0 2 has taken a fork
0 2 is eating
0 3 is thinking
0 4 has taken a fork
0 4 has taken a fork
0 4 is eating
20 5 has taken a fork
200 4 is sleeping
200 2 is sleeping
200 5 has taken a fork
200 5 is eating
200 1 has taken a fork
200 3 has taken a fork
200 3 has taken a fork
200 3 is eating
300 4 is thinking
300 2 is thinking
310 1 died
$> 
```
This test was a positive case, so we expected to get something out of this run. But what happens when we start messing with it?

```shell
$> ./philo
Wrong number of arguments: must be four of five.
$> ./philo 4
Wrong number of arguments: must be four of five.
$> ./philo 4 100
Wrong number of arguments: must be four of five.
$> ./philo 4 100 100
Wrong number of arguments: must be four of five.
$> ./philo 4 100 100 100
0 2 has taken a fork
0 2 has taken a fork
0 2 is eating
0 1 is thinking
0 3 is thinking
0 4 has taken a fork
0 4 has taken a fork
0 4 is eating
100 3 died
$> ./philo 4 100 100 100 5
0 3 is thinking
0 2 has taken a fork
0 2 has taken a fork
0 2 is eating
0 1 is thinking
0 4 has taken a fork
0 4 has taken a fork
0 4 is eating
100 1 died
$> ./philo 4 100 100 100 5 5
Wrong number of arguments: must be four of five.
$> ./philo 4 100 100 100 5 5 5
Wrong number of arguments: must be four of five.
```
In this code the `argc` was well defined (i.e. `argc == 5 || argc == 6`) so no surprises came to us, but one should test these cases nonetheless because they are still prone to segmentation faults.

##### 3.2. Sanitizing the argument values

The type of arguments that your code can take is somewhat of a broad problem to solve, and it highly depends on context of the project that you have. However, you can check for a few shortcomings, such as trying to open directories instead of files, taking arguments in quotes, integer overflows, etc.

Lets consider that this project is about a specific program that takes a file, like the so_long project. Our file must be a `.ber` file, and this is already one thing to be testing:

- test for syntax of the file (map1.ber, .bermap1, map1ber, .mabep1r...);
- integrity of the file (empty file, too big of a file...);
- name a directory with ending with `.ber`;

These tests are a good way of testing how the input was parsed. If the project involves reading a file, it means that there will be a world of checks to be done by you and by the "evaluatee", like permissions and accessibility of the file.

Other times the input can be simply strings or numbers. This is the moment for you to test with basic things, such as `INT_MAX`, `INT_MIN`, and other stuff:

```terminal
$> ./push_swap 2147483647 −2147483648
sa
$> ./push_swap 2147483648 −2147483648
Error
$> ./push_swap 2147483647 −2147483649
Error
$> ./push_swap 1 -0 2
sa
```
(under work)
