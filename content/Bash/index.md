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
- '>' symbol to write to a file
- '>>' symbol to append to a file
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

#### Test Operators

```bash
[hello = hello]
echo $? # gives exit code of last executed command
# 0 // means command was executed without any issues
[1 = 0]
echo $?
# 0
[1 -eq 1]
echo $?
# 0
```

#### Conditionals (if/elif/else)

```bash
#!/bin/bash

if [ ${1,,} = harsh ]; then
	echo "Oh, you're the boss here. Welcome!"
elif [ ${1,,} = help ]; then
	echo "Just enter your username, duh!"
else
	echo "I don't know who you are. But you'are not the boss of me!"
fi
```

#### Case Statements

```bash
#!/bin/bash

case ${1,,} in
	harsh | administrator)
		echo "Hello, you'r the boss here!"
		;;
	help)
		echo "Just enter your username!"
		;;
	*)
		echo "Hello there. You're not the boss of me. Enter a valid username!"
esac

# ./login.sh harsh
# > Hello you're the boss here!

```

#### Arrays




