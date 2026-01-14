老项目维护

先切环境

```bash
# 切换低版本node
nvm use v16

# 老项目目录

# 将你的Zulu JDK注册为本地版本
sdk install java 8-zulu-local /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home


cd /path/to/old-project
# 切换低版本java
sdk use java 8-zulu-local

```
