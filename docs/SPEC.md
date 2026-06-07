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
├── backend/
│   ├── src/
│   ├── include/
│   └── CMakeLists.txt
├── frontend/
│   ├── index.html
│   ├── login.html
│   ├── problems.html
│   ├── problem.html
│   ├── admin.html
│   ├── problem-form.html
│   ├── css/
│   └── js/
├── sql/
│   ├── schema.sql
│   └── seed.sql
└── docs/
    └── SPEC.md
```

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
 
