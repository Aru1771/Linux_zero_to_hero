To check the high disk utiligation folders:
--------------------------------------------

        du -xhd1 / | sort -h

        -x = stay in same file system
        -h = human redable
        - d 1 = directry and subdirectry level 
        / = check under root folder level
