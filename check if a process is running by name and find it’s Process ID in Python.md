# check if a process is running by name and find it’s Process ID in Python

https://thispointer.com/python-check-if-a-process-is-running-by-name-and-find-its-process-id-pid/

In this article we will discuss a cross platform way to find a running process PIDs by name using psutil.

To install python’s psutil library use,

```
pip install psutil
```

## Check if a process is running

To check if process is running or not, let’s iterate over all the running process using [psutil.process_iter()](https://thispointer.com/python-get-list-of-all-running-processes-and-sort-by-highest-memory-usage/) and match the process name i.e.

```python
import psutil
 
def checkIfProcessRunning(processName):
    '''
    Check if there is any running process that contains the given name processName.
    '''
    #Iterate over the all the running process
    for proc in psutil.process_iter():
        try:
            # Check if process name contains the given name string.
            if processName.lower() in proc.name().lower():
                return True
        except (psutil.NoSuchProcess, psutil.AccessDenied, psutil.ZombieProcess):
            pass
    return False;
```

Let’s use this function to check if any process with ‘chrome’ substring in name is running or not i.e.

```python
# Check if any chrome process was running or not.
if checkIfProcessRunning('chrome'):
    print('Yes a chrome process was running')
else:
    print('No chrome process was running')
```

Output:

```
Yes a chrome process was running
```

As in my system many chrome instances are running. So it will return True. But how to get the process ID of all the running ‘chrome.exe’ process ?

## Find PID (Process ID) of a running process by Name

As there may be many running instances of a given process. So, we will iterate over all the running process and for each process whose name contains the given string, we will keep it’s info in a list i.e.

```python
import psutil

def findProcessIdByName(processName):
    """
    Get a list of all the PIDs of a all the running process whose name contains
    the given string processName
    """
    listOfProcessObjects = []
    #Iterate over the all the running process
    for proc in psutil.process_iter():
        try:
            pinfo = proc.as_dict(attrs=['pid', 'name', 'create_time'])
            # Check if process name contains the given name string.
            if processName.lower() in pinfo['name'].lower() :
                listOfProcessObjects.append(pinfo)
        except (psutil.NoSuchProcess, psutil.AccessDenied , psutil.ZombieProcess) :
            pass
    return listOfProcessObjects
```

Let’s call this function to get the PIDs of all the running chrome.exe process

```python
# Find PIDs od all the running instances of process that contains 'chrome' in it's name
listOfProcessIds = findProcessIdByName('chrome')
 
if len(listOfProcessIds) > 0:
   print('Process Exists | PID and other details are')
   for elem in listOfProcessIds:
       processID = elem['pid']
       processName = elem['name']
       processCreationTime =  time.strftime('%Y-%m-%d %H:%M:%S', time.localtime(elem['create_time']))
       print((processID ,processName,processCreationTime ))
else :
   print('No Running Process found with given text')
```

Contents of the list will be,

```
(2604, 'chrome.exe', '2018-11-10 19:12:13')
(4276, 'chrome.exe', '2018-11-10 19:12:14')
(9136, 'chrome.exe', '2018-11-10 19:12:14')
(9616, 'chrome.exe', '2018-11-10 19:43:41')
(12904, 'chrome.exe', '2018-11-10 19:12:13')
(13476, 'chrome.exe', '2018-11-10 20:03:04')
(15520, 'chrome.exe', '2018-11-10 20:02:22')
```

We can do the same in a **single line using list comprehension i.e.**

```python
# Find PIDs od all the running instances of process that contains 'chrome' in it's name
procObjList = [procObj for procObj in psutil.process_iter() if 'chrome' in procObj.name().lower() ]
```

procObjList is a list of Process class objects. Let’s iterate over it and print them i.e.

```python
for elem in procObjList:
  print(elem)
```

Output will be,

```
psutil.Process(pid=2604, name='chrome.exe', started='19:12:13')
psutil.Process(pid=4276, name='chrome.exe', started='19:12:14')
psutil.Process(pid=9136, name='chrome.exe', started='19:12:14')
psutil.Process(pid=9616, name='chrome.exe', started='19:43:41')
psutil.Process(pid=12904, name='chrome.exe', started='19:12:13')
psutil.Process(pid=13476, name='chrome.exe', started='20:03:04')
psutil.Process(pid=15520, name='chrome.exe', started='20:02:22')
```