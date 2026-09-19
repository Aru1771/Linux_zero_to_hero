To check the high disk utiligation folders:
--------------------------------------------

        du -xhd1 / | sort -h

        -x = stay in same file system
        -h = human redable
        - d 1 = directry and subdirectry level 
        / = check under root folder level
Find files larger than 500 MB
------------------------------

      sudo find / -xdev -type f -size +500M -exec ls -lh {} \;

       / = Start searching from the root directory /, meaning the whole filesystem.
       xdev = serach under this file system.
       -type f = regular file type
       -size +500M = Find files whose size is greater than 500 MB.
       -exec ls -lh {} \; = This tells find
       ls -lh shows the file details in human-readable format.

        {} = the file found by find.
        
        \; = tells find that the -exec command has ended.
               
