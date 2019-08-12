# Executing Shell Commands with Python

**This quick tutorial shows you how to execute shell commands in Python.**

Python is an excellent scripting language. More and more sysadmins are using Python scripts to automate their work.

Since the sysadmin tasks involve Linux commands all the time, running Linux commands from the Python script is a great help.

In this tutorial, I’ll show you a couple of ways you can run shell commands and get its output in your Python program.

![Execute shell commands in Python programs](http://img-note.wuqianlin.cn/img-md/2019-07-29-030830.png)

## Execute Shell command in Python with os module

Let me create a simple python program that executes a shell command with the [os module](https://docs.python.org/2/library/os.html).

```bash
import os
myCmd = 'ls -la'
os.system(myCmd)
```

Now, if I run this program, here’s what I see in the output.

```bash
python prog.py 
total 40
drwxr-xr-x  3 abhishek abhishek 4096 Jan 17 15:58 .
drwxr-xr-x 49 abhishek abhishek 4096 Jan 17 15:05 ..
-r--r--r--  1 abhishek abhishek  456 Dec 11 21:29 agatha.txt
-rw-r--r--  1 abhishek abhishek    0 Jan 17 12:11 count
-rw-r--r--  1 abhishek abhishek   14 Jan 10 16:12 count1.txt
-rw-r--r--  1 abhishek abhishek   14 Jan 10 16:12 count2.txt
--w-r--r--  1 abhishek abhishek  356 Jan 17 12:10 file1.txt
-rw-r--r--  1 abhishek abhishek  356 Dec 17 09:59 file2.txt
-rw-r--r--  1 abhishek abhishek   44 Jan 17 15:58 prog.py
-rw-r--r--  1 abhishek abhishek  356 Dec 11 21:35 sherlock.txt
drwxr-xr-x  3 abhishek abhishek 4096 Jan  4 20:10 target
```

That’s the content of the directory where prog.py is stored.

If you want to use the output of the shell command, you can store it in a file directly from the shell command:

```python
import os
myCmd = 'ls -la > out.txt'
os.system(myCmd)
```

You can also store the output of the shell command in a variable in this way:

```python
import os
myCmd = os.popen('ls -la').read()
print(myCmd)
```

If you run the above program, it will print the content of the variable myCmd and it will be the same as the output of the [ls command](https://linuxhandbook.com/ls-command/) we saw earlier.



[**READ**  3 Ways to Write a List to a File in Python](https://linuxhandbook.com/python-write-list-file/)

Now let’s see another way of running Linux command in Python.

## Execute shell command in Python with subprocess module

A slightly better way of running shell commands in Python is using the [subprocessmodule](https://docs.python.org/2/library/subprocess.html).

If you want to run a shell command without any options and arguments, you can call subprocess like this:

```python
import subprocess
subprocess.call("ls")
```

The call method will execute the shell command. You’ll see the content of the current working directory when you run the program:

```bash
python prog.py 
agatha.txt  count1.txt    file1.txt  prog.py   target
count        count2.txt  file2.txt  sherlock.txt
```

If you want to provide the options and the arguments along with the shell command, you’ll have to provide them in a list.

```python
import subprocess
subprocess.call(["ls", "-l", "."])
```

When you run the program, you’ll see the content of the current directory in the list format.

Now that you know how to run shell command with subprocess, the question arises about storing the output of the shell command.

For this, you’ll have to use the Popen function. It outputs to the Popen object which has a communicate() method that can be used to get the standard output and error as a [tuple](https://www.w3schools.com/python/python_tuples.asp). You can learn more about the subprocess module [here](https://www.pythonforbeginners.com/os/subprocess-for-system-administrators).

```python
import subprocess
MyOut = subprocess.Popen(['ls', '-l', '.'], 
            stdout=subprocess.PIPE, 
            stderr=subprocess.STDOUT)
stdout,stderr = MyOut.communicate()
print(stdout)
print(stderr)
```

When you run the program, you’ll see the stdout and stderr (which is none in this case).

```bash
python prog.py 
 total 32
 -r--r--r-- 1 abhishek abhishek  456 Dec 11 21:29 agatha.txt
 -rw-r--r-- 1 abhishek abhishek    0 Jan 17 12:11 count
 -rw-r--r-- 1 abhishek abhishek   14 Jan 10 16:12 count1.txt
 -rw-r--r-- 1 abhishek abhishek   14 Jan 10 16:12 count2.txt
 --w-r--r-- 1 abhishek abhishek  356 Jan 17 12:10 file1.txt
 -rw-r--r-- 1 abhishek abhishek  356 Dec 17 09:59 file2.txt
 -rw-r--r-- 1 abhishek abhishek  212 Jan 17 16:54 prog.py
 -rw-r--r-- 1 abhishek abhishek  356 Dec 11 21:35 sherlock.txt
 drwxr-xr-x 3 abhishek abhishek 4096 Jan  4 20:10 target

None
```

I hope this quick tip helped you to execute shell command in Python programs. In a related quick tip, you can learn to [write list to file in Python](https://linuxhandbook.com/python-write-list-file/).

[**READ**  3 Ways to Write a List to a File in Python](https://linuxhandbook.com/python-write-list-file/)

If you have questions or suggestions, please feel free to drop a comment below.


原文地址：https://linuxhandbook.com/execute-shell-command-python/