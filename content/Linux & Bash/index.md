---
title: Linux
sub-title: Linux terminal commands
tags:
  - linux
  - terminal
  - bash
  - shell
draft: false
---
#### Print
`echo "Hello World"`

#### User Name
```bash
echo whoami
# harshwardhan
``` 

### What is a Terminal?
its just a program that accepts text based command as input and render text on the screen

### What is a Shell?
So if your terminal is just a program that lets you issue text-based commands and renders the output of those commands What is the program that runs those commands???

That's a shell.

Shell do a lot of things, but their main job is to interpret the commands you type and execute them.
##### REPL
> Shells often referred to as "REPL's". REPL stands for
- Read
- Eval(evaluate)
- Print
- Loop

### Expressions
```bash
expr 123456 + 7890
```

### Variables
```bash
name="Lane"
echo $name
```

### History
history of commands
`history`


## File System

```bash
pwd #(present working directory)
ls # list directories and files
cd # change directory
cd Downloads

```

### Absolute and Relative path
> absolute path starts with / because its the address from the root directory which don't change where ever you are. On the other hand relative path's are based on your current location in folder structure

### Cat command
> view contents of file

```bash
cat file1.txt
# concatinate the contents of multiple files and print them to terminal
cat file1.txt file2.txt
```

### Head and Tail Command

```bash
# head prints first n lines of a file.(default 10)
head -n 10 file1.txt 

# tail to print last n lines
tail -n 10 file2.txt
```



















## Reference Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/v392lEyM29A?si=JXUPbJYeP_7OeIPg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
