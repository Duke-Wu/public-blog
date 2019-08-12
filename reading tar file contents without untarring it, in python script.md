## [reading tar file contents without untarring it, in python script](https://stackoverflow.com/questions/2018512/reading-tar-file-contents-without-untarring-it-in-python-script)

> **or** [python tar file how to extract file into stream](https://stackoverflow.com/questions/13562037/python-tar-file-how-to-extract-file-into-stream)

**Question:**

I have a tar file which has number of files within it. I need to write a python script which will read the contents of the files and gives the count o total characters, including total number of letters, spaces, newline characters, everything, without untarring the tar file.

**Answer:**

you can use getmembers()

```py
>>> import  tarfile
>>> tar = tarfile.open("test.tar")
>>> tar.getmembers()
```

After that, you can use extractfile() to extract the members as file object. Just an example

```py
import tarfile,os
import sys
os.chdir("/tmp/foo")
tar = tarfile.open("test.tar")
for member in tar.getmembers():
    f=tar.extractfile(member)
    content=f.read()
    print "%s has %d newlines" %(member, content.count("\n"))
    print "%s has %d spaces" % (member,content.count(" "))
    print "%s has %d characters" % (member, len(content))
    sys.exit()
tar.close()
```

With the file object "f" in the above example, you can use read(), readlines() etc.



**Others:**
> [python tar file how to extract file into stream](https://stackoverflow.com/questions/13562037/python-tar-file-how-to-extract-file-into-stream)

**Question:**

I am trying to extract a zipped folder but instead of directly using `.extractall()`, I want to extract the file into stream so that I can handle the stream myself. Is it possible to do it using `tarfile`? Or is there any suggestions?

**Answer:**

You can obtain each file from a tar file as a python `file` object using the `.extractfile()` method. Loop over the `tarfile.TarFile()` instance to list all entries:

```py
import tarfile

with tarfile.open(path) as tf:
    for entry in tf:  # list each entry one by one
        fileobj = tf.extractfile(entry)
        # fileobj is now an open file object. Use `.read()` to get the data.
        # alternatively, loop over `fileobj` to read it line by line.
```


