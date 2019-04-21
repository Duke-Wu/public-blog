## [Why would one use both, os.path.abspath and os.path.realpath?](https://stackoverflow.com/questions/37863476/why-would-one-use-both-os-path-abspath-and-os-path-realpath)

### The Question:

In multiple open source projects, I have seen people do `os.path.abspath(os.path.realpath(__file__))` to get the absolute path to the current file.

However, I find that `os.path.abspath(__file__)` and `os.path.realpath(__file__)` produce the same result. `os.path.abspath(os.path.realpath(__file__))` seems to be a bit redundant.

Is there a reason people are using that?



### The Answers

For your stated scenario,  **there is no reason to combine realpath and abspath,**  since [`os.path.realpath`](https://hg.python.org/cpython/file/3.6/Lib/posixpath.py#l382) actually calls `os.path.abspath` before returning a result (I checked Python 2.5 to Python 3.6).

- `os.path.abspath` returns the absolute path, but does NOT resolve symlinks.
- `os.path.realpath` will first resolve any symbolic links in the path, and then return the absolute path.

However, if you expect your path to contain a `~`, **neither abspath or realpath will resolve ~ to the user's home directory, and the resulting path will be invalid**. You will need to use [`os.path.expanduser`](https://docs.python.org/3.6/library/os.path.html#os.path.expanduser) to resolve this to the user's directory.

For the sake of a thorough explanation, here are some results which I've verified in Windows and Linux, in Python 3.4 and Python 2.6. The current directory (`./`) is my home directory, which looks like this:

```bash
myhome
|- data (symlink to /mnt/data)
|- subdir (extra directory, for verbose explanation)
```

```bash
# os.path.abspath returns the absolute path, but does NOT resolve symlinks
os.path.abspath('./')
'/home/myhome'
os.path.abspath('./subdir/../data')
'/home/myhome/data'


# os.path.realpath will resolve symlinks AND return an absolute path from a relative path
os.path.realpath('./')
'/home/myhome'
os.path.realpath('./subdir/../')
'/home/myhome'
os.path.realpath('./subdir/../data')
'/mnt/data'

# NEITHER abspath or realpath will resolve or remove ~.
os.path.abspath('~/data')
'/home/myhome/~/data'

os.path.realpath('~/data')
'/home/myhome/~/data'

# And the returned path will be invalid
os.path.exists(os.path.abspath('~/data'))
False
os.path.exists(os.path.realpath('~/data'))
False

# Use realpath + expanduser to resolve ~
os.path.realpath(os.path.expanduser('~/subdir/../data'))
'/mnt/data'
```


### Nice Comment

Then also do not forget about os.path.expandvars if variables are used in a path

```bash
expandvars(path)
    Expand shell variables of form $var and ${var}.  Unknown variables
    are left unchanged.
```

**For Example:**
in bash:

```bash
$ echo $HOME
/Users/g
```

in python shell

```bash
import os
os.path.expandvars('${HOME}/Desktop/path/b/')
'/Users/g/Desktop/path/b/'
```


