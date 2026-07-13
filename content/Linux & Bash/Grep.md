---
title: grep Cheat Sheet (with Examples)
tags:
  - grep
  - bash
  - filter
  - terminal
  - linux
  - scripting
  - shell
draft: false
---

`grep` = **Global Regular Expression Print**

Search for patterns inside files or command outputs.

---

# Basic Syntax

```bash
grep [OPTIONS] PATTERN FILE
```

Example:

```bash
grep "error" logs.txt
```

---

# Search in a File

```bash
grep "hello" file.txt
```

Output

```
hello world
hello everyone
```

---

# Case Insensitive

```bash
grep -i "error" logs.txt
```

Matches

```
ERROR
Error
error
```

---

# Show Line Numbers

```bash
grep -n "main" app.js
```

Output

```
14:function main() {
56:main();
```

---

# Recursive Search

Search every file inside a directory.

```bash
grep -r "TODO" .
```

or

```bash
grep -R "TODO" .
```

---

# Count Matches

```bash
grep -c "TODO" app.js
```

Output

```
7
```

---

# Invert Match

Show lines that **don't** match.

```bash
grep -v "^#" config.conf
```

Useful for removing comments.

Input

```
# Comment
port=8080
# Another
host=localhost
```

Output

```
port=8080
host=localhost
```

---

# Match Whole Word

```bash
grep -w "cat" words.txt
```

Matches

```
cat
```

Not

```
category
concatenate
```

---

# Only Print the Match

```bash
echo "Email: john@gmail.com" | grep -o "[a-zA-Z0-9._%+-]*@[a-zA-Z0-9.-]*"
```

Output

```
john@gmail.com
```

---

# Search Multiple Patterns

Method 1

```bash
grep -e "error" -e "warning" logs.txt
```

Method 2 (Regex)

```bash
grep -E "error|warning|fatal" logs.txt
```

---

# Extended Regular Expressions

```bash
grep -E "(GET|POST) /api"
```

Matches

```
GET /api
POST /api
```

---

# Exact Number of Context Lines

### After

```bash
grep -A 3 "ERROR" logs.txt
```

Shows 3 lines **after** the match.

---

### Before

```bash
grep -B 2 "ERROR" logs.txt
```

Shows 2 lines **before** the match.

---

### Before & After

```bash
grep -C 2 "ERROR" logs.txt
```

Shows 2 lines before and after.

---

# Search Specific File Types

```bash
grep -rn "useState" --include="*.jsx" .
```

---

```bash
grep -rn "TODO" --include="*.ts" .
```

---

# Exclude File Types

```bash
grep -rn "password" . --exclude="*.log"
```

---

# Exclude Directories

```bash
grep -rn "API_KEY" . --exclude-dir=node_modules
```

---

# Search Multiple Directories

```bash
grep -rn "TODO" src tests docs
```

---

# Search Multiple Files

```bash
grep "hello" file1.txt file2.txt
```

---

# Ignore Binary Files

```bash
grep -I "password" .
```

---

# Show Matching File Names Only

```bash
grep -l "TODO" *.js
```

Output

```
app.js
utils.js
```

---

# Show Files Without Match

```bash
grep -L "TODO" *.js
```

---

# Highlight Matches

```bash
grep --color=always "TODO" app.js
```

---

# Quiet Mode (Exit Status Only)

Useful in shell scripts.

```bash
grep -q "success" output.log
```

Example

```bash
if grep -q "error" logs.txt; then
    echo "Found error"
fi
```

---

# Fixed String Search (Faster)

Don't treat pattern as regex.

```bash
grep -F "user.name()" app.js
```

---

# Search from Command Output

```bash
ps aux | grep chrome
```

---

```bash
history | grep git
```

---

```bash
git log | grep "fix"
```

---

# Search Hidden Files Too

```bash
grep -rn "API_KEY" .
```

(`grep -r` traverses hidden files and directories unless excluded.)

---

# Useful Regular Expressions

### Starts With

```bash
grep "^Hello" file.txt
```

---

### Ends With

```bash
grep "world$" file.txt
```

---

### Empty Lines

```bash
grep "^$" file.txt
```

---

### Non-empty Lines

```bash
grep -v "^$" file.txt
```

---

### Digits

```bash
grep "[0-9]" file.txt
```

---

### Exactly 3 Digits

```bash
grep -E "^[0-9]{3}$" file.txt
```

---

### Email

```bash
grep -E "[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}"
```

---

### IPv4 Address (Simple)

```bash
grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}"
```

---

# Common Developer Examples

Find every TODO

```bash
grep -rn "TODO" .
```

---

Find React hooks

```bash
grep -rn "useEffect" src
```

---

Find API keys

```bash
grep -rn "API_KEY" .
```

---

Find console logs

```bash
grep -rn "console.log" src
```

---

Find environment variables

```bash
grep -rn "process.env" .
```

---

Find import statements

```bash
grep "^import" app.js
```

---

Count JavaScript files containing TODO

```bash
grep -rl "TODO" . | wc -l
```

---

Search Git history

```bash
git log --oneline | grep "fix"
```

---

# Exit Codes

|Exit Code|Meaning|
|---|---|
|`0`|Match found|
|`1`|No match|
|`2`|Error occurred|

Useful in shell scripts:

```bash
grep -q "error" logs.txt

if [ $? -eq 0 ]; then
    echo "Error found"
fi
```

---

# Most Useful Options at a Glance

|Option|Description|
|---|---|
|`-i`|Ignore case|
|`-n`|Show line numbers|
|`-r` / `-R`|Recursive search|
|`-v`|Invert match|
|`-w`|Whole word|
|`-o`|Print only matched text|
|`-c`|Count matches|
|`-l`|Print matching file names|
|`-L`|Print non-matching file names|
|`-A N`|N lines after match|
|`-B N`|N lines before match|
|`-C N`|N lines before & after|
|`-e`|Multiple patterns|
|`-E`|Extended regex|
|`-F`|Fixed string (no regex)|
|`-q`|Quiet mode|
|`--include`|Search only matching file types|
|`--exclude`|Skip matching files|
|`--exclude-dir`|Skip directories|
|`--color=always`|Highlight matches|
|`-I`|Ignore binary files|

> **Pro tip:** `grep` is often combined with other Unix tools like `find`, `xargs`, `sort`, `uniq`, `awk`, and `sed` to build powerful command-line pipelines.