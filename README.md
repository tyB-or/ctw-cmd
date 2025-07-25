# 终端命令与工具管理工具

这是一个基于Go语言开发的适用多操作系统的终端命令和工具管理系统，旨在帮助用户快速查找常用命令和工具。用户可以通过标签、关键词和描述来组织和检索命令，大大提高终端操作效率。

该项目提供三个专用工具：

- **cmmd** - 命令管理工具，用于管理xlsx格式的命令库
- **tool** - 离线工具管理工具，用于管理JSON格式的离线工具
- **web** - 网页工具管理工具，用于管理JSON格式的网页工具

支持从多种数据源加载数据：
- XLSX文件（传统命令格式）
- JSON文件（离线工具和网页工具）

## 功能特点

- **多数据源支持**：支持从xlsx文件或JSON文件加载数据
- **快速检索**：通过关键词、标签或描述快速查找
- **标签系统**：使用标签组织命令和工具，便于分类管理
- **灵活的搜索**：支持多种搜索方式，包括组合搜索和模糊搜索
- **简单易用**：简洁的命令行界面，易于上手
- **彩色输出**：使用一致的彩色编码系统增强可读性和直观性
- **快速粘贴**：一键将命令粘贴到终端中
- **多平台支持**：支持macOS、Linux和Windows系统
- **Grid布局显示**：优化标签和工具统计的显示效果，解决中英文混合对齐问题

## 安装

```bash
# 克隆代码库
git clone https://github.com/yourusername/cmd.git
cd cmd

# 编译所有工具
# Linux/macOS
./build.sh

# 仅编译当前平台
./build.sh --local-only

# Windows
build.bat

# 仅编译当前平台
build.bat --local-only
```

编译后的可执行文件将保存在以下位置：
- 当前平台的可执行文件：项目根目录下的`cmmd`、`tool`和`web`
- 多平台编译的可执行文件：`build`目录下按平台分类，如`build/darwin_amd64/`、`build/windows_amd64/`等

## 使用方法

### 三个工具的基本用法

```bash
# 命令管理工具 (cmmd)
cmmd -l commands.xlsx         # 加载xlsx命令库文件
cmmd                          # 进入交互模式(自动加载上次使用的xlsx文件)
cmmd -h                       # 显示帮助信息

# 离线工具管理工具 (tool)
tool -o offline_tools.json    # 加载离线工具JSON文件
tool                          # 进入交互模式(自动加载上次使用的JSON文件)
tool -h                       # 显示帮助信息

# 网页工具管理工具 (web)
web -w web_notes.json         # 加载网页工具JSON文件
web                           # 进入交互模式(自动加载上次使用的JSON文件)
web -h                        # 显示帮助信息
```

### 搜索功能(所有工具通用)

```bash
# 基本搜索
cmmd docker          # 从命令和描述中搜索包含docker的命令
tool scanner         # 从工具名和描述中搜索包含scanner的离线工具
web api              # 从标题和描述中搜索包含api的网页工具

# 多关键词搜索
cmmd git,commit      # 从命令和描述中搜索同时包含git和commit的命令
tool sql,注入        # 从工具名和描述中搜索同时包含sql和注入的离线工具

# 标签搜索
cmmd -t docker       # 搜索docker标签的命令
tool -t 渗透测试     # 搜索渗透测试标签的离线工具
web -t 在线工具      # 搜索在线工具标签的网页工具

# 组合搜索
cmmd git -t 版本控制 -d 回退  # 组合多种搜索条件
tool scanner -t 信息收集     # 组合多种搜索条件
```

### 统计信息(所有工具通用)

```bash
# 显示标签统计
cmmd -T    # 显示命令库中所有标签及其统计信息
tool -T    # 显示离线工具库中所有标签及其统计信息
web -T     # 显示网页工具库中所有标签及其统计信息

# 显示工具名统计
cmmd -C    # 显示命令库中所有工具名及其统计信息
tool -C    # 显示离线工具库中所有工具名及其统计信息
```

### 快速粘贴

```bash
# 复制并粘贴指定序号的命令到终端
cmmd @1    # 粘贴序号为1的命令
tool @1    # 粘贴序号为1的工具命令
```

### 交互模式

```bash
# 进入交互模式(自动加载上次使用的文件)
cmmd       # 命令管理交互模式
tool       # 离线工具管理交互模式
web        # 网页工具管理交互模式
```

在交互模式中：
- 输入关键词实时搜索
- 使用Tab键/方向键导航结果
- 回车键选择命令（选择后会自动复制命令、退出交互模式并粘贴到终端中）
- q键退出交互模式
- 输入`@<序号>`快速粘贴命令

## 数据源格式

### XLSX命令库格式

命令库是一个Excel文件(.xlsx)，包含以下列：
- 序号
- 工具名
- 命令
- 标签
- 描述

示例：

| 序号 | 工具名 | 命令                         | 标签   | 描述         |
|------|--------|------------------------------|--------|--------------|
| 1    | sqlmap | python3 sqlmap.py -u xxx.com | 注入   | url注入      |
| 2    | git    | git --log                    | git    | 查看版本     |
| 3    | git    | git push origin master       | git,版本控制 | 推送到远程master分支 |

### JSON命令库格式

#### 离线工具JSON格式

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

#### 网页工具JSON格式

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

## 彩色输出规则

工具使用一致的颜色编码系统来提高可读性和用户体验：

| 元素 | 颜色 | 样式 | 示例 |
|------|------|------|------|
| 序号 | 暗灰色 | 普通 | `1.` |
| 工具名 | 蓝色 | 加粗 | **`git`** |
| 命令 | 绿色 | 普通 | `git push origin master` |
| 标签 | 黄色 | 方括号 | `[git][版本控制]` |
| 描述 | 白色 | 普通 | `推送到远程master分支` |
| 匹配关键词 | 黄色高亮背景 | 高亮 | git `push` origin master |
| 交互提示 | 青色 | 普通 | `输入关键词搜索...` |
| 错误信息 | 红色 | 加粗 | `文件不存在` |
| 成功消息 | 亮绿色 | 普通 | `已复制到剪贴板` |
| 统计信息 | 亮蓝色 | 普通 | `标签统计 (共 3 个)` |

## 系统要求

- 支持 Linux、macOS、Windows
- 不依赖特定系统组件

## 更新日志

### v0.2.1 (最新版本)
- 实现Grid布局系统，优化标签和工具统计显示
- 改进中英文混合文本的宽度计算和对齐显示
- 添加自定义工具路径配置指南
- 优化构建脚本，支持多平台交叉编译
- 优化帮助信息显示，右侧描述文本统一对齐

### v0.2.0
- 分离为三个专用工具(cmmd、tool、web)，优化各自功能
- 实现参数记忆功能，提高使用效率
- 添加对JSON数据源的支持，包括离线工具和网页工具
- 支持同时加载多个数据源
- 优化交互界面和搜索体验
- 改进错误处理和提示信息

完整的更新日志请查看 [CHANGELOG.md](docs/CHANGELOG.md)

## 贡献

欢迎提交问题和改进建议！

## 许可

[MIT](LICENSE) 

## 自定义工具路径配置

为了更好地管理个人编写的命令工具，可以配置专门的工具路径。以下是在不同系统中的配置方法：

### macOS 配置方法

1. 创建个人工具目录：
```bash
mkdir -p ~/my_tools
```

2. 配置环境变量（针对 zsh shell）：
```bash
echo 'export PATH=$PATH:$HOME/my_tools' >> ~/.zshrc
source ~/.zshrc
```

3. 将工具复制到个人目录：
```bash
cp your_tool ~/my_tools/
chmod +x ~/my_tools/*  # 确保工具有执行权限
```

### Windows 配置方法

1. 创建个人工具目录：
```cmd
mkdir %USERPROFILE%\my_tools
```

2. 添加到系统环境变量（两种方式）：

方式一：使用命令行（需要管理员权限）
```cmd
setx PATH "%PATH%;%USERPROFILE%\my_tools" /M
```

方式二：通过图形界面
1. 右键"此电脑" -> "属性" -> "高级系统设置" -> "环境变量"
2. 在"系统变量"部分找到 Path
3. 点击"编辑"
4. 点击"新建"
5. 输入 %USERPROFILE%\my_tools
6. 点击"确定"保存所有更改

3. 将工具复制到个人目录：
```cmd
copy your_tool.exe %USERPROFILE%\my_tools\
``` 