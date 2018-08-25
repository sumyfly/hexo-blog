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

### 5. Android Studio Debug签名
默认Android Stuido Debug时会自动签名(这个是IDE生成的，gradle命令行不会自动生成签名)，用的keystore是没有密码的，但是有时候我们调试第三方登录，需要固定的签名，所以就自己生成签名，覆盖默认的Debug的签名。

#### 5.1 生成Debug签名
`$ keytool -genkey -v -keystore debug.keystore -storepass android -alias androiddebugkey -keypass android -keyalg RSA -keysize 2048 -validity 10000 -dname "C=US, O=Android, CN=Android Debug"
`
https://gist.github.com/henriquemenezes/70feb8fff20a19a65346e48786bedb8f

#### 5.2 设置keystore.properties

keystore.properties
``` groovy 
storePassword=myStorePassword
keyPassword=mykeyPassword
keyAlias=myKeyAlias
storeFile=myStoreFileLocation
```

#### 5.3 设置app/build.gradle
模块的build.gradle
``` groovy
// 在模块的 build.gradle 文件中，于 android {} 块的前面添加用于加载 keystore.properties 文件的代码。
def keystorePropertiesFile = rootProject.file("keystore.properties")

// Initialize a new Properties() object called keystoreProperties.
def keystoreProperties = new Properties()

// Load your keystore.properties file into the keystoreProperties object.
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))

android {
   signingConfigs {
        debug {
            keyAlias keystoreProperties['keyAlias']
            keyPassword keystoreProperties['keyPassword']
            storeFile file(keystoreProperties['storeFile'])
            storePassword keystoreProperties['storePassword']
        }
        release {
            keyAlias keystoreProperties['keyAlias']
            keyPassword keystoreProperties['keyPassword']
            storeFile file(keystoreProperties['storeFile'])
            storePassword keystoreProperties['storePassword']
        }
    }
    ...
  }
```
这样设置后，debug签名就更换成我们设置的签名了。但是`./graldew assembleRelease`还是要设置buildTypes才能使用release签名。
``` groovy
buildTypes {
    release {
        minifyEnabled enableProguardInReleaseBuilds
        proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        signingConfig signingConfigs.release
    }
}
```

https://developer.android.com/studio/publish/app-signing
https://blog.csdn.net/zouchengxufei/article/details/48747803