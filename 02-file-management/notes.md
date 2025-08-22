# Week 1 – Day 2: File Management & File Viewing

## Key Concepts
- **Absolute Path**: starts from `/` (root), e.g. `/home/user/file.txt`
- **Relative Path**: depends on current directory (`pwd`), e.g. `../file.txt`
- **File Creation**:
  - `touch file.txt` → empty file
  - `echo "text" > file.txt` → write text into file
- **File Operations**:
  - `cp file1 file2` → copy
  - `mv old new` → move/rename
  - `rm file` / `rm -r dir` → delete (⚠️ careful)
- **Viewing Files**:
  - `cat`, `less`, `head -n`, `tail -n`
- **Wildcards (Globbing)**:
  - `*.txt` → all txt files
  - `file?.txt` → one-character match
  - `file[123].txt` → specific matches

## Exercises Recap
- Created `a.txt`, `b.txt`, `c.txt` with content using `echo`.
- Practiced copying, renaming, moving, deleting.
- Viewed content with `head`, `tail`, `less`.
- Tested wildcards with `*.txt`, `file?.txt`.

## Mini Project
- Created a `notes/` directory.
- Added files `day1.txt` → `day5.txt` with sample notes.
- Used wildcards to list and copy them into `archive/`.
- Deleted only `day3.txt` from `archive/`.

## Reflection / Interview Prep
**Q:** What’s the difference between absolute and relative paths?  
**A:** Absolute paths always start from `/` and are the same no matter where you are. Relative paths depend on your current working directory.

---

