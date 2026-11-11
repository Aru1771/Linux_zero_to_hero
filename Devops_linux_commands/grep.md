grep:
====


grep is used to search for specific words or patterns inside files and directories.

1. Basic grep

Suppose you have a log file named application.log.

        grep "ERROR" application.log

This displays all lines containing ERROR.

Example log:
    
    INFO Application started
    ERROR Database connection failed
    INFO Retrying connection
    ERROR Connection timeout
    INFO Application running  

Output:

    ERROR Database connection failed
    ERROR Connection timeout

<img width="826" height="448" alt="image" src="https://github.com/user-attachments/assets/8f108311-900a-4df9-9871-17c12cc10bce" />

-r – Recursive search:

    grep -r "ERROR" /var/log/
Searches for ERROR inside files under /var/log/ and its subdirectories.

* This is what you will commonly use in production.

      grep -i -n -A 3 -B 1 "error" application.log


Meaning:
- -i → Ignore case.
- -n → Show line numbers.
- -A 3 → Show 3 lines after.
- -B 1 → Show 1 line before.
- error → Search pattern.

# Search multiple words using OR (|):

Suppose you want to search for ERROR or WARNING.

    grep -E "ERROR|WARNING" application.log

Output:

    ERROR Database connection failed
    WARNING High memory usage
    ERROR Connection timeout


-E enables extended regular expressions, so | means OR.
