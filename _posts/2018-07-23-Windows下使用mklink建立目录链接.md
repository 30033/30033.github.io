---
categories: [Windows]
tags: [link,onedrive]
---


# Windows下使用mklink建立目录链接

> 经常使用OneDrive同步Navicat里经常使用的SQL，问题是我只想同步指定的文件夹，就想到如果像Linux那样在OneDrive里做个软链接到Navicat配置文件目录就好了，直接用Windows右键建立快捷方式，直接打开Navicat，配置文件识别不到。
直接搜到了mlink这个命令，Windows自带的，用完之后完美支持

```bash
mklink /D 目的地址 源地址

```

> 如

```bash
mklink /D D:\users\desktop\tools\Navicat\public D:\users\onedrive\navicat\public
```

> 这样onedrive目录下的navicat\public文件夹就会在D:\users\desktop\tools\Navicat\建立一个软链接，Navicat也能识别到，这样经常用的SQL就可以用OneDrive同步了。
mklink 只支持同分区建立

