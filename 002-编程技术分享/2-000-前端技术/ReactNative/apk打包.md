### 签名配置（Release 版本需要）

如果是第一次打包 Release，需要先配置签名：

1. 生成签名密钥：
    
    ```bash
    keytool -genkeypair -v -storetype PKCS12 -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
    ```
    
1. 在 `android/gradle.properties` 添加：
    
properties
    MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
    MYAPP_RELEASE_KEY_ALIAS=my-key-alias
    MYAPP_RELEASE_STORE_PASSWORD=密码
    MYAPP_RELEASE_KEY_PASSWORD=密码
    ```
    
1. 修改 `android/app/build.gradle` 配置[[签名]]

### 2. 移动签名文件到正确位置

```bash
# 生成的 keystore 文件在 android 目录，已被 gradle.properties 引用
# 如果不在 android 目录，移动过去：
# mv my-release-key.keystore android/
```