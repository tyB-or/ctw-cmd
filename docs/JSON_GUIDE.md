# JSON数据源使用指南

本指南介绍如何使用工具管理系统的JSON数据源功能，包括离线工具和网页工具的加载和使用。

## 概述

工具管理系统v0.2.1版本支持JSON数据源，提供三个专用工具：

1. **cmmd** - 命令管理工具，用于管理xlsx格式的命令库
2. **tool** - 离线工具管理工具，用于管理JSON格式的离线工具
3. **web** - 网页工具管理工具，用于管理JSON格式的网页工具

## 支持的JSON格式

### 离线工具JSON格式

离线工具JSON文件应包含以下结构：

```json
{
  "scan_path": "/path/to/tools",
  "auto_refresh": true,
  "tools": [
    {
      "id": "unique-id",
      "name": "工具名称",
      "category": "分类",
      "path": "/path/to/tool",
      "description": "工具描述",
      "tags": ["标签1", "标签2"],
      "command": "启动命令",
      "url": "相关网址"
    }
  ]
}
```

### 网页工具JSON格式

网页工具JSON文件应包含以下结构：

```json
{
  "notes": [
    {
      "id": "unique-id",
      "title": "网页标题",
      "url": "https://example.com",
      "source": "来源",
      "tool": "相关工具",
      "tags": ["标签1", "标签2"],
      "note": "笔记内容"
    }
  ]
}
```

## 基本用法

### 离线工具管理 (tool)

```bash
# 加载离线工具JSON文件
tool -o /path/to/offline_tools_sec-tools.json

# 不带参数启动(自动加载上次使用的JSON文件)
tool
```

### 网页工具管理 (web)

```bash
# 加载网页工具JSON文件
web -w /path/to/web_notes.json

# 不带参数启动(自动加载上次使用的JSON文件)
web
```

### 命令管理 (cmmd)

```bash
# 加载xlsx命令库文件
cmmd -l /path/to/commands.xlsx

# 不带参数启动(自动加载上次使用的xlsx文件)
cmmd
```

## 帮助信息

所有三个工具都提供了详细的帮助信息，通过`-h`或`--help`参数查看：

```bash
cmmd -h
tool -h
web -h
```

帮助信息包括：
- 配置命令（加载数据源、显示版本等）
- 搜索命令（关键词、标签、工具名等）
- 统计命令（显示标签和工具名统计）
- 快捷操作（使用@序号快速访问）
- 使用示例

v0.2.1版本优化了帮助信息的显示效果，实现了右侧描述文本的统一对齐，使帮助信息更加清晰易读，特别是对于中英文混合文本。

## 统计功能

所有三个工具都支持标签统计和工具统计功能，使用Grid布局显示：

```bash
# 显示所有标签统计
tool -T
web -T
cmmd -T

# 显示所有工具名统计
tool -C
web -C
cmmd -C
```

### Grid布局显示

v0.2.1版本引入了Grid布局系统，用于优化标签(-T)和工具(-C)统计的显示效果：

- 自动计算最佳列数和列宽
- 精确处理中英文混合文本的宽度差异
- 动态调整列间距和对齐方式
- 统一的显示格式，提高可读性

例如，标签统计显示如下：

```
标签统计 (共 15 个):

安全(5)    渗透测试(8)    漏洞扫描(3)    信息收集(6)    代码审计(4)
数据库(3)  网络工具(7)    加密解密(2)    社会工程(1)    取证分析(5)
```

## 使用示例

### 示例1：搜索离线工具

```bash
# 加载离线工具库并搜索所有与"git"相关的命令
tool -o offline_tools_sec-tools.json git

# 按标签搜索框架工具
tool -o offline_tools_sec-tools.json -t framework

# 按工具名搜索
tool -o offline_tools_sec-tools.json -c Burp
```

### 示例2：显示统计信息

```bash
# 显示所有标签统计
tool -o offline_tools_sec-tools.json -T

# 显示所有工具名统计
tool -o offline_tools_sec-tools.json -C
```

### 示例3：交互模式

```bash
# 进入交互模式
tool -o offline_tools_sec-tools.json

# 在交互模式中：
# - 输入关键词搜索
# - 使用Tab键/方向键选择
# - 回车选中命令
# - 输入 @<序号> 直接选择对应序号的命令
# - q键退出
```

### 示例4：快速访问

```bash
# 快速访问指定ID的命令
tool -o offline_tools_sec-tools.json @1
```

## 高级用法

### 组合搜索

```bash
# 组合搜索：关键词 + 标签 + 工具名
tool -o offline_tools_sec-tools.json 渗透测试 -t framework -c Spring
```

### 多关键词搜索

```bash
# 使用逗号或者空格分隔多个关键词
tool -o offline_tools_sec-tools.json git,commit,push
```

## 多平台构建

v0.2.1版本优化了构建系统，支持多平台交叉编译：

```bash
# 编译所有支持的平台
./build.sh

# 仅编译当前平台
./build.sh --local-only
```

编译后的可执行文件将保存在`build`目录下，按平台分类：
- `build/darwin_amd64/` (macOS Intel)
- `build/darwin_arm64/` (macOS Apple Silicon)
- `build/linux_amd64/` (Linux x86_64)
- `build/linux_arm64/` (Linux ARM64)
- `build/windows_amd64/` (Windows x64)

## 常见问题

### 1. 找不到JSON文件

确保提供了正确的文件路径。可以使用绝对路径避免问题：

```bash
tool -o /absolute/path/to/offline_tools_sec-tools.json
```

### 2. JSON解析错误

确保JSON文件格式正确，没有语法错误。可以使用在线JSON验证工具检查。

### 3. 命令ID不存在

使用统计命令查看可用的命令ID：

```bash
tool -o offline_tools_sec-tools.json -C
```

### 4. 显示对齐问题

如果统计信息显示不对齐，可能是因为：
- 终端窗口太窄
- 使用了特殊字符或超长文本
- 终端字体不支持等宽显示

解决方法：
- 调整终端窗口宽度
- 使用支持等宽字体的终端
- 避免在标签和工具名中使用特殊字符

## 测试脚本

项目根目录提供了测试脚本，可以快速测试JSON功能：

```bash
# Linux/macOS
./test_json.sh

# Windows
test_json.bat
```

## 更多信息

更多详细信息，请参考：

- [README.md](../README.md)：完整的使用说明
- [CHANGELOG.md](CHANGELOG.md)：版本更新记录
- [INSTALL.md](INSTALL.md)：安装和配置指南 