# Week 1 – Day 1: Linux Basics & Bash

## Key Concepts

- **What is Linux?**
  - Linux is an open-source operating system kernel.
  - Common distributions: Ubuntu, Fedora, Debian, CentOS.

- **Linux Filesystem Hierarchy**
  - `/` → root directory
  - `/bin` → essential binaries (commands like ls, cat)
  - `/home` → user directories
  - `/etc` → configuration files
  - `/tmp` → temporary files
  - `/var` → variable files like logs
  - `/usr` → user programs and data

- **Navigating the System**
  - `pwd` → show current working directory
  - `ls` → list files/folders
  - `cd <directory>` → change directory
  - `cd ..` → go up one directory
  - `tree /` → visualize filesystem structure

- **Creating & Managing Files/Directories**
  - `mkdir <folder>` → create folder
  - `touch <file>` → create empty file
  - `echo "text" > <file>` → write text to file
  - `cat <file>` → display file content
  - `less <file>` → scroll through content
  - `head <file>` / `tail <file>` → view beginning/end of file
  - `rm <file>` → delete file
  - `rm -r <folder>` → delete folder recursively

- **Counting Words and Lines**
  - `wc <file>` → count lines, words, and characters

## Mini Project: Filesystem Map
- `tree / > filesystem.txt` → output entire filesystem structure to a file
- Skim through `filesystem.txt` to recognize key directories

## Reflection / Interview Prep
**Q:** If `/bin/ls` is deleted, how can you still list files?  
**A:** Use other commands like `/bin/echo *` or `find .`, because `ls` is just one tool; the shell provides other ways.

---

