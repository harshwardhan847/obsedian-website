---
title: Bash Cheat Sheet
tags:
  - bash
  - terminal
  - scripting
  - shell
  - linux
---
# Bash & Terminal Cheat Sheet for Programmers

> A complete reference from beginner to advanced.

---

# Table of Contents

1. Terminal Basics
2. File & Directory Management
3. Viewing Files
4. Searching
5. Copying & Moving
6. Permissions
7. Process Management
8. Disk Usage
9. Networking
10. Archives & Compression
11. Text Processing
12. Pipes & Redirection
13. Environment Variables
14. Shell Shortcuts
15. Package Managers
16. SSH
17. Git Related Commands
18. Symbolic Links
19. Jobs
20. Process Signals
21. Useful Utilities
22. Advanced Bash Features
23. Bash Scripting
24. Terminal Productivity
25. Advanced Concepts Every Developer Should Know

---

# 1. Terminal Basics

## Print current directory

```bash
pwd
```

Output

```
/Users/harsh/projects
```

---

## List files

```bash
ls
```

Detailed view

```bash
ls -l
```

Show hidden files

```bash
ls -la
```

Human readable

```bash
ls -lh
```

Sort by modification date

```bash
ls -lt
```

Recursive

```bash
ls -R
```

---

## Clear screen

```bash
clear
```

or

```
Ctrl + L
```

---

# 2. File & Directory Management

## Create directory

```bash
mkdir folder
```

Nested directories

```bash
mkdir -p src/components/ui
```

---

## Create file

```bash
touch file.txt
```

---

## Remove file

```bash
rm file.txt
```

---

## Remove directory

```bash
rm -r folder
```

Force delete

```bash
rm -rf folder
```

⚠️ Dangerous command.

---

## Copy

```bash
cp file.txt backup.txt
```

Recursive copy

```bash
cp -r folder backup
```

---

## Move / Rename

```bash
mv old.txt new.txt
```

Move directory

```bash
mv src project/
```

---

## Change directory

```bash
cd folder
```

Parent

```bash
cd ..
```

Home

```bash
cd ~
```

Previous directory

```bash
cd -
```

Root

```bash
cd /
```

---

# 3. Viewing Files

## cat

Print entire file

```bash
cat file.txt
```

Concatenate

```bash
cat a.txt b.txt
```

---

## less

```bash
less huge.log
```

Scrollable viewer.

---

## more

```bash
more file.txt
```

---

## head

First 10 lines

```bash
head file.txt
```

First 50

```bash
head -50 file.txt
```

---

## tail

Last 10 lines

```bash
tail file.txt
```

Live logs

```bash
tail -f app.log
```

---

# 4. Searching

## find

Find file

```bash
find . -name "*.js"
```

Directories

```bash
find . -type d
```

Large files

```bash
find . -size +100M
```

---

## locate

```bash
locate node
```

Very fast.

---

## which

```bash
which node
```

Shows executable path.

---

## whereis

```bash
whereis python
```

---

## grep

Search inside files

```bash
grep "hello" file.txt
```

Recursive

```bash
grep -r "TODO" .
```

Ignore case

```bash
grep -i hello file.txt
```

Show line numbers

```bash
grep -n error app.log
```

Regex

```bash
grep "^import" file.js
```

---

# 5. Permissions

View permissions

```bash
ls -l
```

Example

```
-rwxr-xr-x
```

Meaning

```
Owner
Group
Others
```

---

Change permission

```bash
chmod 755 file
```

Recursive

```bash
chmod -R 755 folder
```

---

Change owner

```bash
chown user file
```

---

# 6. Process Management

List processes

```bash
ps
```

All

```bash
ps aux
```

Interactive

```bash
top
```

Better version

```bash
htop
```

---

Kill process

```bash
kill PID
```

Force

```bash
kill -9 PID
```

Kill by name

```bash
pkill node
```

---

Background

```bash
command &
```

---

# 7. Disk Usage

Filesystem

```bash
df -h
```

Folder sizes

```bash
du -sh *
```

Single folder

```bash
du -sh node_modules
```

---

# 8. Networking

IP

```bash
ifconfig
```

Linux

```bash
ip addr
```

---

Ping

```bash
ping google.com
```

---

DNS lookup

```bash
nslookup google.com
```

---

HTTP request

```bash
curl https://example.com
```

Download

```bash
curl -O url
```

---

Download

```bash
wget url
```

---

Open port

```bash
lsof -i :3000
```

Kill process on port

```bash
kill -9 $(lsof -ti:3000)
```

---

# 9. Archives

Zip

```bash
zip -r archive.zip folder
```

Unzip

```bash
unzip archive.zip
```

---

Tar

Create

```bash
tar -cvf archive.tar folder
```

Extract

```bash
tar -xvf archive.tar
```

Compressed

```bash
tar -czvf archive.tar.gz folder
```

Extract

```bash
tar -xzvf archive.tar.gz
```

---

# 10. Text Processing

## wc

Count lines

```bash
wc file.txt
```

Only lines

```bash
wc -l file.txt
```

---

## sort

```bash
sort file.txt
```

---

## uniq

```bash
uniq file.txt
```

---

## cut

```bash
cut -d "," -f2 users.csv
```

---

## tr

Replace

```bash
tr a-z A-Z
```

---

## sed

Replace

```bash
sed 's/old/new/g'
```

Modify file

```bash
sed -i '' 's/old/new/g' file.txt
```

(macOS syntax)

---

## awk

Print first column

```bash
awk '{print $1}'
```

CSV

```bash
awk -F, '{print $2}'
```

---

# 11. Pipes & Redirection

Pipe

```bash
cat file | grep hello
```

---

Output

```bash
>
```

Append

```bash
>>
```

Input

```bash
<
```

Error

```bash
2>
```

Redirect all

```bash
command > output.txt 2>&1
```

---

Example

```bash
ls | grep txt
```

---

# 12. Environment Variables

View

```bash
env
```

Echo

```bash
echo $HOME
```

Create

```bash
export API_KEY=123
```

Permanent

```
~/.bashrc
~/.zshrc
```

---

# 13. Shell Shortcuts

```
Tab            Auto complete
Ctrl+C         Stop process
Ctrl+D         Exit shell
Ctrl+R         Search history
Ctrl+A         Beginning
Ctrl+E         End
Ctrl+K         Delete to end
Ctrl+U         Delete to start
Ctrl+W         Delete previous word
Ctrl+L         Clear
!!             Previous command
!100           Run command 100
history        Command history
```

---

# 14. Package Managers

Node

```bash
npm
pnpm
yarn
bun
```

Python

```bash
pip
pip3
```

Ubuntu

```bash
apt
```

macOS

```bash
brew
```

---

# 15. SSH

Connect

```bash
ssh user@host
```

Generate key

```bash
ssh-keygen
```

Copy key

```bash
ssh-copy-id user@host
```

---

# 16. Git Helpers

Open repository root

```bash
git rev-parse --show-toplevel
```

Current branch

```bash
git branch
```

Recent commits

```bash
git log --oneline
```

---

# 17. Symbolic Links

Create

```bash
ln -s source target
```

Example

```bash
ln -s ~/.config/nvim ~/nvim
```

---

# 18. Jobs

Show jobs

```bash
jobs
```

Background

```
Ctrl+Z
bg
```

Foreground

```bash
fg
```

---

# 19. Signals

```
SIGINT
SIGTERM
SIGKILL
SIGSTOP
SIGHUP
SIGCONT
```

Send signal

```bash
kill -SIGTERM PID
```

---

# 20. Useful Utilities

Current user

```bash
whoami
```

Current date

```bash
date
```

Calendar

```bash
cal
```

Uptime

```bash
uptime
```

Memory

```bash
free -h
```

(macOS)

```bash
vm_stat
```

Open Finder

```bash
open .
```

Linux

```bash
xdg-open .
```

---

# 21. Advanced Bash Features

## Wildcards

```
*
?
[]
{}
```

Example

```bash
*.js
```

---

Brace expansion

```bash
mkdir project/{src,public,test}
```

Creates

```
project/
    src
    public
    test
```

---

Command substitution

```bash
echo $(pwd)
```

---

Arithmetic

```bash
echo $((5+10))
```

---

Aliases

```bash
alias ll="ls -lah"
```

---

Functions

```bash
hello(){
    echo "Hello"
}
```

---

Source file

```bash
source ~/.zshrc
```

---

# 22. Bash Scripting

Shebang

```bash
#!/bin/bash
```

Variable

```bash
name="Harsh"
```

Input

```bash
read name
```

Condition

```bash
if
```

Loop

```bash
for
```

While

```bash
while
```

Exit code

```bash
$?
```

---

# 23. Useful One-Liners

Find largest files

```bash
find . -type f | xargs du -h | sort -hr | head
```

Delete node_modules

```bash
find . -name node_modules -type d -prune -exec rm -rf '{}' +
```

Find TODOs

```bash
grep -rn "TODO" .
```

Watch logs

```bash
tail -f logs/app.log
```

Git branches

```bash
git branch
```

Open VS Code

```bash
code .
```

Open current folder

```bash
open .
```

---

# 24. Advanced Concepts

## stdin

Standard Input

```
Keyboard
```

---

## stdout

Standard Output

```
Screen
```

---

## stderr

Error Output

```
Error Stream
```

---

## Pipes

```
Program A
      │
      ▼
Program B
```

---

## Redirection

```
stdout
    │
    ▼
 file.txt
```

---

## Process

Running program.

Each has a unique PID.

---

## Daemon

Background service.

Examples

```
Docker
SSH
Cron
```

---

## Exit Codes

```
0  Success

1+ Failure
```

Check

```bash
echo $?
```

---

## PATH

```
/usr/bin
/usr/local/bin
```

Terminal searches these folders for commands.

View

```bash
echo $PATH
```

---

## Shell

```
bash
zsh
fish
```

The shell interprets your commands before executing programs.

---

## TTY

The terminal session attached to your shell. Historically a physical teletype, now a virtual terminal.

---

## Login vs Non-login Shell

- **Login shell**: Reads login configuration files (e.g., `.bash_profile`, `.zprofile`).
- **Interactive shell**: Reads interactive configuration (e.g., `.bashrc`, `.zshrc`).
- Understanding this explains why some aliases or environment variables only appear in certain terminals.

---

## Shell Expansion Order

When you type:

```bash
echo ~/project/*.js "$HOME"
```

Bash performs expansions in a specific order:

1. Brace expansion
2. Tilde expansion
3. Variable expansion
4. Command substitution
5. Arithmetic expansion
6. Word splitting
7. Filename (glob) expansion

Knowing this helps explain many "weird" shell behaviors.

---

## Subshells

Parentheses create a subshell:

```bash
(cd project && npm install)
```

Changes inside do not affect the parent shell.

---

## Current Shell

Curly braces execute in the current shell:

```bash
{
    cd project
    npm install
}
```

Directory changes persist afterward.

---

## File Descriptors

```
0 → stdin
1 → stdout
2 → stderr
```

Examples:

```bash
command > out.txt
command 2> errors.txt
command > out.txt 2>&1
```

---

## Globbing

The shell expands patterns before executing commands.

```
*.js
*.md
file?.txt
```

---

## Quoting

Single quotes prevent expansion:

```bash
echo '$HOME'
```

Output:

```
$HOME
```

Double quotes allow expansion:

```bash
echo "$HOME"
```

---

## xargs

Convert stdin into command arguments.

Example:

```bash
find . -name "*.log" | xargs rm
```

---

## tee

Write output to both terminal and file.

```bash
npm test | tee output.log
```

---

## nohup

Keep a process running after closing the terminal.

```bash
nohup node server.js &
```

---

## Cron Jobs

Run scheduled tasks.

Edit:

```bash
crontab -e
```

List:

```bash
crontab -l
```

---

## File System Hierarchy

```
/
├── bin
├── usr
├── etc
├── var
├── home
├── tmp
├── dev
└── opt
```

Knowing what each directory is used for is extremely useful when debugging servers.

---

# 25. Commands Every Professional Developer Uses Daily

```bash
pwd
ls -la
cd
mkdir -p
rm -rf
cp -r
mv
touch
cat
less
head
tail -f
find
grep
awk
sed
curl
wget
ssh
scp
chmod
chown
ps
top
htop
kill
lsof
df
du
tar
zip
unzip
history
alias
source
env
export
echo
which
whereis
xargs
tee
nohup
jobs
bg
fg
git
code .
open .
```

---

# Recommended Learning Order

```
✓ Navigation
✓ File Management
✓ Pipes
✓ grep
✓ find
✓ Redirection
✓ Permissions
✓ Processes
✓ Environment Variables
✓ SSH
✓ curl
✓ sed
✓ awk
✓ xargs
✓ Bash Scripting
✓ Cron
✓ Signals
✓ File Descriptors
✓ Subshells
✓ Shell Expansions
✓ Process Management
```

---

# Final Advice

A programmer becomes productive in the terminal by mastering a few powerful ideas rather than memorizing hundreds of commands:

- Everything is a file.
- Commands can be chained together using pipes.
- Standard input, output, and error are separate streams.
- The shell performs expansions before executing commands.
- Small utilities can be composed to solve complex problems.
- Learn `grep`, `find`, `sed`, `awk`, `xargs`, `curl`, and `ssh` deeply—they provide the biggest productivity gains.