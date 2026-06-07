# 仿 LeetCode OJ 项目 SPEC.md

## 1. 项目目标

搭建一个面向个人学习的在线评测系统 MVP，支持用户在线浏览题目、编写 C/C++ 代码、运行样例、提交判题，并支持管理员维护题目。

项目重点是快速实现可用闭环，而不是一开始追求完整生产级 OJ。

## 2. 成功标准

第一版完成后，应支持：

- 固定账号登录
- 普通用户查看题目列表
- 普通用户查看题目详情
- 普通用户在线编辑 C/C++ 完整程序
- 支持运行样例测试
- 支持提交全部测试用例判题
- 返回编译错误、运行错误、超时、输出对比、通过测试点数量、耗时、内存字段
- 管理员新增、编辑、软删除题目
- 管理员管理样例与隐藏测试用例
- 普通用户不能访问管理接口
- 题目数据持久化到 MySQL
- 提交记录和用户代码暂不持久化

## 3. 用户角色

### 普通用户

固定账号：

- `user / user123`

能力：

- 登录
- 查看题目列表
- 查看题目详情
- 在线写代码
- 运行样例
- 提交判题
- 查看本页面会话内的提交结果

### 管理员

固定账号：

- `admin / admin123`

能力：

- 普通用户全部能力
- 查看管理后台
- 新增题目
- 编辑题目
- 软删除题目
- 管理样例测试用例
- 管理隐藏测试用例

## 4. 第一版页面范围

必须实现：

- 登录页
- 题目列表页
- 题目详情 / 做题页
- 管理员题目管理页
- 管理员新增 / 编辑题目页

暂缓实现：

- 用户主页
- 真实提交历史页
- 排行榜
- 题解
- 讨论区
- 比赛模式
- 用户通过状态持久化
- 复杂数据统计

## 5. 前端设计

技术栈：

- 原生 HTML
- 原生 CSS
- 原生 JavaScript
- 不使用前端框架

UI 风格：

- 类似 LeetCode 的浅色简洁风格
- 代码编辑区域第一版使用 `<textarea>`
- 后续可替换为 Monaco Editor 或 CodeMirror

题目详情页包含：

- 题目标题
- 难度
- 标签
- 题目描述
- 输入格式
- 输出格式
- 样例
- 时间限制
- 内存限制
- 语言选择：C / C++
- 代码编辑框
- 运行按钮
- 提交按钮
- 判题结果面板
- 本页面会话内最近提交记录

## 6. 后端技术栈

后端：

- C++
- cpp-httplib

数据库：

- MySQL

建议依赖：

- `nlohmann/json`：处理 JSON
- MySQL C API 或 MySQL Connector/C++：访问 MySQL
- CMake：构建项目

运行环境：

- 推荐 WSL2 / Linux
- 默认使用 Linux 路径、Linux 进程控制、`gcc`、`g++`
- Windows 本机不作为第一版主要运行环境

静态资源托管：

- cpp-httplib 后端同时托管前端静态文件
- 用户访问 `http://localhost:8080` 使用系统
- 避免前后端跨域问题

## 7. 项目目录结构

```text
oj-project/
├── backend/                         # C++ 后端服务目录
│   ├── CMakeLists.txt               # 后端 CMake 构建配置
│   ├── include/                     # 后端头文件
│   │   ├── api/                     # HTTP 路由声明
│   │   │   ├── admin_routes.h       # 管理员接口路由声明
│   │   │   ├── auth_routes.h        # 登录认证接口路由声明
│   │   │   ├── judge_routes.h       # 运行与提交判题接口路由声明
│   │   │   └── problem_routes.h     # 普通题目接口路由声明
│   │   ├── auth/                    # 认证与权限模块声明
│   │   │   ├── auth_service.h       # 登录、用户身份校验服务声明
│   │   │   └── token_store.h        # 简单 token 存储与校验声明
│   │   ├── common/                  # 通用基础模块声明
│   │   │   ├── config.h             # 配置项声明
│   │   │   ├── error.h              # 统一错误结构声明
│   │   │   ├── response.h           # 统一 JSON 响应封装声明
│   │   │   └── utils.h              # 通用工具函数声明
│   │   ├── db/                      # 数据库访问模块声明
│   │   │   ├── db_pool.h            # MySQL 连接管理声明
│   │   │   ├── problem_repository.h # 题目数据访问声明
│   │   │   ├── test_case_repository.h # 测试用例数据访问声明
│   │   │   └── user_repository.h    # 用户数据访问声明
│   │   ├── judge/                   # 判题模块声明
│   │   │   ├── compiler.h           # C/C++ 编译封装声明
│   │   │   ├── judge_service.h      # 判题主流程服务声明
│   │   │   ├── output_compare.h     # 输出比较规则声明
│   │   │   ├── process_runner.h     # 子进程运行与超时控制声明
│   │   │   └── temp_workspace.h     # 临时目录管理声明
│   │   └── models/                  # 数据模型声明
│   │       ├── problem.h            # 题目模型
│   │       ├── test_case.h          # 测试用例模型
│   │       └── user.h               # 用户模型
│   ├── src/                         # 后端实现文件
│   │   ├── api/                     # HTTP 路由实现
│   │   │   ├── admin_routes.cpp     # 管理员接口实现
│   │   │   ├── auth_routes.cpp      # 登录认证接口实现
│   │   │   ├── judge_routes.cpp     # 判题接口实现
│   │   │   └── problem_routes.cpp   # 题目接口实现
│   │   ├── auth/                    # 认证与权限模块实现
│   │   │   ├── auth_service.cpp     # 登录、用户身份校验服务实现
│   │   │   └── token_store.cpp      # 简单 token 存储与校验实现
│   │   ├── common/                  # 通用基础模块实现
│   │   │   ├── config.cpp           # 配置读取与默认值实现
│   │   │   ├── error.cpp            # 错误码与错误对象实现
│   │   │   ├── response.cpp         # 统一 JSON 响应封装实现
│   │   │   └── utils.cpp            # 通用工具函数实现
│   │   ├── db/                      # 数据库访问模块实现
│   │   │   ├── db_pool.cpp          # MySQL 连接管理实现
│   │   │   ├── problem_repository.cpp # 题目数据访问实现
│   │   │   ├── test_case_repository.cpp # 测试用例数据访问实现
│   │   │   └── user_repository.cpp  # 用户数据访问实现
│   │   ├── judge/                   # 判题模块实现
│   │   │   ├── compiler.cpp         # C/C++ 编译封装实现
│   │   │   ├── judge_service.cpp    # 判题主流程服务实现
│   │   │   ├── output_compare.cpp   # 输出比较规则实现
│   │   │   ├── process_runner.cpp   # 子进程运行与超时控制实现
│   │   │   └── temp_workspace.cpp   # 临时目录管理实现
│   │   └── main.cpp                 # 后端入口，注册路由并启动服务
│   ├── third_party/                 # 第三方单头文件依赖
│   │   ├── httplib.h                # cpp-httplib HTTP 服务库
│   │   └── json.hpp                 # nlohmann/json JSON 库
│   └── tmp/                         # 后端运行时临时目录
│       └── judge/                   # 判题临时工作目录
├── frontend/                        # 原生 HTML/CSS/JS 前端目录
│   ├── admin.html                   # 管理员题目管理页
│   ├── index.html                   # 首页或默认跳转页
│   ├── login.html                   # 登录页
│   ├── problem.html                 # 题目详情与做题页
│   ├── problem-form.html            # 管理员新增 / 编辑题目页
│   ├── problems.html                # 题目列表页
│   ├── css/                         # 样式文件目录
│   │   ├── admin.css                # 管理后台样式
│   │   ├── auth.css                 # 登录页样式
│   │   ├── problem.css              # 题目详情页样式
│   │   └── style.css                # 全局通用样式
│   └── js/                          # 前端脚本目录
│       ├── admin.js                 # 管理员题目管理逻辑
│       ├── api.js                   # API 请求封装
│       ├── auth.js                  # 登录、登出、token 处理
│       ├── problem.js               # 题目详情、运行、提交逻辑
│       ├── problem-form.js          # 新增 / 编辑题目表单逻辑
│       └── problems.js              # 题目列表页逻辑
├── sql/                             # 数据库脚本目录
│   ├── schema.sql                   # 建表脚本
│   └── seed.sql                     # 初始化账号、题目、测试用例数据
├── docs/                            # 项目文档目录
│   └── SPEC.md                      # 项目规格说明文档
├── .gitignore                       # Git 忽略规则
└── README.md                        # 项目说明与运行指南
```

目录职责：

- `backend/include`：后端头文件，按 API、认证、数据库、判题、通用工具、数据模型分层。
- `backend/src`：后端实现文件，目录结构与 `include` 保持一致。
- `backend/third_party`：第一版可直接放置单头文件依赖，例如 `cpp-httplib` 与 `nlohmann/json`。
- `backend/tmp/judge`：判题临时工作目录，运行时创建随机子目录，提交结束后清理；该目录不提交生成文件。
- `frontend`：由后端静态托管的页面、样式与脚本，不引入前端构建工具。
- `sql`：数据库建表脚本与初始化数据脚本。
- `docs`：项目规格、接口说明、后续设计文档。

## 8. 数据持久化范围

需要持久化：

- 用户固定账号
- 用户角色
- 题目
- 样例测试用例
- 隐藏测试用例
- 题目软删除状态
- 创建时间
- 更新时间

暂不持久化：

- 用户提交记录
- 用户代码
- 用户是否通过某题
- 排行榜数据

## 9. 数据库设计建议

### users

字段：

- `id`
- `username`
- `password_hash`
- `role`
- `created_at`
- `updated_at`

角色：

- `user`
- `admin`

### problems

字段：

- `id`
- `title`
- `difficulty`
- `tags`
- `description`
- `input_format`
- `output_format`
- `time_limit_ms`
- `memory_limit_mb`
- `is_deleted`
- `created_at`
- `updated_at`

难度固定：

- `Easy`
- `Medium`
- `Hard`

标签：

- 自由输入字符串

默认限制：

- 时间限制：`1000ms`
- 内存限制：`128MB`

### test_cases

字段：

- `id`
- `problem_id`
- `type`
- `stdin`
- `expected_stdout`
- `created_at`
- `updated_at`

`type`：

- `sample`
- `hidden`

## 10. 判题模型

用户提交完整 C/C++ 程序，必须自己写 `main`，通过标准输入读取，通过标准输出输出结果。

### 语言支持

C：

```bash
gcc -std=c11 -O2
```

C++：

```bash
g++ -std=c++17 -O2
```

### 运行按钮

只运行样例测试用例。

### 提交按钮

运行全部测试用例：

- 样例测试用例
- 隐藏测试用例

### 测试用例结构

每个测试用例只包含：

- `stdin`
- `expected_stdout`

暂不支持：

- 文件输入输出
- 浮点误差判定
- Special Judge / 自定义 checker
- 交互题
- 多线程限制
- 系统调用过滤

### 输出比较规则

第一版采用简单标准输出比较：

- 忽略末尾空白字符
- 忽略末尾换行
- 不忽略中间空格
- 不忽略行顺序
- 不做浮点误差容忍

示例：

```text
"1 2\n" == "1 2"
"1  2" != "1 2"
```

## 11. 判题准确性风险与处理

你担心判题准确性，这是合理的。第一版需要明确这些边界，避免误以为已经达到 LeetCode 完整能力。

### 第一版可保证

- 编译失败能准确返回编译错误
- 程序超时能返回 TLE
- 程序非零退出能返回运行错误
- 标准输出与期望输出可做确定性比较
- 每个测试点独立运行
- 返回通过测试点数量
- 返回失败用例的输入、期望输出、实际输出

### 第一版不保证

- 浮点题精度判断
- 多答案题判断
- 图、集合等无序输出判断
- 交互题判断
- 文件 IO 题判断
- 非标准输出格式的智能纠错
- 系统级安全隔离
- 严格内存限制

### 降低误判建议

第一版题目应优先选择：

- 输入输出确定
- 答案唯一
- 不涉及浮点误差
- 不需要 Special Judge
- 测试数据较小
- 单个测试点运行时间较短

不建议第一版使用：

- 浮点精度题
- 多解题
- 输出任意合法方案的题
- 大数据压力题
- 需要自定义 checker 的题

## 12. 安全策略

MVP 最低安全措施：

- 每次编译运行使用独立临时目录
- 使用随机文件名
- 编译后生成独立可执行文件
- 每个测试点设置超时时间
- 超时后 kill 子进程
- 运行完成后清理临时文件
- 后端不直接拼接未转义用户输入到 shell 命令
- 普通用户无法访问 `/api/admin/*`

暂不实现：

- Docker 沙箱
- nsjail
- seccomp
- chroot
- 网络隔离
- 严格内存限制
- 系统调用白名单

## 13. API 设计

### 认证

登录后返回简单 token。

前端保存到 `localStorage`。

后续可升级为 Cookie + HttpOnly Session。

### 权限规则

- 未登录访问受保护接口：`401`
- 普通用户访问管理员接口：`403`
- 管理员接口统一以 `/api/admin/*` 开头

### API 列表

认证：

- `POST /api/login`

题目：

- `GET /api/problems`
- `GET /api/problems/:id`

判题：

- `POST /api/submissions/run`
- `POST /api/submissions/submit`

管理员：

- `GET /api/admin/problems`
- `POST /api/admin/problems`
- `PUT /api/admin/problems/:id`
- `DELETE /api/admin/problems/:id`

## 14. 统一错误格式

```json
{
  "success": false,
  "error": {
    "code": "COMPILE_ERROR",
    "message": "编译失败",
    "detail": "具体错误信息"
  }
}
```

常见错误码：

- `UNAUTHORIZED`
- `FORBIDDEN`
- `NOT_FOUND`
- `VALIDATION_ERROR`
- `DATABASE_ERROR`
- `COMPILE_ERROR`
- `RUNTIME_ERROR`
- `TIME_LIMIT_EXCEEDED`
- `JUDGE_ERROR`
- `INTERNAL_ERROR`

## 15. 判题结果格式

```json
{
  "success": true,
  "data": {
    "status": "ACCEPTED",
    "passed": 3,
    "total": 3,
    "cases": [
      {
        "index": 1,
        "status": "ACCEPTED",
        "stdin": "1 2",
        "expected_stdout": "3",
        "actual_stdout": "3",
        "time_ms": 12,
        "memory_kb": null
      }
    ],
    "compile_output": "",
    "total_time_ms": 36
  }
}
```

状态：

- `ACCEPTED`
- `WRONG_ANSWER`
- `COMPILE_ERROR`
- `RUNTIME_ERROR`
- `TIME_LIMIT_EXCEEDED`
- `JUDGE_ERROR`

## 16. 架构图

```text
Browser
  |
  | HTTP
  v
cpp-httplib Server
  |
  |-- Static File Server
  |     |
  |     |-- HTML / CSS / JS
  |
  |-- Auth API
  |
  |-- Problem API
  |     |
  |     v
  |   MySQL
  |
  |-- Admin API
  |     |
  |     v
  |   MySQL
  |
  |-- Judge API
        |
        |-- Create temp directory
        |-- Write user code
        |-- Compile with gcc / g++
        |-- Run executable per test case
        |-- Capture stdout / stderr
        |-- Compare output
        |-- Return result
```

## 17. TODO 清单

### 阶段 1：项目基础

- 初始化项目目录
- 配置 CMake
- 引入 cpp-httplib
- 引入 JSON 库
- 配置 MySQL 连接
- 编写 `schema.sql`
- 编写 `seed.sql`
- 准备固定账号
- 准备示例题

### 阶段 2：认证与权限

- 实现登录接口
- 实现 token 生成
- 实现 token 校验
- 实现角色校验
- 保护管理员接口

### 阶段 3：题目接口

- 实现题目列表接口
- 实现题目详情接口
- 过滤软删除题目
- 返回样例测试用例
- 普通用户不返回隐藏测试用例

### 阶段 4：管理员接口

- 实现管理员题目列表
- 实现新增题目
- 实现编辑题目
- 实现软删除题目
- 实现样例测试用例管理
- 实现隐藏测试用例管理

### 阶段 5：判题服务

- 创建临时运行目录
- 写入用户代码
- 根据语言选择编译命令
- 捕获编译错误
- 按测试点运行程序
- 设置超时
- 捕获 stdout / stderr
- 比较输出
- 返回测试点结果
- 清理临时文件

### 阶段 6：前端页面

- 登录页
- 题目列表页
- 题目详情页
- 代码编辑区域
- 运行结果面板
- 会话内提交记录
- 管理员题目管理页
- 新增 / 编辑题目页

### 阶段 7：验收测试

- 普通用户登录
- 管理员登录
- 普通用户查看题目列表
- 普通用户查看题目详情
- 普通用户运行样例
- 普通用户提交判题
- 编译错误返回
- 运行错误返回
- 超时返回
- 输出错误返回
- 管理员新增题目
- 管理员编辑题目
- 管理员软删除题目
- 普通用户无法访问管理员接口

## 18. 验收标准

### 用户验收

- 使用 `user / user123` 可以登录
- 普通用户可以看到未删除题目
- 普通用户可以进入题目详情
- 普通用户可以提交 C 代码
- 普通用户可以提交 C++ 代码
- 正确代码返回 `ACCEPTED`
- 错误代码返回 `WRONG_ANSWER`
- 编译失败返回 `COMPILE_ERROR`
- 死循环代码返回 `TIME_LIMIT_EXCEEDED`
- 本页面能显示最近提交结果

### 管理员验收

- 使用 `admin / admin123` 可以登录
- 管理员可以进入后台
- 管理员可以新增题目
- 管理员可以编辑题目
- 管理员可以软删除题目
- 删除后的题目普通用户不可见
- 管理员接口拒绝普通用户访问

### 判题准确性验收

至少准备以下测试题：

- A+B，验证标准输入输出
- 多行输入题，验证换行处理
- 字符串题，验证空格敏感
- 编译错误代码，验证错误返回
- 死循环代码，验证超时
- 输出格式错误代码，验证 WA

判题结果必须能区分：

- 正确
- 答案错误
- 编译失败
- 运行崩溃
- 超时
- 系统判题异常
*** End Patch
 
