* ss is a Linux command used to check network connections and listening ports.
* It helps DevOps engineers identify which ports are open and which processes are using them.

Understand the command

    ss -tulnp

<img width="545" height="412" alt="image" src="https://github.com/user-attachments/assets/1f873a83-cf7c-4fba-9a44-bab2956796dd" />

Real-world example

Run:
  
    sudo ss -tulnp

Example output:

    Netid  State   Local Address:Port   Process
    tcp    LISTEN  0.0.0.0:80            nginx
    tcp    LISTEN  127.0.0.1:3306        mysqld
    tcp    LISTEN  0.0.0.0:22            sshd
    udp    UNCONN  0.0.0.0:53            systemd-resolve

<img width="977" height="447" alt="image" src="https://github.com/user-attachments/assets/0445862c-def0-4071-8a01-d3e4e75025ab" />

<img width="1000" height="662" alt="image" src="https://github.com/user-attachments/assets/81e78d98-470f-42a9-bfeb-4ba76b99ac3b" />

Important commands to remember:
-------------------------------


    # Show all listening TCP ports
    sudo ss -tlpn
    
    # Show all listening UDP ports
    sudo ss -ulpn
    
    # Check a specific port
    sudo ss -tulnp | grep :8080
    
    # Show all TCP connections
    ss -tn


