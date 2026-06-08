# 项目依赖安装说明

本文档根据 `docs/SPEC.md` 整理第一版 MVP 所需依赖。当前项目推荐在 WSL2 / Linux 环境运行，Windows 本机不作为第一版主要运行环境。

## 1. 依赖清单

### 系统环境

- WSL2 Ubuntu 或 Linux 发行版
- C++17 编译环境
- CMake
- MySQL Server
- MySQL 客户端与开发库

### C / C++ 工具链

- `gcc`：编译用户提交的 C 代码
- `g++`：编译后端服务和用户提交的 C++ 代码
- `make`：配合 CMake 构建
- `cmake`：生成构建工程

### 后端 C++ 依赖

- `cpp-httplib`：HTTP 服务与静态资源托管
- `nlohmann/json`：JSON 请求、响应解析与生成
- MySQL C API 或 MySQL Connector/C++：访问 MySQL

第一版建议使用 MySQL C API，对应 Ubuntu 包通常是 `libmysqlclient-dev`。

### 前端依赖

第一版前端使用原生 HTML、CSS、JavaScript，不需要安装 Node.js、npm 或前端框架。

## 2. Ubuntu / WSL2 安装方式

更新软件源：

```bash
sudo apt update
```

安装基础构建工具：

```bash
sudo apt install -y build-essential cmake pkg-config
```

安装 MySQL：

```bash
sudo apt install -y mysql-server mysql-client libmysqlclient-dev
```

安装后启动 MySQL：

```bash
sudo service mysql start
```

检查 MySQL 状态：

```bash
sudo service mysql status
```

进入 MySQL：

```bash
sudo mysql
```

## 3. 单头文件依赖安装方式

项目目录规划中建议将第三方单头文件放到：

```text
backend/third_party/
├── httplib.h
└── json.hpp
```

### cpp-httplib

从官方仓库下载 `httplib.h`，放入：

```text
backend/third_party/httplib.h
```

下载地址：

```text
https://github.com/yhirose/cpp-httplib/blob/master/httplib.h
```

### nlohmann/json

从官方仓库下载单头文件 `json.hpp`，放入：

```text
backend/third_party/json.hpp
```

下载地址：

```text
https://github.com/nlohmann/json/releases
```

选择 release 中的 `json.hpp` 单文件即可。

## 4. 可选：通过 apt 安装 JSON 库

如果不想手动放置 `json.hpp`，也可以使用 Ubuntu 包：

```bash
sudo apt install -y nlohmann-json3-dev
```

这种方式下，代码中通常使用：

```cpp
#include <nlohmann/json.hpp>
```

如果采用 `backend/third_party/json.hpp`，代码中通常使用：

```cpp
#include "json.hpp"
```

二者选择一种即可，避免重复混用。

## 5. 建议的安装顺序

1. 安装 WSL2 Ubuntu 或准备 Linux 环境。
2. 安装 `build-essential`、`cmake`、`pkg-config`。
3. 安装 `mysql-server`、`mysql-client`、`libmysqlclient-dev`。
4. 启动 MySQL，并准备项目数据库。
5. 下载 `httplib.h` 到 `backend/third_party/httplib.h`。
6. 下载 `json.hpp` 到 `backend/third_party/json.hpp`，或改用 `nlohmann-json3-dev`。
7. 编写 `backend/CMakeLists.txt`，链接 MySQL 客户端库。

## 6. 安装后检查

检查 GCC：

```bash
gcc --version
```

检查 G++：

```bash
g++ --version
```

检查 CMake：

```bash
cmake --version
```

检查 MySQL 客户端：

```bash
mysql --version
```

检查 MySQL 开发库：

```bash
mysql_config --version
mysql_config --libs
mysql_config --cflags
```

如果这些命令都能正常输出版本或编译参数，说明第一版项目所需的主要依赖已经准备好。

## 7. CMake 连接 MySQL 的提示

如果使用 MySQL C API，CMake 中通常需要包含 MySQL 头文件目录并链接 MySQL 客户端库。可以通过 `mysql_config` 获取参数：

```bash
mysql_config --cflags
mysql_config --libs
```

后续编写 `CMakeLists.txt` 时，可以先采用简单方式：

```cmake
execute_process(
    COMMAND mysql_config --cflags
    OUTPUT_VARIABLE MYSQL_CFLAGS
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

execute_process(
    COMMAND mysql_config --libs
    OUTPUT_VARIABLE MYSQL_LIBS
    OUTPUT_STRIP_TRAILING_WHITESPACE
)
```

第一版也可以手动配置 include 路径和链接库，等项目稳定后再整理为更规范的 CMake 查找逻辑。

## 8. 暂不需要安装的依赖

第一版暂不需要：

- Node.js
- npm / pnpm / yarn
- React / Vue / Angular
- Monaco Editor / CodeMirror
- Docker
- nsjail
- seccomp
- Redis
- Nginx

这些可以等 MVP 跑通后按需要再引入。
