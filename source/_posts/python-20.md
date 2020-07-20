---
title: python之面向对象
tags:
  - python
  - linux
categories: 开发
date: 2020-07-14 18:29:35
---
在类的封装中,第一个参数必须是self
```bash
需求：小猫爱吃鱼  小猫要喝水
分析：
    类     猫
    动作    吃  喝水
  按照需求,不需要定义属性
```
```python
class Cat:
  def eat(self):
      print("小猫爱吃鱼")
  def drink(self):
      print("小猫要喝水")
tom = Cat()
tom.eat()
tom.drink()
