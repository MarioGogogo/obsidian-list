看到错误了！这是因为 `react-native` CLI 不认识 `webpack-bundle` 命令，需要先完成 **Re.Pack 的完整配置**。

---

## 🔧 完整配置 Re.Pack

### 步骤 1：安装完整依赖

```bash
cd NebulaRN

安装系统
npx @react-native-community/cli init NebulaRN3 --version 0.76.5

# 安装 Re.Pack 及相关依赖
npm install --save-dev @callstack/repack webpack terser-webpack-plugin babel-loader

# 安装 React Native 文件系统库（用于远程加载）
npm install react-native-fs

# iOS 需要 pod install
cd ios && pod install && cd ..



npx @callstack/repack-init@4.4.1

Need to install the following packages:
@callstack/repack-init@4.4.1
Ok to proceed? (y) y

npm warn deprecated node-domexception@1.0.0: Use your platform's native DOMException instead
✔ Dependencies installed
✔ Downloading webpack.config.mjs template
✔ Created webpack.config.mjs from template
✔ Created react-native.config.js
✔ Added "webpack-bundle" as bundleCommand to android/app/build.gradle
✔ Successfully modified iOS project files

🎉 Setup complete. Thanks for using Re.Pack!
```

---

### 步骤 2：创建 React Native 配置文件

创建 `react-native.config.js`：

```javascript
// react-native.config.js
module.exports = {
  commands: require('@callstack/repack/commands'),
};
```

---

### 步骤 3：创建 Webpack 配置

创建 `webpack.config.mjs`：

```javascript
// webpack.config.mjs
import path from 'path';
import TerserPlugin from 'terser-webpack-plugin';
import * as Repack from '@callstack/repack';

/**
 * @param env - 环境变量
 * @returns Webpack 配置
 */
export default (env) => {
  const {
    mode = 'development',
    context = Repack.getDirname(import.meta.url),
    entry = './index.js',
    platform = process.env.PLATFORM,
    minimize = mode === 'production',
    devServer = undefined,
    bundleFilename = undefined,
    sourceMapFilename = undefined,
    assetsPath = undefined,
  } = env;

  const isProd = mode === 'production';

  return {
    mode,
    devtool: false,
    context,
  
    /**
     * 入口文件配置
     */
    entry: {
      index: entry,
    },

    /**
     * 解析配置
     */
    resolve: {
      ...Repack.getResolveOptions(platform),
      alias: {
        '@': path.resolve(context, 'src'),
      },
    },

    /**
     * 输出配置
     */
    output: {
      clean: true,
      path: path.join(context, 'build/generated', platform),
      filename: 'index.bundle',
      chunkFilename: '[name].chunk.bundle',
      publicPath: Repack.getPublicPath({ platform, devServer }),
    },

    /**
     * 优化配置
     */
    optimization: {
      minimize,
      minimizer: [
        new TerserPlugin({
          test: /\.(jsx?|tsx?)$/,
          extractComments: false,
        }),
      ],
      chunkIds: 'named',
      splitChunks: {
        chunks: 'all',
        cacheGroups: {
          // 分离第三方库
          vendor: {
            test: /[\\/]node_modules[\\/]/,
            name: 'vendor',
            priority: 10,
          },
          // 自定义分包
          miniApp: {
            test: /[\\/]src[\\/]bundles[\\/]MiniApp/,
            name: 'miniApp',
            priority: 20,
          },
        },
      },
    },

    /**
     * 模块规则
     */
    module: {
      rules: [
        /**
         * 处理 JS/TS 文件
         */
        {
          test: /\.[jt]sx?$/,
          exclude: /node_modules/,
          use: {
            loader: 'babel-loader',
            options: {
              presets: [
                ['module:@react-native/babel-preset', { useTransformReactJSXExperimental: true }],
              ],
              plugins: [
                ['@babel/plugin-transform-react-jsx', { runtime: 'automatic' }],
              ],
            },
          },
        },

        /**
         * 处理图片等资源
         */
        {
          test: Repack.getAssetExtensionsRegExp(Repack.ASSET_EXTENSIONS),
          use: {
            loader: '@callstack/repack/assets-loader',
            options: {
              platform,
              devServerEnabled: Boolean(devServer),
            },
          },
        },
      ],
    },

    /**
     * 插件配置
     */
    plugins: [
      new Repack.RepackPlugin({
        context,
        mode,
        platform,
        devServer,
        output: {
          bundleFilename,
          sourceMapFilename,
          assetsPath,
        },
      }),
    ],
  };
};
```

---

### 步骤 4：修改 `package.json`

```json
{
  "name": "NebulaRN",
  "version": "0.0.1",
  "scripts": {
    "android": "react-native run-android",
    "ios": "react-native run-ios",
    "start": "react-native webpack-start",
    "bundle:android": "react-native webpack-bundle --platform android --mode production",
    "bundle:ios": "react-native webpack-bundle --platform ios --mode production",
    "dev": "react-native webpack-start --mode development"
  },
  "dependencies": {
    "react": "18.3.1",
    "react-native": "0.76.5",
    "react-native-fs": "^2.20.0"
  },
  "devDependencies": {
    "@callstack/repack": "^4.3.2",
    "babel-loader": "^9.2.1",
    "terser-webpack-plugin": "^5.3.10",
    "webpack": "^5.97.1"
  }
}
```

---

### 步骤 5：创建目录结构

```bash
# 创建项目结构
mkdir -p src/bundles
mkdir -p src/components
mkdir -p src/utils

# 移动文件
mv App.tsx src/App.tsx
```

---

### 步骤 6：修改 `index.js`

```javascript
// index.js
import { AppRegistry } from 'react-native';
import App from './src/App';
import { name as appName } from './app.json';

AppRegistry.registerComponent(appName, () => App);
```

---

### 步骤 7：创建主包 `src/App.tsx`

```typescript
// src/App.tsx
import React, { useState, Suspense } from 'react';
import {
  View,
  Text,
  TouchableOpacity,
  StyleSheet,
  ActivityIndicator,
  SafeAreaView,
} from 'react-native';

const App = () => {
  const [loadMiniApp, setLoadMiniApp] = useState(false);

  const handleLoadMiniApp = () => {
    console.log('🚀 开始加载分包...');
    setLoadMiniApp(true);
  };

  const handleUnload = () => {
    console.log('📦 卸载分包');
    setLoadMiniApp(false);
  };

  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.content}>
        <Text style={styles.title}>React Native 分包加载</Text>
        <Text style={styles.subtitle}>主包（Main Bundle）</Text>

        {!loadMiniApp ? (
          <TouchableOpacity
            style={styles.loadButton}
            onPress={handleLoadMiniApp}
          >
            <Text style={styles.buttonText}>🚀 加载分包（MiniApp）</Text>
          </TouchableOpacity>
        ) : (
          <View style={styles.miniAppWrapper}>
            <Suspense
              fallback={
                <View style={styles.loading}>
                  <ActivityIndicator size="large" color="#007AFF" />
                  <Text style={styles.loadingText}>加载中...</Text>
                </View>
              }
            >
              <MiniAppLoader onUnload={handleUnload} />
            </Suspense>
          </View>
        )}
      </View>
    </SafeAreaView>
  );
};

// 懒加载分包组件
const MiniAppLoader = ({ onUnload }: { onUnload: () => void }) => {
  const MiniApp = React.lazy(() => import(
    /* webpackChunkName: "miniApp" */
    './bundles/MiniApp'
  ));

  return (
    <View style={styles.miniAppContainer}>
      <MiniApp />
      <TouchableOpacity
        style={styles.unloadButton}
        onPress={onUnload}
      >
        <Text style={styles.buttonText}>❌ 卸载分包</Text>
      </TouchableOpacity>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f5f5f5',
  },
  content: {
    flex: 1,
    padding: 20,
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#333',
    marginTop: 40,
    marginBottom: 8,
  },
  subtitle: {
    fontSize: 16,
    color: '#666',
    marginBottom: 40,
  },
  loadButton: {
    backgroundColor: '#007AFF',
    height: 56,
    borderRadius: 12,
    justifyContent: 'center',
    alignItems: 'center',
    shadowColor: '#007AFF',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 8,
    elevation: 5,
  },
  unloadButton: {
    backgroundColor: '#FF3B30',
    height: 48,
    borderRadius: 12,
    justifyContent: 'center',
    alignItems: 'center',
    marginTop: 20,
  },
  buttonText: {
    color: '#fff',
    fontSize: 18,
    fontWeight: '600',
  },
  miniAppWrapper: {
    flex: 1,
  },
  miniAppContainer: {
    flex: 1,
    backgroundColor: '#fff',
    borderRadius: 16,
    padding: 20,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.1,
    shadowRadius: 12,
    elevation: 5,
  },
  loading: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  loadingText: {
    marginTop: 12,
    fontSize: 16,
    color: '#666',
  },
});

export default App;
```

---

### 步骤 8：创建分包 `src/bundles/MiniApp/index.tsx`

```typescript
// src/bundles/MiniApp/index.tsx
import React, { useState, useEffect } from 'react';
import {
  View,
  Text,
  StyleSheet,
  TouchableOpacity,
  Animated,
} from 'react-native';

const MiniApp = () => {
  const [count, setCount] = useState(0);
  const [fadeAnim] = useState(new Animated.Value(0));

  useEffect(() => {
    console.log('✅ MiniApp 分包加载成功！');
  
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 600,
      useNativeDriver: true,
    }).start();
  }, []);

  return (
    <Animated.View style={[styles.container, { opacity: fadeAnim }]}>
      <Text style={styles.emoji}>🎉</Text>
      <Text style={styles.title}>分包内容已加载</Text>
      <Text style={styles.description}>
        这是一个独立的 Bundle，按需加载！
      </Text>

      <View style={styles.card}>
        <Text style={styles.cardTitle}>计数器示例</Text>
        <Text style={styles.counter}>{count}</Text>
      
        <View style={styles.buttonRow}>
          <TouchableOpacity
            style={[styles.counterBtn, styles.decrementBtn]}
            onPress={() => setCount(c => c - 1)}
          >
            <Text style={styles.counterBtnText}>-</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={[styles.counterBtn, styles.resetBtn]}
            onPress={() => setCount(0)}
          >
            <Text style={styles.resetBtnText}>重置</Text>
          </TouchableOpacity>

          <TouchableOpacity
            style={[styles.counterBtn, styles.incrementBtn]}
            onPress={() => setCount(c => c + 1)}
          >
            <Text style={styles.counterBtnText}>+</Text>
          </TouchableOpacity>
        </View>
      </View>

      <View style={styles.infoBox}>
        <Text style={styles.infoTitle}>📦 分包特性</Text>
        {['独立打包', '按需加载', '减小主包体积', '支持热更新'].map((item, index) => (
          <Text key={index} style={styles.infoItem}>• {item}</Text>
        ))}
      </View>
    </Animated.View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  emoji: {
    fontSize: 64,
    textAlign: 'center',
    marginBottom: 16,
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#333',
    textAlign: 'center',
    marginBottom: 8,
  },
  description: {
    fontSize: 14,
    color: '#666',
    textAlign: 'center',
    marginBottom: 24,
  },
  card: {
    backgroundColor: '#f8f9fa',
    borderRadius: 16,
    padding: 24,
    marginBottom: 20,
    alignItems: 'center',
  },
  cardTitle: {
    fontSize: 18,
    fontWeight: '600',
    color: '#333',
    marginBottom: 16,
  },
  counter: {
    fontSize: 56,
    fontWeight: 'bold',
    color: '#007AFF',
    marginBottom: 20,
  },
  buttonRow: {
    flexDirection: 'row',
    gap: 12,
  },
  counterBtn: {
    width: 64,
    height: 64,
    borderRadius: 32,
    justifyContent: 'center',
    alignItems: 'center',
  },
  decrementBtn: {
    backgroundColor: '#FF3B30',
  },
  incrementBtn: {
    backgroundColor: '#34C759',
  },
  resetBtn: {
    backgroundColor: '#8E8E93',
    paddingHorizontal: 16,
    width: 80,
  },
  counterBtnText: {
    color: '#fff',
    fontSize: 32,
    fontWeight: 'bold',
  },
  resetBtnText: {
    color: '#fff',
    fontSize: 14,
    fontWeight: '600',
  },
  infoBox: {
    backgroundColor: '#E3F2FD',
    borderRadius: 12,
    padding: 16,
    borderLeftWidth: 4,
    borderLeftColor: '#007AFF',
  },
  infoTitle: {
    fontSize: 16,
    fontWeight: '600',
    color: '#333',
    marginBottom: 12,
  },
  infoItem: {
    fontSize: 14,
    color: '#666',
    marginBottom: 6,
    lineHeight: 20,
  },
});

export default MiniApp;
```

---

### 步骤 9：运行项目

```bash
# 1. 安装依赖
npm install

# 2. 启动开发服务器
npm start

# 3. 运行 Android
npm run android

# 4. 生产打包（生成分包）
npm run bundle:android
```

---

### 步骤 10：验证分包

打包后查看生成的文件：

```bash
ls -lh build/generated/android/

# 应该看到：
# index.bundle          # 主包
# miniApp.chunk.bundle  # 分包
# vendor.chunk.bundle   # 第三方库分包
```

---

## ✅ 验证清单

```bash
✅ react-native.config.js 已创建
✅ webpack.config.mjs 已配置
✅ package.json scripts 已更新
✅ 目录结构正确（src/bundles/MiniApp）
✅ npm install 完成
✅ npm start 能运行
✅ npm run android 成功
✅ 点击按钮能加载分包
```

---

如果还有问题，把错误信息发给我，我继续帮你解决！🚀