This is a very good Linux System Health & Monitoring practice module for a DevOps engineer.
===========================================================================================

The goal is not just to memorize commands. You should learn:
-------------------------------------------------------------

     Symptom → command → output → interpretation → next command → root cause

MISSION 1 — System Health & Monitoring
--------------------------------------

1. uptime — Is the server overloaded?
-------------------------------------
       uptime

Example:

    14:20:31 up 25 days, 4:12, 2 users, load average: 0.45, 0.60, 0.72

What does it tell you?

      14:20:31              current time
      up 25 days            server has been running for 25 days
      2 users               currently logged-in users
      load average:
      0.45  0.60  0.72
       |      |      |
       1m     5m     15m

Important concept: Load average

Suppose your server has:

      4 CPU cores

And:

    load average: 1.0, 1.5, 2.0

That's generally fine because the load is below 4.

But:

      load average: 8.0, 7.5, 6.5

means the system has more runnable/uninterruptible work than the CPUs can handle.

Don't blindly say "load > core count = CPU problem." Load can also rise because processes are stuck waiting on I/O.

Check CPU count:

    nproc

or:

    lscpu



2. free -h — Is the server running out of RAM?
   ----------------------------------------------

          free -h

Example:

                     total        used        free      shared    buff/cache   available
      Mem:            7.7Gi       5.2Gi       300Mi       200Mi       2.2Gi       2.1Gi
      Swap:           2.0Gi       500Mi       1.5Gi

The most important column is:

    available

Don't look only at:

    free

Linux intentionally uses unused memory for filesystem cache.

Example

          free = 300 MB
          available = 2.1 GB

This is not necessarily a memory problem.

Linux can reclaim cache when applications need memory.

Warning signs
available → very low
swap used → continuously increasing
si/so → active in vmstat
OOM killer messages → dmesg/journalctl




3. df -h — Is disk space running out?
   ----------------------------------

        df -h

Example:

    Filesystem      Size  Used Avail Use%
    /dev/xvda1       50G   46G  4G   92%

Here:

    Use% = 92%

That's a warning.

At:

    95%+

you should investigate urgently.

At:

    100%

applications can start failing.

Especially inspect:

    df -h /
    df -h /var

Why /var?

    Because logs, package data, Docker/container data, etc. often live there.

Also check inode exhaustion

    df -i

You can have:

    Disk space = 50% used

but:

    Inodes = 100%

and still be unable to create files.



4. top — What is consuming CPU/RAM?
   -------------------------------


Run:

    top

You'll see something similar to:

    %Cpu(s): 10.0 us, 3.0 sy, 0.0 ni, 80.0 id, 7.0 wa
    MiB Mem :  7980 total,  ...


Important CPU fields:


| Field | Meaning              |
| ----- | -------------------- |
| `us`  | User/application CPU |
| `sy`  | Kernel/system CPU    |
| `id`  | Idle                 |
| `wa`  | I/O wait             |
| `si`  | Software interrupt   |
| `hi`  | Hardware interrupt   |

Very important troubleshooting rule

    high us → application/process CPU consumption
    high sy → kernel/system activity
    high wa → I/O bottleneck

For example:

    %Cpu(s): 90 us, 3 sy, 0 id, 2 wa

Likely CPU-intensive application.

Whereas:

    %Cpu(s): 10 us, 5 sy, 5 id, 80 wa

CPU isn't necessarily the real problem.

Processes are spending significant time waiting for I/O.


htop
-----

htop - It's an interactive alternative to top.

Useful keys:

    F5 → tree view
    F6 → sort
    F9 → kill process

For example, you can sort by CPU and immediately identify:

    java
    python
    nginx
    postgres
    containerd

consuming resources.

Be careful with F9 in production.


6. vmstat — My favorite troubleshooting command
   ---------------------------------------------

       vmstat 1 5

Meaning:

    1 → every 1 second
    5 → collect 5 samples

Example:

    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
     r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
     2  0      0  500000  ...   ...     0    0    100   50  ...

Important fields:

r

Runnable processes.

Compare with CPU cores.

cores = 4
r = 20

Potential CPU pressure.

b

Processes blocked waiting for I/O.

b = 5

means processes are blocked.

si

Swap in.

so

Swap out.

If you see continuously:

si > 0
so > 0

you may have memory pressure.

wa

I/O wait.

Example:

us=20
sy=5
id=10
wa=65

Strong indication to investigate storage/I/O.


7. iostat — Is the disk saturated?
   -------------------------------

First check whether it's installed:

     iostat

If not:

Ubuntu/Debian:

      sudo apt install sysstat

Then:

      iostat -x 1 3

-x gives extended disk statistics.

Important:

    await
    %util

await

Average I/O request latency.

Roughly:

      <10 ms     generally good
      10-50 ms   investigate depending on workload
      >50 ms     potentially slow

These are rules of thumb, not universal thresholds.

%util

How busy the device is.

If:

    %util ≈ 100%

the device may be saturated.

Then correlate this with:

vmstat
top
iostat

Don't diagnose from %util alone.


8. dmesg — Kernel-level problems
   -----------------------------

        dmesg -T

Search for important errors:

      dmesg -T | grep -Ei 'oom|out of memory|error|fail|segfault|I/O'

You might find:

      Out of memory: Killed process 1234 (java)

That's extremely important.

    It means the Linux kernel's OOM killer terminated a process because the system was under severe memory pressure.

You may also find:

    I/O error

or network interface problems.


9. journalctl — System/service logs
    ------------------------------


For all errors since boot:

    journalctl -p err -b

For a particular service:

    journalctl -u nginx

Follow live logs:

    journalctl -u nginx -f

Errors only:

    journalctl -u nginx -p err

Current boot:

    journalctl -b

This is extremely useful when a service isn't behaving correctly.


10. systemctl — Is a service broken?
    -------------------------------

Check a service:

    systemctl status nginx

Start:

    sudo systemctl start nginx

Stop:

    sudo systemctl stop nginx

Restart:

    sudo systemctl restart nginx

Enable at boot:

    sudo systemctl enable nginx

Check failed services:

    systemctl list-units --failed

This is an excellent first command during an incident.


11. ps aux — Process investigation
    ------------------------------

         ps aux

Find top memory consumers:

        ps aux --sort=-%mem | head

Find top CPU consumers:

        ps aux --sort=-%cpu | head

Example:

      USER PID  %CPU %MEM COMMAND
      root 1234 95.0 20.0 java -jar app.jar

You immediately know:

    java
    PID = 1234
    CPU = 95%
    MEM = 20%

Now investigate that process.

Your Golden Troubleshooting Flow
======================================

Imagine somebody says:

"The application is very slow."

Don't immediately restart everything.

Use:


                 Application slow
                       |
                       v
                    uptime
                       |
                       v
                  CPU / Load?
                  /          \
                YES           NO
                |              |
              top           free -h
                |              |
            CPU high?       RAM pressure?
                |              |
               YES            YES
                |              |
           identify PID    check swap/OOM
                |
                v
              vmstat
                |
        -----------------
        |       |       |
       CPU      RAM     I/O
        |       |       |
       us      si/so    wa
                        |
                        v
                    iostat -x


Also:

    systemctl list-units --failed
    journalctl -p err -b
    dmesg -T
    df -h
    df -i
