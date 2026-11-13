Linux du – Check Directory Sizes:
---------------------------------

First, your command has a small mistake. The correct command is:

    du -sh * | sort -rh | head -10


Let's understand it step by step.

What is du?

    du stands for Disk Usage.

It tells us how much disk space files and directories are consuming.

Example:

    du -sh /var/log

Possible output:

    250M    /var/log

Meaning: /var/log is consuming 250 MB of disk space.

<img width="632" height="458" alt="image" src="https://github.com/user-attachments/assets/044c328d-3a35-47d6-b77f-f4d8263faff9" />

<img width="997" height="772" alt="image" src="https://github.com/user-attachments/assets/e0a9f4be-119b-42c5-b363-9fb838711d58" />

<img width="1012" height="427" alt="image" src="https://github.com/user-attachments/assets/4c526ad1-6458-4a5a-97d7-0d2fc6c98ef4" />

5. Important commands for AWS DevOps

# Check current directory sizes
du -sh *

# Check /var directory size
du -sh /var

# Find biggest 10 directories under /var
du -sh /var/* | sort -rh | head -10

# Check filesystem disk usage
df -h

# Check directory sizes including hidden entries
du -sh .[^.]* * 2>/dev/null | sort -rh | head -10
