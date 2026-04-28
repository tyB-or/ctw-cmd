# CTW 工具管理系统

CTW 是一个用 Go 编写的跨平台命令与工具管理系统，面向终端命令、本地离线工具和网页工具三类场景。
它支持按关键词、标签、工具名和描述快速检索，并提供交互式选择、剪贴板复制和自动粘贴/打开能力。

## 主要特性

- 同一套代码支持 `cmmd`、`tool`、`web` 三种模式。
- 支持从 `xlsx` 或 `JSON` 加载数据。
- 支持关键词、标签、工具名、描述组合搜索。
- 支持交互式检索，输入即搜索，方向键或 `Tab` 选择，回车执行。
- 支持 `@<序号> 参数1 参数2 ...` 快速访问并填充模板参数。
- 支持自动复制到剪贴板，并尽可能自动粘贴到当前终端。
- `web` 模式会直接调用系统默认浏览器打开目标网址。

## 模式说明

这三个可执行文件实际上都来自同一个入口 `./cmd/cmd`，程序会根据启动时的文件名决定模式：

- `cmmd`：命令管理工具，读取 `xlsx` 命令库。
- `tool`：离线工具管理工具，读取离线工具 `JSON`。配置文件基于matu7
- `web`：网页工具管理工具，读取网页工具 `JSON`。配置文件基于matu7


## 构建

### macOS / Linux

```bash
./build.sh
```

只编译当前平台：

```bash
./build.sh --local-only
```

### Windows

```bat
build.bat
```

只编译当前平台：

```bat
build.bat --local-only
```

### 输出结果

- 当前平台的可执行文件会生成在项目根目录下：`cmmd`、`tool`、`web`。
- 多平台构建产物会输出到 `build/<goos>_<goarch>/`。

## 快速开始
必须:先指定加载的文件路径

### 命令管理工具 `cmmd`

```bash
cmmd -l commands.xlsx  【名称可随便，路径对即可】
```

### 离线工具管理工具 `tool`

```bash
tool -o offline_tools.json
```

### 网页工具管理工具 `web`

```bash
web -w web_tools.json
```

不带搜索条件直接启动时，会进入交互模式，并自动尝试加载上次使用的数据文件。

## 常用参数

所有模式都支持以下通用搜索参数：

- `<关键词...>`：按关键词搜索，支持空格或逗号分隔。多关键词使用and逻辑

- `-t, --tag <标签>`：按标签搜索，多个标签用逗号分隔。
- `-c, --command <工具名>`：按工具名搜索。
- `-d, --description <描述>`：按描述搜索。

- `-T, --showtags`：显示标签统计。
- `-C, --showcommands`：显示工具名统计。

- `-v, --version`：显示版本信息。
- `-h, --help`：显示帮助信息。

按模式区分的数据源参数：

- `cmmd` 使用 `-l, --load <xlsx文件路径>`
- `tool` 使用 `-o, --offline <JSON文件路径>`
- `web` 使用 `-w, --web <JSON文件路径>`

### 示例

```bash
cmmd docker
cmmd git,commit
cmmd git commit
cmmd -t 版本控制
cmmd -c git
cmmd -d 回退
cmmd @1
cmmd @10 a.txt b.txt
```

```bash
tool scanner
tool -t 渗透测试
tool -c Burp
tool -d 代理
tool @3
tool @8 http://example.com
```

```bash
web api
web -t 在线工具
web -c Notion
web -d 文档
web @2
web @5 docs
```

## 搜索规则

- 关键词搜索会同时匹配命令内容、工具名、描述和标签。
- 多个关键词默认是 `AND` 关系，也就是每个关键词都需要在某个字段中命中。
- 标签搜索支持模糊匹配，多个标签之间也是 `AND` 关系。
- 工具名搜索支持模糊匹配，多个工具名之间是 `OR` 关系。
- 描述搜索是子串匹配。

如果搜索结果只有 1 条，并且是通过关键词参数触发，程序会自动执行对应动作：

- `cmmd`：复制命令并尝试粘贴到终端。
- `tool`：根据工具配置拼接 `cd` / 启动命令后复制并尝试粘贴。
- `web`：在浏览器中打开对应网址。

## 模板参数

命令内容、离线工具命令和网页工具网址都支持英文 `{}` 或 `{提示文字}` 占位符。

当你使用 `@<序号> 参数1 参数2 ...` 时，程序会按顺序把参数依次填入命令中的占位符。
占位符内可以写提示文字（如默认值、说明），实际执行时会被参数完整替换，无论括号内是否有内容。

```bash
# 命令模板（空占位符）
touch {}.txt

# 输入
cmmd @10 test

# 实际执行
touch test.txt
```

```bash
# 命令模板（带提示文字的占位符）
ls {./测试}

# 输入
cmmd @7 ./

# 实际执行
ls ./
```

```bash
# 命令模板（混合使用）
mv {源文件} {目标路径}

# 输入
cmmd @12 a.txt backup/

# 实际执行
mv a.txt backup/
```

规则如下：

- `{}` 和 `{任意内容}` 都是有效的占位符，均会被参数替换。
- 占位符按从左到右依次替换。
- 参数数量少于占位符数量时，剩余占位符保持原样（包括括号内的提示文字）。
- 参数数量多于占位符数量时，多余参数会被忽略。

## 交互模式

直接运行对应命令且不带搜索条件时会进入交互模式。

```bash
cmmd
tool
web
```

交互模式支持：

- 实时输入搜索。
- `Tab`、方向键上下选择结果。
- 回车执行当前选中项。
- 输入 `q` 后回车退出。
- 输入 `@<序号> 参数1 参数2 ...` 直接执行指定条目并填充模板参数。

## 数据格式

### 1. XLSX 命令库

`cmmd` 读取 Excel 文件的第一个工作表，表头必须包含以下字段，顺序不限：

- `序号`
- `工具名`
- `命令`
- `标签`
- `描述`

标签列支持用英文逗号分隔多个标签。

示例：

| 序号 | 工具名 | 命令 | 标签 | 描述 |
| --- | --- | --- | --- | --- |
| 1 | git | git status | 版本控制,Git | 查看仓库状态 |
| 2 | curl | curl -I https://example.com | 网络,HTTP | 查看响应头 |

### 2. 离线工具 JSON

`tool` 读取的格式如下：

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

执行逻辑：

- `command` 存在时，优先使用 `command`。
- 如果同时存在 `path`，实际执行会拼成 `cd "path" && command`。
- 如果没有 `command`，但有 `path`，则默认执行 `cd "path"`。
- 如果没有 `command` 但有 `url`，会使用 `open <url>` 作为命令内容。
- `command` 或 `url` 中如果包含 `{}`，会按 `@<序号> 参数...` 的顺序填充。

### 3. 网页工具 JSON

`web` 支持新旧两种格式，新格式如下：

```json
{
  "tools": [
    {
      "id": "unique-id",
      "name": "网页工具名称",
      "url": "https://example.com",
      "icon": "optional",
      "description": "网页工具描述",
      "category": "分类",
      "tags": ["标签1", "标签2"],
      "usage_count": 0,
      "created_at": "2025-01-01",
      "updated_at": "2025-01-02",
      "last_used_at": "2025-01-03",
      "note_file": "optional"
    }
  ]
}
```

旧版 `notes` 格式也兼容，程序会自动转换成可搜索的命令记录。

执行逻辑：

- `web` 会把网址转换为 `open <url>`。
- 在 macOS、Windows、Linux 上会调用系统默认浏览器打开网址。
- `url` 中如果包含 `{}`，会按 `@<序号> 参数...` 的顺序填充。

## 配置与记忆

程序会在用户主目录下创建一个统一配置目录：

- `~/.cmd`

会保存最近使用过的数据文件路径，分别对应：

- `last_xlsx`
- `last_offline_tools`
- `last_web_tools`

这意味着下次直接运行对应工具时，程序会优先尝试自动加载上次使用的文件。
你每次显式传入 `-l`、`-o` 或 `-w` 时，程序也会同步更新最近使用的路径。

## 剪贴板与自动粘贴

程序会先把内容复制到系统剪贴板，然后再尝试自动粘贴。

平台行为如下：

- macOS：通过 `osascript` 和系统事件模拟 `Command+V`。
- Linux：优先使用 `xdotool`，失败后回退到 `xclip`。
- Windows：通过 PowerShell 的 `SendKeys("^v")` 模拟粘贴。

如果自动粘贴失败，程序仍会保留剪贴板内容，并提示你手动粘贴。

### 运行依赖

- macOS：需要终端具有辅助功能权限。
- Linux：建议安装 `xdotool` 或 `xclip`。
- Windows：需要可用的 PowerShell 环境。

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

## 常见问题

### 为什么我不带参数运行时会进入交互模式？

这是设计行为。程序在没有搜索条件时会进入交互模式，方便边输入边查找。

### 为什么 `cmmd` / `tool` / `web` 的参数不一样？

因为它们虽然来自同一套代码，但面向的数据源不同：

- `cmmd` 面向 `xlsx`
- `tool` 面向离线工具 `JSON`
- `web` 面向网页工具 `JSON`

### 为什么有时自动粘贴失败？

通常是终端权限、桌面环境或缺少辅助工具导致。此时程序会保留已复制的内容，你可以直接手动粘贴。

### 为什么统计显示会自动换列？

统计页会根据终端宽度自动计算列数，并尽量处理中英文混排的对齐问题。

## 项目结构

- `cmd/cmd/main.go`：程序入口和命令行逻辑。
- `pkg/search`：搜索规则。
- `pkg/xlsreader`：Excel 数据读取。
- `pkg/jsonreader`：JSON 数据读取。
- `pkg/ui`：交互模式。
- `pkg/utils`：剪贴板、粘贴和输出格式化。
- `build.sh`、`build.bat`：构建脚本。


