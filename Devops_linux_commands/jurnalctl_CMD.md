What is journalctl?

* journalctl is a Linux command used to view and investigate logs collected by the systemd Journal.

In simple words:

* When you start, stop or restart a Linux service, or when a service encounters an error, systemd can record those events in its journal.

* We use journalctl to read those logs.


Understand your command

Your command:

    journalctl -u <service-name> -f -p err -b

<img width="847" height="356" alt="image" src="https://github.com/user-attachments/assets/41f11040-3424-4ff8-9701-d8a23bbae763" />

For example:

    sudo journalctl -u jenkins -f -p err -b

* This command displays error-level and more severe journal entries for the Jenkins service from the current boot and continuously follows new entries.

* Important correction: -p err does not display every log. It filters out lower-priority messages such as warnings, notices and informational logs.

<img width="686" height="575" alt="image" src="https://github.com/user-attachments/assets/6f263faa-2b33-434a-b4f0-705bc48214a2" />


<img width="957" height="522" alt="image" src="https://github.com/user-attachments/assets/fc31c5e2-7ed7-4366-87f7-7b4415369be9" />

EC2 instance restarted unexpectedly

    sudo journalctl -b -1 -p err


This helps identify error-level messages from the previous boot.
