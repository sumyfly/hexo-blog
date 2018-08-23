---
title: Openssl使用
date: 2018-08-22 15:54:39
tags:
---

### 1.查看private key
openssl pkcs8  -in my_private_key -passin mypassworld
openssl pkcs12  -info -in my_private_key -passin mypassworld
> 注意不同的加密格式对应不同命令

<!-- more -->

### 2. PEM
Your keys may already be in PEM format, but just named with .crt or .key.
If the file's content begins with -----BEGIN and you can read it in a text editor:
The file uses base64, which is readable in ASCII, not binary format. The certificate is already in PEM format. Just change the extension to .pem.
If the file is in binary:
For the server.crt, you would use

openssl x509 -inform DER -outform PEM -in server.crt -out server.crt.pem
For server.key, use openssl rsa in place of openssl x509.

The server.key is likely your private key, and the .crt file is the returned, signed, x509 certificate.

If this is for a Web server and you cannot specify loading a separate private and public key:

You may need to concatenate the two files. For this use:

cat server.crt server.key > server.includesprivatekey.pem
I would recommend naming files with "includesprivatekey" to help you manage the permissions you keep with this file.


TODO:https://stackoverflow.com/questions/991758/how-to-get-pem-file-from-key-and-crt-files

https://www.sslshopper.com/article-most-common-openssl-commands.html