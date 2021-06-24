---
title: linux使用pyinstaller
image: /images/pyinstaller.jpg
date: 2021-06-02 23:29:43
tags: [linux,python]
categories: 
- python
---

### 安装pyinstaller

[Downloads — PyInstaller bundles Python applications](https://www.pyinstaller.org/downloads.html)

下载

```shell
wget https://github.com/pyinstaller/pyinstaller/releases/download/v4.2/PyInstaller-4.2.tar.gz

tar -xvf PyInstaller-4.2.tar.gz
```

build

```shell
cd pyinstaller-4.2

python3 setup.py build
```



install

```shell
sudo python3 setup.py install
```

测试是否安装成功

```shell
pyinstaller --version
```

### 打包python项目

```shell
pyinstaller -F program_exporter.py
```

