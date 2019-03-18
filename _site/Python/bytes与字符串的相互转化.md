```python
# bytes转字符串方式一
b=b'\xe9\x80\x86\xe7\x81\xab'
string=str(b,'utf-8')
print(string)

# bytes转字符串方式二
b=b'\xe9\x80\x86\xe7\x81\xab'
string=b.decode() # 第一参数默认utf8，第二参数默认strict
print(string)

# bytes转字符串方式三
b=b'\xe9\x80\x86\xe7\x81haha\xab'
string=b.decode('utf-8','ignore') # 忽略非法字符，用strict会抛出异常
print(string)

# bytes转字符串方式四
b=b'\xe9\x80\x86\xe7\x81haha\xab'
string=b.decode('utf-8','replace') # 用？取代非法字符
print(string)

# 字符串转bytes方式一
str1='逆火'
b=bytes(str1, encoding='utf-8')
print(b)

# 字符串转bytes方式二
b=str1.encode('utf-8')
print(b)
```

