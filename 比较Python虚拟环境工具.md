---
title: 比较Python虚拟环境工具
date: 2019/3/28 18:13:25
---

# 比较Python虚拟环境工具

### Various tools for Python Virtual Environments
#### 1. virtualenv

[Python Virtual Environments made easy](<https://towardsdatascience.com/python-virtual-environments-made-easy-fe0c603fe601>)

<https://pypi.org/project/virtualenv/>

<https://virtualenv.pypa.io/en/latest/userguide/>

<https://virtualenvwrapper.readthedocs.io/en/latest/>

<https://virtualenvwrapper.readthedocs.io/en/latest/install.html>

#### 2. Python Environment Wrapper (pew)

#### 3. venv

<https://docs.python.org/3/library/venv.html>

#### 4. pipenv

<https://docs.python-guide.org/dev/virtualenvs/>

<https://realpython.com/python-virtual-environments-a-primer/>

<https://www.pythonforthelab.com/blog/virtual-environment-is-a-must-have-tool/>

https://towardsdatascience.com/comparing-python-virtual-environment-tools-9a6543643a44

<https://github.com/pypa/pipenv>

<https://pipenv.readthedocs.io/en/latest/>

#### 5. [How to rename a virtualenv in Python?](https://stackoverflow.com/questions/43256369/how-to-rename-a-virtualenv-in-python)

By default virtualenv does not support the renaming of environments. It is safer to just delete the virtualenv directory and create a new one with the correct name. You can do this by:

1. Activate your virtualenv: `source vnev/bin/activate`
2. Create a requirements.txt of currently installed packages: `pip freeze > requirements.txt`
3. Delete the misspelled virtualenv: `rm -r vnev/`
4. Create a new virtualenv with correct name: `virtualenv venv`
5. Activate new virtualenv: `source venv/bin/activate`
6. Install packages from requirements.txt: `pip install -r requirements.txt`

If recreating is not an option there are 3rd party tools like [virtualenv-mv](https://github.com/brbsix/virtualenv-mv) that might be helpful.

Alternatively you can use [virtualenvwrapper](http://virtualenvwrapper.readthedocs.io/en/latest/command_ref.html?highlight=cpvirtualenv#cpvirtualenv) which provides the `cpvirtualenv` command to copy or rename virtualenvs.

### 6.[How do I properly setup pipenv in PyCharm?](https://stackoverflow.com/questions/46251411/how-do-i-properly-setup-pipenv-in-pycharm)

<https://stackoverflow.com/questions/46251411/how-do-i-properly-setup-pipenv-in-pycharm>



### 参考文档

<https://towardsdatascience.com/comparing-python-virtual-environment-tools-9a6543643a44>



