Django国际化文件使用方法
1.安装gettext，windows和linux版本不同，
​    这个是windows的地址，linux自行搜索
​    https://mlocati.github.io/articles/gettext-iconv-windows.html

2.使用的时候需要用django.utils.translation.gettext_lazy方法来包裹字符串

    gettext_lazy('api id')
    通常情况下会使用别名'_', import的时候使用as关键字
    _('api id')

2.运行django manage命令makemessages，会自动搜索settings里面LOCALE_PATHS中的路径，以及django默认回去搜索的路径（这个不用管它）

    makemessages -l zh_Hans -e py -i venv

3.修改locale目录下的django.po文件
​    msgid "put"
​    msgstr "翻译后的值"
​    

4.运行django manage命令compilemessages
​    compilemessages -l zh_Hans