## 文件夹进行MD5校验的实现算法

每份相同数据（文件夹）都可以生成一份唯一的md5校验文件，我们可以通过直接校验整个数据文件夹的方法来确定数据是否有误。

#### 1. 针对整个文件夹生成 md5 校验文件方法：

以data文件夹为例，我们需要得到data整个文件夹中的文件的md5校验值，我们通过shell程序对整个data文件夹中的文件进行md5校验。

通过如下命令获得整个data文件夹的md5校验文件：

```bash
cd  data
find  ./  -type  f  -print0  |  xargs  md5sum  >  data_temp.md5
cat data_temp.md5 |  sort > data.md5
```

(找出data文件中的每个文件并获得该文件的md5值存入data_temp.md5中 ，然后对md5值的文件进行排序获得data.md5校验文件)

data.md5文件中数据格式如下，前面的字符串是该文件的md5值，后面的是该文件相对data文件夹的路径。由于每个不同文件的md5值都唯一，每个文件的路径都不同，所以排序之后，同样的文件夹生成的该md5文件都是唯一的

040173e4c2c29787cd0e644bd3737f8f  ./preinstall/Babaqunaer_1.3.6.apk

0054b51601427b2630793d43ed0b224c  ./pcvoice/7.mp3



#### 2. 生成 md5 校验文件程序：

```bash
#!/bin/bash
# author: huinatianyun
# Updated Date:2014-03-24
# Set environment variables

LANG=""
export LANG
#这里必须要设置语言环境，因为sort排序是对文件的每一行作为一个单位，相互比较，比较原则是从首字符向后依次进行比较，语言环境不同，字符的编码可能就不一样，所以会导致文件文件排序结果不一致,LANG=""表示默认设置为en_US 
echo ""
echo ""
echo "please put generate_md5_value.sh, custdata.tar, hpdata.tar, hwapudata.tar in the same directory,"
echo ""

read -p "are you put generate_md5_value.sh in the right position ? (Y/N): " select_yn

echo ""

echo ""

if [ "$select_yn" == "Y" ] || [ "$select_yn" == "y" ]; then

echo "start generate data.md5 ..."

cd ./data

find ./ -type f -print0 | xargs -0 md5sum | sort > ../data.md5 

#xargs 命令-0是表示以空字符作为分隔符，因为有些目录的名字是含有空格的，而xargs是默认是以空格为分割符的，它会误认为还有空格的目录的路径为两个路径，换成空字符就是消除这个问题 

echo "generate md5 value over"

elif [ "$select_yn" == "N" ] || [ "$select_yn" == "n" ]; then 

echo "stop generate md5 value !"

else

echo "I don't know what your choice is !!!"

fi 
```



#### 3. 数据校验方法：

通过上述方法对data文件夹里数据生成一份md5校验文件，这个md5文件就这可作为这个data文件夹的校验文件，校验一个文件夹数据是否和data相同的方法就是针对该文件夹通过同样的方法生成一份md5校验文件，然后比对校验文件是否相同，如果两个文件相同，这认为这两个比对的数据文件夹一致



