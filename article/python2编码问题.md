Title:   python2编码问题
Summary: A brief description of my document.
Author: Waylan Limberg
         John Doe
Date:    October 2, 2007
blank-value: 
base_url: http://example.com

[TOC]

#python2编码问题

##代码如下

    :::python
    #coding:utf-8

    u=u'中文'
    s='中文'
    print u
    print s


####1. 代码文件开头加上`#coding:utf-8`，代码中有中文字符，文件用utf-8编码保存，一切正常。

####2. 代码文件开头加上`#coding:utf-8`，代码中有中文字符，文件用gbk编码保存，报错`SyntaxError: (unicode error) 'utf8' codec can't decode byte 0xd6 in position 0: invalid continuation byte`

####3. 代码文件开头无`#coding:utf-8`，代码中有中文字符，文件用utf-8编码保存，报错`SyntaxError: Non-ASCII character '\xd6' in file`

####4. 代码文件开头无`#coding:utf-8`，代码中有中文字符，文件用gbk编码保存，报错`SyntaxError: Non-ASCII character '\xd6' in file`

## str与unicode

###假设代码文件开头加上`#coding:utf-8`，代码中有中文字符，文件用utf-8编码保存。控制台可以显示utf-8编码的字符。

####1. `u=u'中文'`，u是unicode类型。

####2. `u=u'中文'`，u.encode('utf-8')是str类型,编码格式是utf-8。

####3. `u=u'中文'`，u.encode('gbk')是str类型，编码格式是gbk。

####4. `u=u'中文'`，u.encode("ascii")报错`UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)`，ascii编码无法保存中文字符。

####5. `u=u'中文'`，u.decode('utf-8')，u.decode("gbk")，u.decode("ascii")报错`UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)`。

--------------------------

####1. `s='中文'`,s是str类型，编码格式是utf-8。

####2. `s='中文'`,s.decode('utf-8')是unicode类型。

####3. `s='中文'`,s.decode('gbk')报错`UnicodeDecodeError: 'gbk' codec can't decode bytes in position 2-3: illegal multibyte sequence`。

####4. `s='中文'`,s.decode("utf-16")是unicode类型，但是内容已经乱码。

####5. `s='中文'`,s.encode('gbk')，s.encode('utf-8')，s.encode('ascii')，s.encode('utf-16')报错`UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)`。

##总结：

####unicode类型通过encode()方法用指定编码转成str类型，此时可以储存到磁盘或数据库。

####str类型通过decode()方法用指定编码转成unicode类型，若指定编码和str的编码一致，此时可以使用这些字符；若不一致，则可能报错或者出现乱码。

####str.decode(原编码).encode(目标编码)，这样将str字符用目标编码保存。