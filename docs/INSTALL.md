# 终端命令与工具管理工具安装指南

## 系统要求

- 支持 Linux、macOS、Windows
- Go 1.16+ (如需从源码编译)

## 安装方法

### 方法一：使用预编译版本

从[发布页面](https://github.com/yourusername/cmd/releases)下载适合你系统的预编译版本：

- **Linux**: cmmd-linux, tool-linux, web-linux
- **macOS**: cmmd-darwin, tool-darwin, web-darwin
- **Windows**: cmmd.exe, tool.exe, web.exe

### 方法二：从源码编译

1. 克隆代码库
```bash
git clone https://github.com/yourusername/cmd.git
cd cmd
```

2. 编译所有工具
```bash
# Linux/macOS
./build.sh             # 编译所有支持的平台
./build.sh --local-only  # 仅编译当前平台

# Windows
build.bat              # 编译所有支持的平台
build.bat --local-only   # 仅编译当前平台
```

编译后的可执行文件将保存在以下位置：
- 当前平台的可执行文件：项目根目录下的`cmmd`、`tool`和`web`
- 多平台编译的可执行文件：`build`目录下按平台分类，如：
  - `build/darwin_amd64/` (macOS Intel)
  - `build/darwin_arm64/` (macOS Apple Silicon)
  - `build/linux_amd64/` (Linux x86_64)
  - `build/linux_arm64/` (Linux ARM64)
  - `build/windows_amd64/` (Windows x64)

3. 安装到系统路径
```bash
# Linux/macOS
sudo cp cmmd tool web /usr/local/bin/

# Windows
# 将编译好的可执行文件放到系统PATH中的目录
```

## 自定义工具路径配置

为了更好地管理个人编写的命令工具，可以配置专门的工具路径：

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
cp cmmd tool web ~/my_tools/
chmod +x ~/my_tools/*  # 确保工具有执行权限
```

### Linux 配置方法

1. 创建个人工具目录：
```bash
mkdir -p ~/my_tools
```

2. 配置环境变量：
```bash
# 对于 bash
echo 'export PATH=$PATH:$HOME/my_tools' >> ~/.bashrc
source ~/.bashrc

# 对于 zsh
echo 'export PATH=$PATH:$HOME/my_tools' >> ~/.zshrc
source ~/.zshrc
```

3. 将工具复制到个人目录：
```bash
cp cmmd tool web ~/my_tools/
chmod +x ~/my_tools/*
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
copy cmmd.exe %USERPROFILE%\my_tools\
copy tool.exe %USERPROFILE%\my_tools\
copy web.exe %USERPROFILE%\my_tools\
```

## 配置

工具会在用户主目录下创建配置文件夹，存储最近使用的文件路径：

- **Linux/macOS**: ~/.cmmd, ~/.tool, ~/.web
- **Windows**: %USERPROFILE%\.cmmd, %USERPROFILE%\.tool, %USERPROFILE%\.web

## 依赖项

### Linux 依赖

对于自动粘贴功能，Linux 系统需要安装以下工具之一：

```bash
# Ubuntu/Debian
sudo apt-get install xdotool
# 或
sudo apt-get install xclip

# CentOS/RHEL
sudo yum install xdotool
# 或
sudo yum install xclip

# Arch Linux
sudo pacman -S xdotool
# 或
sudo pacman -S xclip
```

### macOS 依赖

macOS 用户需要允许终端访问辅助功能以支持自动粘贴：
1. 系统偏好设置 -> 安全性与隐私 -> 隐私 -> 辅助功能
2. 添加并勾选你使用的终端应用（如 Terminal.app 或 iTerm）

### Windows 依赖

Windows 用户可能需要允许 PowerShell 执行脚本：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## 故障排除

### 常见问题

1. **无法粘贴命令到终端**
   - 检查是否已安装必要的依赖
   - 确认终端应用有必要的权限
   - 尝试手动粘贴（Ctrl+V 或 Command+V）

2. **找不到命令库文件**
   - 检查文件路径是否正确
   - 使用绝对路径避免问题

3. **命令ID不存在**
   - 使用 `-T` 或 `-C` 参数查看可用的命令和ID

4. **显示对齐问题**
   - 确保终端窗口足够宽
   - 尝试调整终端字体大小 