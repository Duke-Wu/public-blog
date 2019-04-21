## [How to get file creation & modification date/times in Python?](https://stackoverflow.com/questions/237079/how-to-get-file-creation-modification-date-times-in-python)

### The Question:
I have a script that needs to do some stuff based on file creation & modification dates but has to run on Linux & Windows.

What's the best cross-platform way to get file creation & modification date/times in Python?

### The Answer01

Getting some sort of modification date in a cross-platform way is easy - just call [`os.path.getmtime(*path*)`](https://docs.python.org/library/os.path.html#os.path.getmtime) and you'll get the Unix timestamp of when the file at `path` was last modified.

Getting file *creation* dates, on the other hand, is fiddly and platform-dependent, differing even between the three big OSes:

- On **Windows**, a file's `ctime` (documented at <https://msdn.microsoft.com/en-us/library/14h5k7ff.aspx>) stores its creation date. You can access this in Python through [`os.path.getctime()`](https://docs.python.org/library/os.path.html#os.path.getctime) or the [`.st_ctime`](https://docs.python.org/3/library/os.html#os.stat_result.st_ctime) attribute of the result of a call to [`os.stat()`](https://docs.python.org/3/library/os.html#os.stat). This *won't*work on Unix, where the `ctime` [is the last time that the file's attributes *or* content were changed](http://www.linux-faqs.info/general/difference-between-mtime-ctime-and-atime).

- On **Mac**, as well as some other Unix-based OSes, you can use the [`.st_birthtime`](https://docs.python.org/3/library/os.html#os.stat_result.st_birthtime) attribute of the result of a call to `os.stat()`.

- On **Linux**, this is currently impossible, at least without writing a C extension for Python. Although some file systems commonly used with Linux [do store creation dates](https://unix.stackexchange.com/questions/7562/what-file-systems-on-linux-store-the-creation-time) (for example, `ext4` stores them in `st_crtime`) , the Linux kernel [offers no way of accessing them](https://unix.stackexchange.com/questions/91197/how-to-find-creation-date-of-file); in particular, the structs it returns from `stat()` calls in C, as of the latest kernel version, [don't contain any creation date fields](https://github.com/torvalds/linux/blob/v4.8-rc6/include/linux/stat.h). You can also see that the identifier `st_crtime` doesn't currently feature anywhere in the [Python source](https://github.com/python/cpython/search?utf8=%E2%9C%93&q=st_crtime). At least if you're on `ext4`, the data *is* attached to the inodes in the file system, but there's no convenient way of accessing it.

  The next-best thing on Linux is to access the file's `mtime`, through either [`os.path.getmtime()`](https://docs.python.org/library/os.path.html#os.path.getmtime) or the [`.st_mtime`](https://docs.python.org/3/library/os.html#os.stat_result.st_mtime) attribute of an `os.stat()` result. This will give you the last time the file's content was modified, which may be adequate for some use cases.

Putting this all together, cross-platform code should look something like this...

```python
import os
import platform

def creation_date(path_to_file):
    """
    Try to get the date that a file was created, falling back to when it was
    last modified if that isn't possible.
    See http://stackoverflow.com/a/39501288/1709587 for explanation.
    """
    if platform.system() == 'Windows':
        return os.path.getctime(path_to_file)
    else:
        stat = os.stat(path_to_file)
        try:
            return stat.st_birthtime
        except AttributeError:
            # We're probably on Linux. No easy way to get creation dates here,
            # so we'll settle for when its content was last modified.
            return stat.st_mtime
```



### The Answer02

You have a couple of choices. For one, you can use the [`os.path.getmtime`](https://docs.python.org/library/os.path.html#os.path.getmtime) and [`os.path.getctime`](https://docs.python.org/library/os.path.html#os.path.getctime)functions:

```py
import os.path, time
print("last modified: %s" % time.ctime(os.path.getmtime(file)))
print("created: %s" % time.ctime(os.path.getctime(file)))
```

Your other option is to use [`os.stat`](https://docs.python.org/library/os.html#os.stat):

```py
import os, time
(mode, ino, dev, nlink, uid, gid, size, atime, mtime, ctime) = os.stat(file)
print("last modified: %s" % time.ctime(mtime))
```

**Note**: `ctime()` does *not* refer to creation time on *nix systems, but rather the last time the inode data changed. (thanks to kojiro for making that fact more clear in the comments by providing a link to an interesting blog post)



### The Answer03

The best function to use for this is [os.path.getmtime()](http://docs.python.org/library/os.path.html#os.path.getmtime). Internally, this just uses `os.stat(filename).st_mtime`.

The datetime module is the best manipulating timestamps, so you can get the modification date as a `datetime` object like this:

```py
import os
import datetime
def modification_date(filename):
    t = os.path.getmtime(filename)
    return datetime.datetime.fromtimestamp(t)
```

Usage example:

```py
>>> d = modification_date('/var/log/syslog')
>>> print d
2009-10-06 10:50:01
>>> print repr(d)
datetime.datetime(2009, 10, 6, 10, 50, 1)
```



[share](https://stackoverflow.com/a/1526089)[improve this answer](https://stackoverflow.com/posts/1526089/edit)

[edited Oct 6 '09 at 14:56](https://stackoverflow.com/posts/1526089/revisions)



answered Oct 6 '09 at 14:51

- 

  This answer is also a little bit wrong. `getmtime` is the nearest thing available on Unix (where getting creation dates isn't possible), but is definitely not the best function to use on Windows, where the `ctime` is a creation time. – [Mark Amery](https://stackoverflow.com/users/1709587/mark-amery) [Sep 12 '16 at 20:00](https://stackoverflow.com/questions/237079/how-to-get-file-creation-modification-date-times-in-python#comment66236949_1526089) 

- 2

  @MarkAmery - This answer is clearly labeled as just being about modification time. – [ArtOfWarfare](https://stackoverflow.com/users/901641/artofwarfare) [Jan 19 '17 at 18:30](https://stackoverflow.com/questions/237079/how-to-get-file-creation-modification-date-times-in-python#comment70690680_1526089)



## [How to delete files from a folder based on Date modified](https://stackoverflow.com/questions/46202256/how-to-delete-files-from-a-folder-based-on-date-modified)

I am trying to delete files from a folder based off of the date modified, inside the folder are files with Date modified as follows:

Name          Date modified

File 1      9/12/2017 1:34 PM
File 2      9/12/2017 1:38 PM
File 3      9/12/2017 12:00 PM
File 4      9/12/2017 12:00 PM
File 5      9/12/2017 7:40 AM
File 6      9/12/2017 7:40 AM
Lets say I just want to keep files in this folder that are only 30 minutes old and lets just say right now it is 1:48 PM so what I would expect to be kept after running the cleanup script which would delete the files that are older than 30 minutes would be:

Name          Date modified

File 1      9/12/2017 1:34 PM
File 2      9/12/2017 1:38 PM
Thanks in advance.


### The Answer

you can try something like this:

```py
import os
import time

now = time.time()

folder = '<folder_path>'

files = [os.path.join(folder, filename) for filename in os.listdir(folder)]
for filename in files:
    if (now - os.stat(filename).st_mtime) > 1800:
        command = "rm {0}".format(filename)
        subprocess.call(command, shell=True)
```

`time.time()` returns the actual time in seconds.

`os.stat(filename).st_mtime` returns the time of last modification in seconds.

1800 is 30 minutes in seconds.