---
layout: post
title: saltstack sls 里的变量操控
category: saltstack, python, jinja
---

在编写 saltstack 的 sls 文件时，我们少不了和变量打交道，本文介绍变量的存贮，生成及变换。


# 变量存在什么地方

## pillar

`pillar` 数据在 master 上编译，主要存贮敏感的信息，比如帐号和密码配置，或者公用的信息
存贮在 `pillar` 里的变量是全局变量，所有 minions 都可以获取到 `pillar` 里的信息

## grains

`grain` 信息从 minion 上收集，相当于局部变量，每个 minion 的 grain 信息只能自己读取

## mine

`mine` 从 minion 上采集任何的信息，然后存贮在 master 上，进而 minion 通过 `salt.module.mine`
可以读取到 `mine` 上的所有信息, `mine` 里适合存贮在某ge minion 上生成的信息，需要分发到其他
的 minion 上，比如 docker swarm 里 token

## execute module

还有一些变量需要实时的获取，可以通过使用 exexute module 来获取


# 如何读取变量

## pillar

```python
pillar['key1']['key2']
``` 
或者 
```python
salt['pillar.get']('key1:key2', 'failover')
```
两者的主要区别是，当查找变量不存在的时候，后者可以返回一个默认值

如果想要查看所有的 pillar 信息，在命令行上使用 `salt-call pillar.items` 即可


## grains

```python
grain['key']
````


## mine
```python
salt['mine.get']('key1:key2')
```


## execute module

```python
salt['module.function']('arg_1', 'arg_2')
```



# 变量的变换

## 生成新变量

```python
set str_var = "hello" 
set int_var = 5 
set dict_var = {} 
set array_var = [] 
```



## 变量变换

jinja 里自带了几十个 filter, 可以完成对变量的部分操作，具体的请查询 [jinja] 文档

str 大小写转换
```python
set a = "Hello World"|lower
```

```python
set b = 1 + 2 
```

dict 更新
```python
do dict_var.update({"key": "value"})
```

新增 item 到 array
```python
set _ = array_var.append(item)
```


[jinja]: http://jinja.pocoo.org/docs/2.10/templates/#id11




