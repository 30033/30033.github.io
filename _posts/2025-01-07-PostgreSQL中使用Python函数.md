---
categories: [Linux]
tags: [PostgreSQL,tools,python,函数]
---

## 已安装的PostgreSQL中使用Python写的函数

>系统版本：CentOS 7.9.2009
>
>PostgreSQL版本:17.2
>
>Python版本:3.12.7

>先安装Python
>安装依赖
```
yum install -y gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel tkinter readline-devel python3-devel zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel tk-devel 
```
>Python3.12 要求openssl 在1.1.1以上，先升级下系统自带的openssl

```
wget -c https://mirrors.ibiblio.org/openssl/source/openssl-1.1.1w.tar.gz
tar xzf openssl-1.1.1w.tar.gz
cd openssl-1.1.1w
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
make -j
make install
```
>编译安装Python3.12

```
wget -c https://www.python.org/ftp/python/3.12.7/Python-3.12.7.tgz
tar xzf Python-3.12.7.tgz
cd Python-3.12.7
./configure --enable-loadable-sqlite-extensions --enable-shared --with-openssl=/usr/local/openssl --with-openssl-rpath=auto --prefix=/var/lib/python3.12.7
make -j
make install
```
>编辑 /etc/ld.so.conf 增加 /var/lib/python3.12.7/lib 并保存
>
>执行 ldconfig 生效



>PG如果已经编译安装好的话，需要重新编译下让它支持Python
>找到PG的源码目录，执行
```
cd postgresql-17.2
./configure --prefix=/data/pgsql/ --with-python
```
>不需要make 和 make install
编译如果没有错误的话的执行
```
cd src/pl/plpython/
make
make install
```

>成功之后进入数据库中进行安装扩展
```
CREATE EXTENSION plpython3u;
```
>这样就可以在PG中使用Python函数了

```

CREATE OR REPLACE FUNCTION v2m (idx character varying)
 RETURNS text
 LANGUAGE plpython3u
AS $function$
# -*- coding: utf-8 -*-
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
import hashlib
password='111111'
def get_pass(password):
    password = password.encode('UTF-8')
    key = hashlib.sha1(password).digest()
    key = hashlib.sha1(key).digest()
    return key[:16]
def aes_ecb_encrypt(plaintext, key):
    cipher = AES.new(key, AES.MODE_ECB)
    padded_data = pad(plaintext.encode(), AES.block_size)
    ciphertext = cipher.encrypt(padded_data)
    return ciphertext
def aes_ecb_decrypt(ciphertext, key):
    cipher = AES.new(key, AES.MODE_ECB)
    decrypted_data = cipher.decrypt(ciphertext)
    plaintext = unpad(decrypted_data, AES.block_size)
    return plaintext.decode()

def v2m(mobile):
    key = get_pass(password)
    plaintext = mobile.split('_')[1]
    decrypted_text = aes_ecb_decrypt(bytes.fromhex(plaintext), key)
    return decrypted_text

res=v2m(idx)
return res
$function$;

```



