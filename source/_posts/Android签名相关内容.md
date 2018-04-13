---
title: Android签名相关内容
date: 2018-04-13 15:37:27
tags:
---
### 1. 查看签名
- 提取签名
`jar xf HelloWorld.apk META-INF/CERT.RSA`
- 打印签名
`keytool -printcert -file CERT.RSA`

- 打印公钥，签名用的公钥
`keytool -printcert -file CERT.RSA -rfc`，或者导出到一个文件`keytool -printcert -file CERT.RSA -rfc > my_pub.key`

相同的签名的不同版本APK应用相同的md5，sha1,sha256指纹，代表同一个公钥签名。
 

### 2. 查看public key的指纹
> 指纹就是用散列算法计算公钥的文本，rfc格式(带有`-----BEGIN CERTIFICATE-----`和`-----END CERTIFICATE-----`)。

使用上面步骤得到的my_pub.key，验证md5。实际工作中不需要，工具都帮我们做好了。
``` bash
openssl x509 -noout -fingerprint -md5 -inform pem -in my_pub.key
openssl x509 -noout -fingerprint -SHA1 -inform pem -in my_pub.key
openssl x509 -noout -fingerprint -SHA256 -inform pem -in my_pub.key
```

### 3. 查看keystore中的签名
> keystore中有多个公钥秘钥对，一个对就是一个alias, 每个的访问密码不同。
`keytool -list -keystore my.keystore -alias myAlias`输入库密码可以看到公钥的指纹。


### 4. 给apk签名
`jarsigner -verbose -keystore my.keystore -signedjar ma_app_signed.apk my.apk myAlias`