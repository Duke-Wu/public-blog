## svn 如何恢复已删除的文件及提交记录

#### 一、恢复已经删除的文件
If you just did
```
svn rm foo.txt
```
then you can undo that with simply
```
svn revert foo.txt
```

If you already checked in after doing the "`svn rm`" then you can look at the log (`svn log`), find the last revision where the file existed, and grab it from that version.
One way to do that is to merge in the old revision that has the file. Assuming the current revision is 123 and the last version with that file is 120, then do this:
```
svn merge -r123:120
```
Maybe first do a dry run to make sure it won't do anything you don't want:
```
svn --dry-run merge -r123:120
```
------

For the sub-question, how to remove a file from svn without removing the local copy:
```
svn rm foo.txt --keep-local
```
Or, of course, you could just copy to a temp file before svn rm'ing and then copy back:
```
cp foo.txt foo.txt-tmp
svn rm foo.txt
(svn ci -m "just removed foo.txt from the repository")
cp foo.txt-tmp foo.txt
```



#### 二、恢复已经删除的文件及提交记录

To remove a file but keep it in the working copy, use
```
svn rm foo.txt --keep-local
```
To get a file back after you've committed the remove, you can use either the merge command, or the copy command - both will preserve the file history. Since the merge way is already described elsewhere, here's the copy way:
```
svn rm foo.txt
svn ci foo.txt -m "removed file"
(now at r5 as an example)
svn cp url/to/removed/foo.txt -r4 foo.txt
svn ci foo.txt -m "got file back"
```



#### 三、使用TortoiseSVN恢复已经删除的文件及提交记录

For TortoiseSVN users, this is very easily accomplished via the Repo Browser. Within the Repo Browser switch to the revision before the file was deleted (you can change the version using the "Revision:" button at the top right):

 ![80fXm](http://img-note.wuqianlin.cn/img-md/2019-06-01-085959.png)

You should then be able to see the deleted file in its original location in the Repo Browser. Right-click on it and choose "Copy to working copy...":

 ![](http://img-note.wuqianlin.cn/img-md/2019-06-01-qMqh6.png)

The file will then be restored to your working copy ready to commit (as though svn added) and with its log history intact.



#### 四、报错处理
1. xcode svn commit: remains in tree-conflict 错误的解决办法
   这是因为在服务器端把文件delete掉了，然后再update了一次，结果就造成了冲突。解决方案：

    filename  为出错文件的绝对路径
    svn remove --force filename
    svn resolve --accept=working  filename
    svn commit

