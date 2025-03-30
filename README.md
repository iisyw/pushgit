# PushGit

一个用 Rust 编写的 Git 提交和日志管理工具，可以帮助你轻松提交代码到 Git 仓库并自动记录开发日志。

## 功能

- 提交代码到 Git 仓库
- 自动记录每日开发日志
- 支持 CI 构建控制（可选添加[skip ci]标记）
- 智能处理日志文件日期变更
- 命令行交互式操作
- 支持多行提交标注（标题+正文列表）
- 日志文件完全符合 Markdown 格式标准
- 支持编辑提交标注（可在确认前修改已输入的内容）
- 优雅的命令行界面，带有彩色输出和自适应分隔线

## 安装

### 方法一：使用预编译可执行文件（Windows）

本项目根目录提供了预编译的 Windows 可执行文件`pushgit.exe`，适合没有安装 Rust 环境或不想编译的用户：

1. 下载本仓库，或者只下载`pushgit.exe`和`push.bat`文件
2. 可以直接使用`pushgit.exe`，也可以通过提供的`push.bat`脚本使用

这种方式无需安装 Rust 环境，适合快速尝试或在多个项目中使用。

### 方法二：从源码编译

确保你已安装 Rust 环境，然后克隆此仓库并编译：

```bash
git clone https://github.com/iisyw/PushGit.git
cd pushgit
cargo build --release
```

编译后的可执行文件在`target/release`目录中。

### 方法三：使用 Cargo 安装

如果你已经安装了 Rust 和 Cargo，可以直接使用以下命令安装：

```bash
cargo install --git https://github.com/iisyw/PushGit.git
```

这将自动下载、编译并安装 PushGit 到你的系统中。

## 快速部署到其他项目

本项目提供了`push.bat`脚本，方便在任何 Git 项目中快速使用 PushGit 工具：

1. 将`pushgit.exe`和`push.bat`复制到目标项目目录中
2. 或者，将它们放在任意位置，然后编辑`push.bat`文件，修改其中的路径指向`pushgit.exe`的实际位置

例如，修改`push.bat`：

```batch
@echo off
:: 设置命令行使用 UTF-8 编码
chcp 65001 >nul

:: 执行PushGit工具并传递所有参数（使用绝对路径）
D:\tools\pushgit.exe %*

:: 脚本结束
exit /b %errorlevel%
```

这样，您就可以在任何项目中使用`push.bat`来调用 PushGit，而无需在每个项目中都复制可执行文件。

### 脚本使用示例

有了`push.bat`脚本后，您可以像使用`pushgit`命令一样使用它：

```bash
# 启动交互式提交过程（可输入标题和正文）
push.bat

# 指定提交消息并推送（提交消息为标题，无正文）
push.bat "修复登录bug" -p

# 指定远程仓库和CI选项
push.bat "更新依赖" -p -r origin -c
```

脚本会自动设置 UTF-8 编码，确保中文显示正常，并将所有参数传递给 PushGit 工具，让您在任何项目中都能享受到一致的 Git 提交体验。

注意：当通过命令行参数提供提交消息时，该消息仅作为标题使用，不会提示输入正文内容。如果需要多行提交标注（标题+正文），请使用不带参数的交互式模式。

## 使用前提条件

在使用 PushGit 之前，请确保您的项目已满足以下条件：

1. **Git 仓库已初始化**：项目目录必须是一个有效的 Git 仓库（已执行过`git init`）
2. **至少有一次提交记录**：推荐先手动执行一次`git commit`，确保主分支已创建
3. **已配置远程仓库**：如果需要推送功能，必须已设置远程仓库（如`git remote add github 仓库URL`）

这些条件非常重要，因为：

- 如果没有设置远程仓库，推送操作会失败
- 如果是首次提交，可能会因为主分支（master/main）不存在而导致推送失败
- 推送前必须确保本地分支与远程分支有正确的跟踪关系

建议首次使用前，先手动完成至少一次 Git 提交和推送操作，确保基本 Git 配置正确。

## 使用方法

### 基本用法

```bash
pushgit
```

这将启动完全交互式提示，依次询问：

- 提交标题和正文内容
  - 首先输入标题（如果不输入标题，将使用默认值"Normal Update"）
  - 然后逐行输入正文内容（每行将自动添加`-`前缀）
  - 输入空行结束正文输入
- 提供提交标注预览并允许编辑已输入的内容
  - 可以修改标题
  - 可以修改任意正文行
  - 可以添加新的正文行或删除已有行
- 是否需要推送到远程仓库
- 远程仓库名称
- 是否需要进行 CI 构建

即使您选择使用默认标题"Normal Update"（不输入标题直接回车），仍然可以继续输入正文内容。这使得创建格式规范的提交信息变得更加灵活。

您也可以直接在命令行指定提交标注：

```bash
pushgit "提交消息"
```

**注意**：通过命令行参数提供的提交消息将直接作为提交标题使用，并跳过正文输入步骤。如果需要包含详细的正文内容，建议使用交互模式。

### 多行提交标注格式

程序支持创建如下格式的提交标注：

```md
标题内容

- 正文第一行内容
- 正文第二行内容
- 正文第三行内容
```

在交互模式下，你只需要输入每行的实际内容，工具会自动添加`-`前缀和必要的换行。

### 编辑提交标注

在输入完成提交标注后，程序会显示预览并询问是否需要编辑。如果选择编辑，将显示一个菜单：

```md
当前提交标注内容:
标题: 修复登录和验证功能
正文:

- 修复登录接口错误
- 优化验证流程

请选择要编辑的部分: 0. 返回不修改

1. 编辑标题
2. 编辑正文第 1 行
3. 编辑正文第 2 行
4. 添加新的正文行
5. 删除最后一行正文
```

通过输入对应的数字，你可以：

- 修改标题
- 修改任意一行正文
- 添加新的正文行
- 删除最后一行正文

这样就可以在提交前完善你的提交标注，无需重新运行整个流程。

### 命令行选项

```bash
# 提交并推送到默认远程仓库(github)
pushgit "提交消息" -p

# 提交并推送到指定远程仓库
pushgit "提交消息" -p -r origin

# 启用CI构建
pushgit "提交消息" -p -c

# 禁用CI构建(添加[skip ci]标记)
pushgit "提交消息" -p -n
# 或者使用别名
pushgit "提交消息" -p --nc
```

当直接在命令行中提供提交消息时，该消息将作为提交标注的标题使用，程序会跳过正文输入环节。这对于快速提交非常方便，适合那些不需要详细正文的简单更改。

例如：

```bash
# 将"修复样式问题"作为提交标题，不询问正文内容
pushgit "修复样式问题"
```

如果需要输入多行提交标注（包含标题和正文），建议使用交互式模式（不提供命令行参数），这样可以分别输入标题和正文内容。

### 命令行参数

- `提交消息`: 提交的说明文字，如不提供则会启动交互式多行输入
- `-p, --push`: 是否推送到远程仓库
- `-r, --remote <REMOTE>`: 远程仓库名称，默认为"github"
- `-c, --ci`: 启用 CI 构建
- `-n, --no-ci, --nc`: 禁用 CI 构建，添加[skip ci]标记

## 日志文件

该工具会创建和维护两个 Markdown 格式的日志文件：

- `TodayDevelopment.md`: 记录当天的开发日志
- `Development.md`: 长期开发日志，当日期变更时会自动合并今日日志

日志格式完全符合 Markdown 标准，支持多行提交信息的正确格式化：

```markdown
## 2024/05/10

1. 修改登录和验证多因素认证接口，优化缓存管理
   - 修改登录接口，增加菜单列表缓存
   - 修改验证多因素认证接口，增加菜单列表缓存
   - 去除刷新访问令牌接口的权限标识缓存
2. 修复其他功能问题
```

这种格式可以直接在支持 Markdown 的查看器中正确显示，保持列表的层级结构。

## 依赖项

本项目使用以下主要依赖：

- `clap`: 用于命令行参数解析
- `chrono`: 用于日期和时间处理
- `git2`: 用于 Git 操作
- `anyhow`: 用于错误处理
- `dialoguer`: 用于交互式命令行对话
- `colored`: 用于彩色输出
- `rustyline`: 用于改进的命令行输入体验

## 许可证

本项目采用 MIT 许可证。详情请参阅 [LICENSE](LICENSE) 文件。

MIT 许可证是一个宽松的软件许可证，它基本上允许任何人以任何方式使用您的代码，前提是他们在自己的项目中保留许可证声明和版权声明。这意味着：

- 可以自由使用、复制、修改、合并、发布、分发、再许可和/或出售本软件的副本
- 必须在所有副本或重要部分中包含上述版权声明和本许可声明
- 软件按"原样"提供，没有任何形式的明示或暗示的保证
