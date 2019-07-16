Bash面试题


阿里Linux Shell脚本面试25个经典问答
https://www.cnblogs.com/andy-zhou/p/5321839.html

分享70个经典的 Shell 脚本面试题与答案
https://www.jb51.net/article/135168.htm

shell-脚本-字符串切片
https://blog.csdn.net/itlinuxp/article/details/79060822

分享70个经典的 Shell 脚本面试题与答案
https://www.jb51.net/article/135168.htm
(原文) https://linoxide.com/linux-shell-script/shell-scripting-interview-questions-answers/




19) 命令：[ -z "" ] && echo 0 || echo 1 的输出是什么
0 


34) 如何使用 awk 列出 UID 小于 100 的用户 ?
awk -F: '$3<100' /etc/passwd


35) 写程序为用户计算主组数目并显示次数和组名
cat /etc/passwd|cut -d: -f4|sort|uniq -c|while read c g
do
{ echo $c; grep :$g: /etc/group|cut -d: -f1;}|xargs -n 2
done


41) 哪个命令将命令替换为大写 ?
tr '[:lower:]' '[:upper:]'


47) 如何去除字符串中的所有空格 ?
echo $string | tr -d " "


$item 与 ${item}的区别


49) 写出输出数字 0 到 100 中 3 的倍数(0 3 6 9 …)的命令 ?
for i in {0..100..3}; do echo $i; done


51) [ $a == $b ] 和 [ $a -eq $b ] 有什么区别
[ $a == $b ] - 用于字符串比较
[ $a -eq $b ] - 用于数字比较


56) [[ $string == abc* ]] 和 [[ $string == "abc*" ]] 有什么区别
[[ $string == abc* ]] - 检查字符串是否以字母 abc 开头
[[ $string == "abc" ]] - 检查字符串是否完全等于 abc


57) 如何列出以 ab 或 xy 开头的用户名 ?
egrep "^ab|^xy" /etc/passwd | cut -d: -f1


58) bash 中 $! 表示什么意思 ?
后台最近执行命令的 PID.


60) 如何输出当前 shell 的 PID ?
echo $$


*62) $* 和 $@ 有什么区别*
$* - 以一个字符串形式输出所有传递到脚本的参数
$@ - 以 $IFS 为分隔符列出所有传递到脚本中的参数


70) 在脚本中如何使用 "expect" ?

/usr/bin/expect << EOD
spawn rsync -ar ${line} ${desthost}:${destpath}
expect "*?assword:*"
send "${password}\r"
expect eof
EOD
