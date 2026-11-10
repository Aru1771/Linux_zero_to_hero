Linux ps aux – Complete DevOps Training:

The ps aux command displays currently running processes on a Linux system, including processes owned by all users, with detailed resource and process information.

As an AWS DevOps Engineer, you will use this command to troubleshoot:

      - High CPU utilization.
      - High memory utilization.
      - Application slowness.
      - Zombie processes.
      - Background services.
      - Resource-consuming processes on EC2 instances.


1. Understand the command

Your command:

    ps aux --sort=-%mem
    
To sort by CPU instead:

    ps aux --sort=-%cpu

To see both CPU and memory rankings, execute them separately.

Important:

Your original text ps aux--sort=-%mem -%cpu is not the correct syntax for sorting by both. Use separate commands or a combined custom format.


<img width="697" height="192" alt="image" src="https://github.com/user-attachments/assets/e064f26a-b499-4224-8155-279d8be4421d" />

Together, ps aux displays a broad snapshot of running processes.

<img width="1000" height="555" alt="image" src="https://github.com/user-attachments/assets/68f2449c-3d2e-4e67-9a13-50167a7bc8f5" />

<img width="625" height="607" alt="image" src="https://github.com/user-attachments/assets/9eea586f-9f6e-4e32-9f9c-a4e15e12f744" />

Important difference: VSZ vs RSS
- VSZ: Total virtual address space allocated to the process.
- RSS: Physical memory currently resident in RAM.

For troubleshooting actual RAM consumption, RSS is particularly useful.

<img width="1028" height="537" alt="image" src="https://github.com/user-attachments/assets/d6e24f29-4ed1-4398-97ec-7633620a7d0b" />

<img width="972" height="537" alt="image" src="https://github.com/user-attachments/assets/38dcc60e-a274-4f53-9fe5-cd233b4b2e11" />

<img width="976" height="607" alt="image" src="https://github.com/user-attachments/assets/ed62e951-45b6-427b-b7b2-60bd3f5378f6" />

<img width="990" height="432" alt="image" src="https://github.com/user-attachments/assets/79686f2d-9611-4b17-8b3e-ed8ba1024c7b" />

<img width="842" height="517" alt="image" src="https://github.com/user-attachments/assets/3cf61f3e-755f-448f-b01d-0a804699e5f8" />

<img width="998" height="826" alt="image" src="https://github.com/user-attachments/assets/e84f3b7f-037c-4b60-a274-19ae454b284f" />


Useful production commands
-------------------------------
These are commands worth remembering.

1. Top 10 memory-consuming processes

        ps aux --sort=-%mem | head -10


2. Top 10 CPU-consuming processes

        ps aux --sort=-%cpu | head -10


3. Display processes with their parent PID

        ps -ef


4. Find a particular application

        ps aux | grep nginx


5. Display a particular process

        ps -p 1200 -o pid,ppid,user,%cpu,%mem,stat,cmd


6. Find zombie processes

        ps aux | awk '$8 ~ /Z/'


7. Display processes sorted by CPU and then memory


        ps aux --sort=-%cpu,-%mem | head -10


8. Monitor processes continuously

        top


Unlike ps, which provides a snapshot, top refreshes process information continuously.
