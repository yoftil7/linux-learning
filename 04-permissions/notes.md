# Linux Permissions & Ownership (04-permissions)

## 1. Permission Model (Core Concept)

Linux permissions are evaluated in this order:

1. **Owner**
2. **Group**
3. **Others**

Only **one** category applies — Linux does *not* merge permissions.

Permissions:

* `r` (4) → read
* `w` (2) → write
* `x` (1) → execute / enter (for directories)

Example:

```
-rw-r--r--
```

* Owner: read + write
* Group: read
* Others: read

---

## 2. Files vs Directories (Critical Difference)

### Files

* `r` → read file contents
* `w` → modify file contents
* `x` → execute file as a program

### Directories

* `r` → list filenames (`ls`)
* `w` → create/delete/rename files
* `x` → enter directory (`cd`) and access files

👉 **Execute (****`x`****) is the most important permission on directories.**

---

## 3. Numeric Permissions

| Number | Meaning |
| ------ | ------- |
| 7      | rwx     |
| 6      | rw-     |
| 5      | r-x     |
| 4      | r--     |
| 0      | ---     |

Example:

```
chmod 755 script.sh
```

---

## 4. Private Files

```
chmod 600 file1
```

Result:

```
-rw-------
```

* Owner: read/write
* Group & others: no access
* Typical for SSH keys, secrets, configs

---

## 5. Directory Permission Logic (Important)

Example:

```
chmod 300 dir
```

```
d-wx------
```

Meaning:

* No read → cannot list files
* Has execute → can enter directory
* Files can still be accessed **if name is known**

👉 Listing ≠ Access

---

## 6. Ownership (`chown`)

Ownership decides **which permission block applies**.

```
chown user:group file
chown -R user:group directory
```

If permissions block access, **ownership does not override them**.

---

## 7. Groups (Collaboration)

Groups allow controlled sharing:

* Files can be shared without world access
* Used heavily in production systems

```
chown :devs project_dir
chmod 770 project_dir
```

---

## 8. setuid (Run as Owner)

* Applies to **binaries only**
* Causes program to run with file owner privileges

Example:

```
-rwsr-xr-x /usr/bin/passwd
```

Why:

* Password changes require root privileges

Linux **blocks setuid on shell scripts** (security risk).

---

## 9. setgid (Group Inheritance)

### On directories (most important):

* New files inherit directory’s group

```
chmod 2775 shared_dir
```

```
drwxrwsr-x
```

Used for:

* Team directories
* Web servers
* CI/CD pipelines

---

## 10. Sticky Bit (Delete Protection)

Sticky bit:

* Users can delete **only their own files**

Example:

```
chmod +t /tmp
```

```
drwxrwxrwt
```

Used for:

* `/tmp`
* Shared writable directories

---

## 11. Professional Rules of Thumb

* ❌ Avoid `777`
* ✅ Use `setgid` for shared work
* ✅ Use sticky bit for safety
* Permissions > ownership
* Execute (`x`) on directories controls access

---

## 12. Interview Triggers

**Q:** Why does a directory need execute permission?
**A:** Execute allows entering and accessing files; read only lists names.

**Q:** Why is setuid disabled on scripts?
**A:** Prevents privilege escalation via race conditions.

**Q:** Difference between `777` and `1777`?
**A:** Sticky bit prevents deleting others’ files.

---

End of `04-permissions`

