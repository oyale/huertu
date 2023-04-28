---
{"created":"2022-12-15T19:44:45 (UTC +01:00)","tags":null,"url":"https://linuxhandbook.com/kill-process-port/","author":"Team LHB","dg-publish":true,"permalink":"/sys-admin/clips/kill-process-running-on-a-specific-port-in-linux/","dgPassFrontmatter":true}
---

From `= this.url`



> ## Excerpt
> Want to kill the processes running on specific ports? No need to know the process ID or name. You can terminate a process based on the port number it is using.

---
## Terminating processes based on their port numbers

The `fuser` command combined with the `-k` (kill) option will end all associated processes that are listening on a TCP or UDP port. Simply provide the port number and type (TCP or UDP) in the `fuser` command.

For instance, to end a process on UDP port 81, use the `fuser` command as:

```
sudo fuser -k 81/udp
```

[![Killing processes on a specific port](https://linuxhandbook.com/content/images/2022/12/Killing-process-using-fuser-command.png)](https://linuxhandbook.com/content/images/2022/12/Killing-process-using-fuser-command.png)

Similarly, use the `fuser` command to terminate a process on TCP port 3306:

```
sudo fuser -k 3306/tcp
```

You can use the lsof command to verify that processes are no longer running on the target port.

## The classic way of killing processes using certain ports

For those who would rather not use `fuser`, `lsof` may be used to determine which processes are using a certain port and then use this information with the kill command.

As an instance, to kill all the process running on TCP port 3306, use the below command to detect the process id:

```
sudo lsof -i TCP:3306
```

And now kill it with its pid:

```
 kill -9 <pid_value>
```

One thing to note here is that certain processes like `mysqld` and `apache2` might restart after you have killed them using the above commands. Even if you use the `killall` command, they will still appear after some time.

In such cases, I advise you to use application specific commands to stop a service. For example, to kill the Apache process on Ubuntu, use the command:

```
sudo systemctl stop apache2
```

[![Killing processes in standar procedure](https://linuxhandbook.com/content/images/2022/12/Killing-process-with-standard-commands.png)](https://linuxhandbook.com/content/images/2022/12/Killing-process-with-standard-commands.png)

This will completely kill the particular process.

## Wrapping Up

This article covered how to terminate a process running on a specific port. With the fuser command, you don't need to know the process details. Otherwise, the classic method of getting the process ID associated with port first and then killing it works as well.
