#!/bin/bash

# 割草游戏项目初始化脚本
# 用途：创建完整的项目目录结构和基础文件

set -e  # 遇到错误立即退出

# 颜色定义
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# 项目根目录名称
PROJECT_NAME="LawnMowerGame"

echo -e "${BLUE}========================================${NC}"
echo -e "${BLUE}  割草游戏项目初始化${NC}"
echo -e "${BLUE}========================================${NC}"
echo ""

# 检查项目目录是否已存在
if [ -d "$PROJECT_NAME" ]; then
    echo -e "${YELLOW}警告：目录 $PROJECT_NAME 已存在${NC}"
    read -p "是否删除并重新创建？(y/N): " -n 1 -r
    echo
    if [[ $REPLY =~ ^[Yy]$ ]]; then
        rm -rf "$PROJECT_NAME"
        echo -e "${GREEN}已删除旧目录${NC}"
    else
        echo -e "${RED}取消初始化${NC}"
        exit 1
    fi
fi

# 创建项目根目录
echo -e "${BLUE}创建项目根目录...${NC}"
mkdir -p "$PROJECT_NAME"
cd "$PROJECT_NAME"

# ==================== 创建共享 proto 目录 ====================
echo -e "${BLUE}创建共享 proto 目录...${NC}"
mkdir -p proto

# ==================== 创建服务端目录结构 ====================
echo -e "${BLUE}创建 C++ 服务端目录结构...${NC}"

# 服务端主目录
mkdir -p server/{include,src,generated,config,tools,tests}

# include 目录
mkdir -p server/include/{network,game,utils,core}
mkdir -p server/include/network/{tcp,udp}
mkdir -p server/include/game/{entities,systems,managers}

# src 目录
mkdir -p server/src/{network,game,utils,core}
mkdir -p server/src/network/{tcp,udp}
mkdir -p server/src/game/{entities,systems,managers}

# tests 目录
mkdir -p server/tests/{unit,integration}

# ==================== 创建客户端目录结构 ====================
echo -e "${BLUE}创建 Java 客户端目录结构...${NC}"

# 客户端主目录
mkdir -p client/{core,desktop,assets}

# core/src 目录
mkdir -p client/core/src/com/lawnmower/{network,screens,entities,systems,ui,utils}

# assets 目录
mkdir -p client/assets/{textures,sounds,music,fonts,ui}
mkdir -p client/assets/textures/{player,enemy,items,effects,background}

# desktop 目录
mkdir -p client/desktop/src/com/lawnmower/desktop

# ==================== 创建文档目录 ====================
echo -e "${BLUE}创建文档目录...${NC}"
mkdir -p docs/{architecture,design,api,guides}

# ==================== 创建基础配置文件 ====================
echo -e "${BLUE}创建基础配置文件...${NC}"

# .gitignore
cat > .gitignore << 'EOF'
# 构建目录
build/
server/build/
server/generated/*.pb.*
client/build/
client/out/

# IDE
.vscode/
.idea/
*.iml
.eclipse/
.settings/

# 编译产物
*.o
*.obj
*.exe
*.out
*.so
*.dylib
*.dll
*.a
*.lib

# 日志
*.log
logs/

# 配置文件（可选）
*.local.json
.env

# 操作系统
.DS_Store
Thumbs.db

# Gradle
.gradle/
gradle-app.setting
!gradle-wrapper.jar

# Java
*.class
hs_err_pid*

# 临时文件
*.swp
*.swo
*~
EOF

# README.md（简化版，引导用户查看详细文档）
cat > README.md << 'EOF'
# 割草游戏 (LawnMower Game)

一款受《植物大战僵尸》和《吸血鬼幸存者》启发的多人生存 Roguelike 游戏。

## 快速开始

### 环境要求

**服务端（C++）：**
- GCC 13+ 或 Clang 16+
- CMake 3.20+
- Protocol Buffers 3.20+
- Asio、spdlog

**客户端（Java）：**
- JDK 17+
- Gradle 7.0+

### 构建步骤

详见 [docs/guides/BUILD.md](docs/guides/BUILD.md)

## 项目结构

```
LawnMowerGame/
├── proto/          # 共享协议定义
├── server/         # C++ 服务端
├── client/         # Java 客户端
└── docs/           # 项目文档
```

## 文档

- [架构设计](docs/architecture/OVERVIEW.md)
- [开发指南](docs/guides/DEVELOPMENT.md)
- [网络协议](docs/api/PROTOCOL.md)

## 许可证

MIT License
EOF

# server/CMakeLists.txt
cat > server/CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.20)
project(LawnMowerServer CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# 查找依赖
find_package(Protobuf REQUIRED)
find_package(spdlog REQUIRED)

# Protobuf 代码生成
set(PROTO_DIR ${CMAKE_SOURCE_DIR}/../proto)
set(GEN_DIR ${CMAKE_SOURCE_DIR}/generated)
file(MAKE_DIRECTORY ${GEN_DIR})

file(GLOB PROTO_FILES "${PROTO_DIR}/*.proto")
set(GENERATED_SRCS)

foreach(PROTO ${PROTO_FILES})
    get_filename_component(NAME ${PROTO} NAME_WE)
    add_custom_command(
        OUTPUT ${GEN_DIR}/${NAME}.pb.cc ${GEN_DIR}/${NAME}.pb.h
        COMMAND protobuf::protoc
        ARGS --cpp_out=${GEN_DIR} --proto_path=${PROTO_DIR} ${PROTO}
        DEPENDS ${PROTO}
    )
    list(APPEND GENERATED_SRCS ${GEN_DIR}/${NAME}.pb.cc)
endforeach()

# Proto 库
add_library(proto_lib STATIC ${GENERATED_SRCS})
target_include_directories(proto_lib PUBLIC ${GEN_DIR})
target_link_libraries(proto_lib PUBLIC protobuf::libprotobuf)

# 主程序
add_executable(server src/main.cpp)
target_include_directories(server PRIVATE include)
target_link_libraries(server PRIVATE proto_lib spdlog::spdlog)
EOF

# server/src/main.cpp
cat > server/src/main.cpp << 'EOF'
#include <iostream>
#include <spdlog/spdlog.h>

int main() {
    spdlog::info("LawnMower Server 启动中...");
    spdlog::info("服务端主程序 - 等待实现网络模块");
    
    // TODO: 初始化网络服务器
    // TODO: 启动游戏循环
    
    return 0;
}
EOF

# server/config/server_config.json
cat > server/config/server_config.json << 'EOF'
{
    "tcp_port": 7777,
    "udp_port": 7778,
    "max_players_per_room": 4,
    "tick_rate": 60,
    "state_sync_rate": 20,
    "map_width": 2000,
    "map_height": 2000,
    "log_level": "info"
}
EOF

# client/build.gradle
cat > client/build.gradle << 'EOF'
buildscript {
    repositories {
        mavenCentral()
        maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }
        google()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:7.0.4'
    }
}

allprojects {
    version = '1.0'
    ext {
        appName = "LawnMowerGame"
        gdxVersion = '1.11.0'
        nettyVersion = '4.1.100.Final'
        protobufVersion = '3.25.1'
    }

    repositories {
        mavenCentral()
        maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }
    }
}

project(":desktop") {
    apply plugin: "java-library"

    dependencies {
        implementation project(":core")
        api "com.badlogicgames.gdx:gdx-backend-lwjgl3:$gdxVersion"
        api "com.badlogicgames.gdx:gdx-platform:$gdxVersion:natives-desktop"
    }
}

project(":core") {
    apply plugin: "java-library"

    dependencies {
        api "com.badlogicgames.gdx:gdx:$gdxVersion"
        
        // 网络
        implementation "io.netty:netty-all:$nettyVersion"
        
        // Protobuf
        implementation "com.google.protobuf:protobuf-java:$protobufVersion"
    }
}
EOF

# client/settings.gradle
cat > client/settings.gradle << 'EOF'
include 'desktop', 'core'
EOF

# client/core/src/com/lawnmower/Main.java
cat > client/core/src/com/lawnmower/Main.java << 'EOF'
package com.lawnmower;

import com.badlogic.gdx.ApplicationAdapter;
import com.badlogic.gdx.Gdx;
import com.badlogic.gdx.graphics.GL20;

public class Main extends ApplicationAdapter {
    @Override
    public void create() {
        System.out.println("LawnMower Client 启动中...");
    }

    @Override
    public void render() {
        Gdx.gl.glClearColor(0.2f, 0.3f, 0.3f, 1);
        Gdx.gl.glClear(GL20.GL_COLOR_BUFFER_BIT);
    }

    @Override
    public void dispose() {
        System.out.println("客户端关闭");
    }
}
EOF

# client/desktop/src/com/lawnmower/desktop/DesktopLauncher.java
cat > client/desktop/src/com/lawnmower/desktop/DesktopLauncher.java << 'EOF'
package com.lawnmower.desktop;

import com.badlogic.gdx.backends.lwjgl3.Lwjgl3Application;
import com.badlogic.gdx.backends.lwjgl3.Lwjgl3ApplicationConfiguration;
import com.lawnmower.Main;

public class DesktopLauncher {
    public static void main(String[] args) {
        Lwjgl3ApplicationConfiguration config = new Lwjgl3ApplicationConfiguration();
        config.setTitle("LawnMower Game");
        config.setWindowedMode(800, 600);
        config.setForegroundFPS(60);
        new Lwjgl3Application(new Main(), config);
    }
}
EOF

# proto/messages.proto（基础版本）
cat > proto/messages.proto << 'EOF'
syntax = "proto3";
package lawnmower;

// 基础类型
message Vector2 {
    float x = 1;
    float y = 2;
}

// 心跳
message C2S_Heartbeat {
    uint64 timestamp = 1;
}

message S2C_Heartbeat {
    uint64 timestamp = 1;
}

// 登录
message C2S_Login {
    string player_name = 1;
}

message S2C_LoginResult {
    bool success = 1;
    uint32 player_id = 2;
    string message = 3;
}

// 消息封装
message Packet {
    uint32 msg_type = 1;
    bytes payload = 2;
}
EOF

# docs 基础文档
cat > docs/guides/BUILD.md << 'EOF'
# 构建指南

## 服务端构建

```bash
cd server
mkdir build && cd build
cmake ..
make -j$(nproc)
./server
```

## 客户端构建

```bash
cd client
./gradlew desktop:dist
./gradlew desktop:run
```
EOF

cat > docs/guides/DEVELOPMENT.md << 'EOF'
# 开发指南

## 环境配置

详见 README.md

## 开发流程

1. 从 proto 定义开始
2. 生成代码
3. 实现业务逻辑
4. 测试
EOF

cat > docs/architecture/OVERVIEW.md << 'EOF'
# 架构概览

## 总体架构

- 客户端-服务器架构
- 权威服务器设计
- UDP + TCP 混合通信

## 模块划分

### 服务端
- 网络层
- 游戏逻辑层
- 数据层

### 客户端
- 网络层
- 渲染层
- 输入层
- UI层
EOF

# 创建 LICENSE
cat > LICENSE << 'EOF'
MIT License

Copyright (c) 2024 LawnMower Game Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
EOF

# 创建开发说明文件
cat > DEVELOPMENT_NOTES.md << 'EOF'
# 开发笔记

## 当前进度

- [x] 项目结构创建
- [ ] Protobuf 协议完善
- [ ] 网络通信实现
- [ ] 游戏逻辑开发

## 下一步

1. 完善 proto/messages.proto
2. 实现 TCP 服务器
3. 实现 Java 客户端连接

## 注意事项

- 修改 proto 文件后需要重新生成代码
- 保持两端协议同步
EOF

# ==================== 完成 ====================
echo ""
echo -e "${GREEN}========================================${NC}"
echo -e "${GREEN}  项目结构创建完成！${NC}"
echo -e "${GREEN}========================================${NC}"
echo ""
echo -e "${BLUE}项目目录：${NC} $(pwd)"
echo ""
echo -e "${YELLOW}目录结构：${NC}"
tree -L 3 -I 'build|generated' 2>/dev/null || find . -type d -not -path '*/\.*' | head -30

echo ""
echo -e "${BLUE}下一步操作：${NC}"
echo -e "  1. ${GREEN}cd $PROJECT_NAME${NC}"
echo -e "  2. 完善 ${YELLOW}proto/messages.proto${NC} 协议定义"
echo -e "  3. 安装依赖："
echo -e "     ${YELLOW}sudo pacman -S cmake protobuf asio spdlog${NC}"
echo -e "  4. 构建服务端："
echo -e "     ${YELLOW}cd server && mkdir build && cd build && cmake .. && make${NC}"
echo -e "  5. 初始化 Git 仓库："
echo -e "     ${YELLOW}git init && git add . && git commit -m 'Initial commit'${NC}"
echo ""
echo -e "${GREEN}祝开发顺利！${NC}"
