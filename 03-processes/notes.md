# 📝 Notes – Linux Processes

## 🔹 What is a Process?
- A **process** is a running instance of a program.
- Each process has:
  - **PID (Process ID)** – unique identifier.
  - **PPID (Parent Process ID)** – the process that started it.
  - **State** – running, sleeping, stopped, zombie.

---

## 🔹 Foreground vs Background
- **Foreground**: Takes over your terminal until it finishes.
- **Background**: Frees up your terminal, runs behind the scenes.
  ```bash
  sleep 60        # foreground
  sleep 60 &      # background
  jobs            # list background jobs
  fg %1           # bring job 1 to foreground
  bg %1           # continue job 1 in background


Managing Processes

List processes
ps aux          # all processes with details
ps -p <PID>     # details of a single process
top / htop      # dynamic view of processes

Signals

kill -15 <PID>  # polite termination (default TERM)
kill -9 <PID>   # force kill (KILL)
kill -STOP <PID> # pause process
kill -CONT <PID> # continue paused process


Monitoring Tools

top: built-in, shows CPU, memory usage, and top processes.

htop (better version):

CPU cores & memory usage bars.

Sort by CPU or MEM (F6).

Kill process interactively (F9).

Key metrics to watch:

%CPU: how much CPU the process consumes.

%MEM: how much memory is used.

STAT: process state (R=running, S=sleeping, T=stopped).

🔹 /proc Filesystem (Process Info)

/proc = virtual filesystem exposing kernel & process info.

Example:
/proc/<PID>/cmdline   # command that started the process
/proc/<PID>/status    # detailed info (state, memory, uid, etc.)
/proc/<PID>/fd/       # open files by the process

Why is this useful?

Background jobs free your terminal → run long tasks while continuing work.

PIDs let you precisely control processes (pause, resume, kill).

Monitoring tools (htop, top) are used daily by sysadmins to troubleshoot slow systems.

/proc is where you investigate details (like what files a process has open).
