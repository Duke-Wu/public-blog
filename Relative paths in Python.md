## [Relative paths in Python](https://stackoverflow.com/questions/918154/relative-paths-in-python)

### Question:

I'm building a simple helper script for work that will copy a couple of template files in our code base to the current directory. I don't, however, have the absolute path to the directory where the templates are stored. I do have a relative path from the script but when I call the script it treats that as a path relative to the current working directory. Is there a way to specify that this relative url is from the location of the script instead?



### Answers1:

In the file that has the script, you want to do something like this:

```py
import os
dirname = os.path.dirname(__file__)
filename = os.path.join(dirname, 'relative/path/to/file/you/want')
```

This will give you the absolute path to the file you're looking for. Note that if you're using setuptools, you should probably use its [package resources API](http://peak.telecommunity.com/DevCenter/PythonEggs#accessing-package-resources) instead.

**UPDATE**: I'm responding to a comment here so I can paste a code sample. :-)

> Am I correct in thinking that `__file__` is not always available (e.g. when you run the file directly rather than importing it)?

I'm assuming you mean the `__main__` script when you mention running the file directly. If so, that doesn't appear to be the case on my system (python 2.5.1 on OS X 10.5.7):

```py
#foo.py
import os
print os.getcwd()
print __file__

#in the interactive interpreter
>>> import foo
/Users/jason
foo.py

#and finally, at the shell:
~ % python foo.py
/Users/jason
foo.py
```

However, I do know that there are some quirks with `__file__` on C extensions. For example, I can do this on my Mac:

```py
>>> import collections #note that collections is a C extension in Python 2.5
>>> collections.__file__
'/System/Library/Frameworks/Python.framework/Versions/2.5/lib/python2.5/lib-
dynload/collections.so'
```

However, this raises an exception on my Windows machine.



### Nice Comments:

Note you should use os.path.join everywhere for portability: `filename = os.path.join(dir, 'relative', 'path', 'to', 'file', 'you' , 'want')`.

`os.path.dirname(__file__)` can give an empty string, use `os.path.dirname(os.path.abspath(__file__))` instead.



### Answers2:

As mentioned in the accepted answer 

```py
import os
dir = os.path.dirname(__file__)
filename = os.path.join(dir, '/relative/path/to/file/you/want')
```

I just want to add that

> the latter string can't begin with the backslash , infact no string should include a backslash

It should be something like

```py
import os
dir = os.path.dirname(__file__)
filename = os.path.join(dir, 'relative','path','to','file','you','want')
```

The accepted answer can be misleading in some cases , please refer to [this](https://stackoverflow.com/questions/1945920/why-doesnt-os-path-join-work-in-this-case) link for details



## [How to get an absolute file path in Python](https://stackoverflow.com/questions/51520/how-to-get-an-absolute-file-path-in-python)

### Question:

Given a path such as `"mydir/myfile.txt"`, how do I find the file's absolute path relative to the current working directory in Python? E.g. on Windows, I might end up with:

```py
"C:/example/cwd/mydir/myfile.txt"
```



### Answer01

```py
>>> import os
>>> os.path.abspath("mydir/myfile.txt")
'C:/example/cwd/mydir/myfile.txt'
```

Also works if it is already an absolute path:

```py
>>> import os
>>> os.path.abspath("C:/example/cwd/mydir/myfile.txt")
'C:/example/cwd/mydir/myfile.txt'
```



### Answer02

You could use the new Python 3.4 library `pathlib`. (You can also get it for Python 2.6 or 2.7 using `pip install pathlib`.) The authors [wrote](http://www.python.org/dev/peps/pep-0428/#abstract): "The aim of this library is to provide a simple hierarchy of classes to handle filesystem paths and the common operations users do over them."

To get an absolute path in Windows:

```py
>>> from pathlib import Path
>>> p = Path("pythonw.exe").resolve()
>>> p
WindowsPath('C:/Python27/pythonw.exe')
>>> str(p)
'C:\\Python27\\pythonw.exe'
```

Or on UNIX:

```py
>>> from pathlib import Path
>>> p = Path("python3.4").resolve()
>>> p
PosixPath('/opt/python3/bin/python3.4')
>>> str(p)
'/opt/python3/bin/python3.4'
```

Docs are here: <https://docs.python.org/3/library/pathlib.html>