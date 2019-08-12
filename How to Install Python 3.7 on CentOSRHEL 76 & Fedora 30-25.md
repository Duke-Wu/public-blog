# How to Install Python 3.7 on CentOS/RHEL 7/6 & Fedora 30-25

Python is a powerful programming language. It is very friendly and easy to learn. At writing time of this article Python 3.7.3 latest stable version is available to download and install. This tutorial will help you to install Python 3.7.3 on your CentOS, Red Hat & Fedora operating systems.

## Step 1 – Requirements

This Python installation required GCC compiler on your system. Login to your server using ssh or shell access. Now, use the following command to install prerequisites for Python before installing it.

```
yum install gcc openssl-devel bzip2-devel libffi-devel
```

- Read: [How to Use SSH to Connect Remote Linux Server](https://tecadmin.net/use-ssh-connect-remote-linux-server/)

## Step 2 – Download Python 3.7

Download Python using the following command from the Python official site. You can also download the latest version in place of specified below.

```
cd /usr/src
wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz
```

Now extract the downloaded package.

```
tar xzf Python-3.7.3.tgz
```

## Step 3 – Install Python 3.7

Use below set of commands to compile Python source code on your system using altinstall.

```
cd Python-3.7.3
./configure --enable-optimizations
make altinstall
```

**make altinstall** is used to prevent replacing the default python binary file **/usr/bin/python**.

Now remove downloaded source archive file from your system

```
rm /usr/src/Python-3.7.3.tgz
```

## Step 4 – Check Python Version

Check the latest version installed of python. Use command python3.7 instead of just python.

```
python3.7 -V

Python 3.7.3
```