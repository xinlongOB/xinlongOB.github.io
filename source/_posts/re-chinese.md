---
title: python re匹配中文和非中文
tags:
  - python
  - linux
categories: 开发
date: 2020-08-06 09:34:52
---
```python
import re

data = """我始终！@@##¥%…………&alkjdfsb1234\n
566667是中国人woaldsfkjzlkcjxv123*())<>
"""

# 匹配所有汉字
print(re.findall('[\u4e00-\u9fa5]', data))

# 匹配所有单字符，英文，数字，特殊符号
print(re.findall('[\x00-\xff]', data))

# 匹配所有非单字符，入汉字和省略号
print(re.findall('[^\x00-\xff]', data))
```