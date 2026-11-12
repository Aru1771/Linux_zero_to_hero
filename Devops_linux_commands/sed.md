sed is a Linux command used to find, replace, modify, and delete text inside files without opening them manually.

Think of sed as a text editor that works directly from the terminal.

1. Basic syntax

        sed 'command' filename

Replace text – s/old/new/

    sed 's/dev/prod/' app.conf

Output:

    environment=prod
    server=old-server
    database=dev-db
    environment=prod


s = substitute (replace).
dev = old text.
prod = new text.
app.conf = file name.

# Here, g means global.

It replaces every occurrence of dev in each line.

# In-place replacement – -i

Without -i: Displays modified output only. Original file remains unchanged.
With -i: Modifies the original file permanently.

# Delete lines – /pattern/d

Command:
sed '/debug/d' app.conf

Explanation:
- /debug/ = Find lines containing debug.
- d = Delete those lines from the output.

<img width="820" height="430" alt="image" src="https://github.com/user-attachments/assets/4b9363cb-8185-49b3-a536-debbdc286b14" />

