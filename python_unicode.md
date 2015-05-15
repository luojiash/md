# python unicode
tags: python 编码

---
## 1. unicode 与 utf8

Unicode 是一个符号集，它规定了符号的二进制代码，但没有规定这个二进制代码应该如何存储。比如，汉字‘严’的unicode 是十六进制数4e25，转换成二进制有15位（100111000100101），也就是说存储这个符号至少需要2个字节。
```python
>>> u'严'
u'\u4e25'
>>> bin(0x4e25)
'0b100111000100101'
```

utf8是unicode的一种实现方式，utf8最大的特点是采用变长的编码方式，可以使用1-4个字节表示一个符号，根据不同的符号变化字节长度。

unicode是python内部的一种编码方式，使用以下函数进行编码的转换。[^uncheck]

* `str.decode([encoding])` 把字符串解码成unicode 字符集
* `str.encode([encoding])` 把字符串编码
* `unicode('汉字', 'utf8')` 等同于decode
```python
str.encode('hex') # 字符串的16进制表示
sys.getdefaultencoding() # ascii, 系统默认编码方式
```

## 2. 正确编码中文

在代码中正确编码中文，有如下几种方式。统一的思路都是将str转为unicode。
```python
str = u'得到'
print type(str) # <type 'unicode'>
```
```python
str = unicode('得到', 'utf8')
#'utf8' 是代码文件的编码，一般都指定为utf8
```
```python
str = '得到'.decode('utf8') #'utf8' 是文件的编码
print type(str) # <type 'unicode'>
```

## 3. 终端的编码

终端指各种命令行界面，Windows是cmd,Linux是terminal. 各种终端显示字符的编码是不同的，中文版Windows cmd的编码是gbk，Linux terminal 是utf8。

1. 正确编码
使用正确的编码才不会在终端显示乱码，正确的编码指终端的显示编码
```python
s1 = u'得到'
print s1.encode('utf8') # 寰楀埌
```
Windows cmd 下乱码，因为终端使用gbk编码显示，改为gbk编码即可
2. 正确解码终端的文字
使用错误的编码方式解码会报错。Windows cmd 下，
```python
>>> unicode('得到','gbk')
u'\u5f97\u5230'
>>> unicode('得到','utf8')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python27\lib\encodings\utf_8.py", line 16, in decode
    return codecs.utf_8_decode(input, errors, True)
UnicodeDecodeError: 'utf8' codec can't decode byte 0xb5 in position 0: invalid start byte
```

---
参考资料：
[字符编码笔记：ASCII，Unicode和UTF-8 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)

[^uncheck]: 此处还有疑惑，unicode是一种编码规范，utf8是其实现方式。可能涉及str和unicode类型两者的区别，待查。unicode 在python内部如何实现？