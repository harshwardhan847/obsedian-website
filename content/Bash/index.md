---
title: Bash Shell Scripting
tags:
  - bash
  - shell
  - scripting
  - terminal
---
##### What is Bash?
- A shell language
- Bourne Again Shell
- Easy Commands

`echo $SHELL` -> tells us the shell path

#### Variables:-

```bash
#!/bin/bash

## NOT GOOD >>
cp /my/location/from /my/location/to
cp /my/location/from/here /my/location/to/there

## BETTER

MY_LOCATION_FROM=/my/location/from
MY_LOCATION_TO=/my/location/to

cp $MY_LOCATION_FROM $MY_LOCATION_TO

cp "$MY_LOCATION_FROM/here" "$MY_LOCATION_TO/there"
```

#### User Input
```bash
#!/bin/bash

echo What is your first name?
read FIRST_NAME
echo What is your last name?
read LAST_NAME

echo Hello $FIRST_NAME $LAST_NAME
```

#### Positional Arguments

**Arguments are a specified positions**
	Commands can take in arguments at a specific, position, counting from one (0 reserved for the shell).

```bash
$ echo Hello There!
   0     1     2
```

##### Example:-

```bash
#!/bin/bash

echo Hello $1 $2

# run:-
# $ sh script.sh Harsh Wardhan
# -> Hello Harsh Wardhan
```

#### PIPING

send command output to another commands

```bash
echo Hello there | grep there
```

#### Output Redirection

**Symbols used** :-
- > symbol to write to a file
- >> to append to a file
```bash
echo Hello World! > hello.txt
echo Hello World >> hello.txt

# output:
# Hello World!
# Hello World
```

Use cases:-
- Logging to a logfile
- Dynamically creating (config) files

```bash
wc -w hello.txt
# 6 hello.txt
wc -w < hello.txt
# 6
cat << EOF
# > I will
# > write some
# > text here
# > EOF
# I will
# write some
# text here

wc -w <<< "Hello there wordcount!"
# 3
```




