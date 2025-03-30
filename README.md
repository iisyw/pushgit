# PushGit

一个用Rust编写的Git提交和日志管理工具，可以帮助你轻松提交代码到Git仓库并自动记录开发日志。

## 功能

- 提交代码到Git仓库
- 自动记录每日开发日志
- 支持CI构建控制（可选添加[skip ci]标记）
- 智能处理日志文件日期变更
- 命令行交互式操作
- 支持多行提交标注（标题+正文列表）
- 日志文件完全符合Markdown格式标准
- 支持编辑提交标注（可在确认前修改已输入的内容）

## 安装

确保你已安装Rust环境，然后克隆此仓库并编译：

```bash
git clone https://github.com/yourusername/pushgit.git
cd pushgit
cargo build --release
```

编译后的可执行文件在`target/release`目录中。

## 使用方法

### 基本用法

```bash
pushgit
```

这将启动完全交互式提示，依次询问：

- 提交标题和正文内容
  - 首先输入标题
  - 然后逐行输入正文内容（每行将自动添加`-`前缀）
  - 输入空行结束正文输入
- 提供提交标注预览并允许编辑已输入的内容
  - 可以修改标题
  - 可以修改任意正文行
  - 可以添加新的正文行或删除已有行
- 是否需要推送到远程仓库
- 远程仓库名称
- 是否需要进行CI构建

也可以直接指定提交标注：

```bash
pushgit "提交消息"
```

### 多行提交标注格式

程序支持创建如下格式的提交标注：

```
标题内容
- 正文第一行内容
- 正文第二行内容
- 正文第三行内容
```

在交互模式下，你只需要输入每行的实际内容，工具会自动添加`-`前缀和必要的换行。

### 编辑提交标注

在输入完成提交标注后，程序会显示预览并询问是否需要编辑。如果选择编辑，将显示一个菜单：

```
当前提交标注内容:
标题: 修复登录和验证功能
正文:
  1. 修复登录接口错误
  2. 优化验证流程

请选择要编辑的部分:
  0. 返回不修改
  1. 编辑标题
  2. 编辑正文第1行
  3. 编辑正文第2行
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

### 命令行参数

- `提交消息`: 提交的说明文字，如不提供则会启动交互式多行输入
- `-p, --push`: 是否推送到远程仓库
- `-r, --remote <REMOTE>`: 远程仓库名称，默认为"github"
- `-c, --ci`: 启用CI构建
- `-n, --no-ci, --nc`: 禁用CI构建，添加[skip ci]标记

## 日志文件

该工具会创建和维护两个Markdown格式的日志文件：

- `TodayDevelopment.md`: 记录当天的开发日志
- `Development.md`: 长期开发日志，当日期变更时会自动合并今日日志

日志格式完全符合Markdown标准，支持多行提交信息的正确格式化：

```markdown
## 2024/05/10

1. 修改登录和验证多因素认证接口，优化缓存管理
   - 修改登录接口，增加菜单列表缓存
   - 修改验证多因素认证接口，增加菜单列表缓存
   - 去除刷新访问令牌接口的权限标识缓存

2. 修复其他功能问题
```

这种格式可以直接在支持Markdown的查看器中正确显示，保持列表的层级结构。

## 许可证

MIT
