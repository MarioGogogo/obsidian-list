# React Native APK 打包指南

完整的 Release 版本打包流程，从签名配置到生成可分发的 APK 文件。

## 前置要求

- Android SDK 已安装
- Java 17（RN 0.72+）或 Java 11（RN 0.60-0.71）
- Node.js 环境

---

## 一、配置签名

Release 版本必须使用签名密钥。

### 生成签名密钥

在项目根目录执行：

```bash
keytool -genkeypair -v -storetype PKCS12 -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

**重要提示**：
- 将生成的 `my-release-key.keystore` 移动到 `android/app/` 目录
- **妥善保管密钥文件和密码**，丢失后无法更新应用
- 不要将密钥文件提交到 Git

### 配置 gradle.properties

编辑 `android/gradle.properties`，添加签名配置：

```properties
MYAPP_RELEASE_STORE_FILE=my-release-key.keystore
MYAPP_RELEASE_KEY_ALIAS=my-key-alias
MYAPP_RELEASE_STORE_PASSWORD=你的密码
MYAPP_RELEASE_KEY_PASSWORD=你的密码
```

**安全建议**：不要直接在文件中写明文密码，可以使用环境变量或本地配置文件（记得加入 `.gitignore`）。

### 配置 build.gradle

编辑 `android/app/build.gradle`，在 `android` 块中添加签名配置：

```gradle
android {
    ...其他配置

    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
                storeFile file(MYAPP_RELEASE_STORE_FILE)
                storePassword MYAPP_RELEASE_STORE_PASSWORD
                keyAlias MYAPP_RELEASE_KEY_ALIAS
                keyPassword MYAPP_RELEASE_KEY_PASSWORD
            }
        }
    }

    buildTypes {
        release {
            ...其他配置
            signingConfig signingConfigs.release
        }
    }
}
```

---

## 二、生成 APK

### 方法一：使用 Gradle 命令（推荐）

在 `android/` 目录下执行：

```bash
cd android

# 生成 Release APK
./gradlew assembleRelease

# 或者在项目根目录
npx react-native run-android --variant=release
```

生成的 APK 位置：
```
android/app/build/outputs/apk/release/app-release.apk
```

### 方法二：使用 Android Studio

1. 打开 Android Studio
2. 选择 **Build** → **Generate Signed Bundle / APK**
3. 选择 **APK**，点击 **Next**
4. 选择或创建密钥库（keystore）
5. 选择 **release** 构建变体
6. 点击 **Finish**

---

## 三、配置应用信息

### 修改应用名称

编辑 `android/app/src/main/res/values/strings.xml`：

```xml
<string name="app_name">你的应用名称</string>
```

### 修改包名和应用 ID

**1. 修改 `android/app/build.gradle`：**

```gradle
android {
    defaultConfig {
        applicationId "com.yourcompany.yourapp"
    }
}
```

**2. 修改目录结构：**

将 `android/app/src/main/java/com/yourcompany/yourapp/` 目录按新包名调整。

### 修改版本号

编辑 `android/app/build.gradle`：

```gradle
android {
    defaultConfig {
        versionCode 1        // 内部版本号（整数，每次发布递增）
        versionName "1.0.0"  // 显示给用户的版本号
    }
}
```

**版本号规则**：
- `versionCode`：整数，每次发布必须递增，用于判断版本新旧
- `versionName`：字符串，格式如 `1.0.0`，用户可见

---

## 四、优化 APK 体积

### 启用代码混淆

编辑 `android/app/build.gradle`：

```gradle
android {
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
        }
    }
}
```

### 启用 APK 拆分

针对不同屏幕密度和 CPU 架构生成多个 APK：

```gradle
android {
    splits {
        abi {
            enable true
            reset()
            include 'armeabi-v7a', 'arm64-v8a', 'x86', 'x86_64'
            universalApk false
        }
    }
}
```

### 常见体积优化技巧

- 移除未使用的语言资源：`resConfigs "zh", "en"`
- 启用代码压缩（shrinkResources）
- 使用矢量图替代 PNG 图片
- 启用 R8 全模式优化

---

## 五、测试 APK

### 安装到设备

```bash
adb install app-release.apk

# 如果已安装，使用 -r 参数覆盖安装
adb install -r app-release.apk
```

### 验证签名

```bash
# 使用 apksigner（Android SDK 工具）
apksigner verify --print-certs app-release.apk
```

### 查看应用信息

```bash
# 使用 aapt 查看 APK 信息
aapt dump badging app-release.apk | grep package
```

---

## 六、常见问题

### 打包失败：Gradle 构建错误

**原因**：Gradle 版本不兼容或网络问题

**解决方案**：

```bash
# 清理 Gradle 缓存
cd android
./gradlew clean

# 检查 Gradle 版本
./gradlew --version

# 如果网络问题，配置国内镜像
# 编辑 android/build.gradle，添加阿里云镜像
```

### 签名配置错误

**错误信息**：`Cannot recover key`

**解决方案**：
- 确认密码正确
- 检查 `gradle.properties` 中的配置是否与 keystore 一致
- 确认 keystore 文件路径正确

### APK 安装失败

**原因**：签名不一致或权限问题

**解决方案**：
- 卸载旧版本后重新安装
- 确保使用相同的签名密钥
- 检查 Android 版本兼容性

### 打包后应用闪退

**检查清单**：
- 查看日志：`adb logcat | grep ReactNative`
- 确认 ProGuard 规则是否正确配置第三方库
- 检查是否使用了开发环境特定的 API

---

## 七、发布到应用商店

### 准备材料

1. **APK 文件**：已签名的 Release APK
2. **应用图标**：多种尺寸的图标
3. **截图**：不同尺寸的设备截图
4. **描述**：应用简介和更新说明
5. **隐私政策**：隐私政策链接（必需）
6. **内容分级**：填写内容评级问卷

### 上传到 Google Play

1. 登录 [Google Play Console](https://play.google.com/console)
2. 创建新应用或选择现有应用
3. 填写应用信息
4. 上传 APK 到**生产环境**或**测试轨道**
5. 填写商店列表信息
6. 提交审核

### 国内应用商店

- **小米应用商店**：[dev.mi.com](https://dev.mi.com)
- **华为应用市场**：[developer.huawei.com](https://developer.huawei.com)
- **应用宝**：[open.tencent.com](https://open.tencent.com)
- **360 手机助手**：[dev.360.cn](https://dev.360.cn)

---

## 八、完整打包流程示例

```bash
# 1. 进入项目目录
cd YourProject

# 2. 清理之前的构建
cd android
./gradlew clean

# 3. 生成 Release APK
./gradlew assembleRelease

# 4. 查看生成的 APK
ls -lh app/build/outputs/apk/release/app-release.apk

# 5. 安装测试
adb install app/build/outputs/apk/release/app-release.apk

# 6. 查看日志（如果有问题）
adb logcat | grep ReactNative
```

---

## 九、参考链接

- [React Native 官方文档 - Signed APK](https://reactnative.dev/docs/signed-apk-android)
- [Android 应用签名](https://developer.android.com/studio/publish/app-signing)
- [Google Play 上架指南](https://support.google.com/googleplay/android-developer)

---

## 更新日志

- 2026-01-14：补充完整打包流程、优化建议和故障排除
- 初版：基础签名配置
