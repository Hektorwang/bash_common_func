# Bash 函数库

一个全面的 Bash 可复用函数集合，提供日志记录、配置管理、进程控制和系统检测功能。

## 系统要求

- Bash 4.0 或更高版本
- 标准 Unix 工具：`awk`、`sed`、`date`、`sort`
- 可选：`jq`（`detect_system_info` 函数需要）

## 功能特性

### 日志系统
- 彩色分级日志（DEBUG、INFO、SUCCESS、WARNING、ERROR）
- 自动记录时间戳和行号
- 支持文件和控制台输出
- 函数：`LOGDEBUG`、`LOGINFO`、`LOGSUCCESS`、`LOGWARNING`、`LOGERROR`

### 配置管理
- INI 文件解析，支持节（section）和键（key）
- 不区分大小写的节匹配
- 从配置文件自动加载变量
- 函数：`get_ini_value`、`get_var`

### 字符串处理
- 去除前导和尾随空白字符、制表符、换行符
- 支持全角空格（CJK）
- 函数：`str_strip`

### 版本比较
- 语义化版本比较（大于、小于、等于、大于等于、小于等于）
- 支持自然排序
- 函数：`version_gt`、`version_lt`、`version_eq`、`version_ge`、`version_le`

### 进程管理
- 优雅终止进程，支持超时
- 先发送 SIGTERM，超时后发送 SIGKILL
- 支持自定义信号和等待时间
- 函数：`proc_killer`、`tmout`

### 时间工具
- 将时间字符串转换为秒（支持 s/m/h/d 单位）
- 支持小数值（如 0.5h）
- 超时命令实现
- 函数：`_conv2sec`、`tmout`

### 网络工具
- 检查 IP 地址是否在指定网段内
- 支持 CIDR 表示法和子网掩码
- 函数：`is_ip_in_network`

### 系统检测
- 检测 Linux 发行版和版本
- 识别包管理器（apt、yum、dnf 等）
- 检测服务管理器（systemd、openrc、runit）
- 区分虚拟机和物理机
- 函数：`detect_system_info`

## 使用方法

### 基本设置

```bash
#!/usr/bin/env bash
source /path/to/func

# 可选：设置日志文件
export log_file="/var/log/myscript.log"
```

### 日志示例

```bash
LOGINFO "启动应用程序"
LOGSUCCESS "操作成功完成"
LOGWARNING "未找到配置文件，使用默认配置"
LOGERROR "连接数据库失败"
LOGDEBUG "变量值：$my_var"
```

### 配置管理

```bash
# config.ini:
# [database]
# host = localhost
# port = 3306

db_host=$(get_ini_value "config.ini" "database" "host")
db_port=$(get_ini_value "config.ini" "database" "port")

# 或使用 get_var 先检查环境变量，再读取配置
get_var "config.ini" "database" "db_host"
echo "数据库主机：$db_host"
```

### 版本比较

```bash
if version_gt "2.1.0" "2.0.5"; then
    echo "版本 2.1.0 大于 2.0.5"
fi

if version_ge "$current_version" "$required_version"; then
    echo "满足版本要求"
fi
```

### 进程控制

```bash
# 优雅终止进程，30秒超时
proc_killer 12345 "TERM" "30s"

# 带超时运行命令
if tmout 5s curl https://example.com; then
    echo "请求在 5 秒内完成"
else
    echo "请求超时"
fi
```

### 网络工具

```bash
# 检查 IP 是否在网段内（CIDR 表示法）
if is_ip_in_network "192.168.1.100" "192.168.1.0/24"; then
    echo "IP 在网段内"
fi

# 使用子网掩码检查
if is_ip_in_network "10.0.0.50" "10.0.0.0" "255.255.255.0"; then
    echo "IP 在网段内"
fi
```

### 系统检测

```bash
# 需要安装 jq
system_info=$(detect_system_info)
echo "$system_info" | jq -r '.os_distribution'
echo "$system_info" | jq -r '.machine_type'  # "vm" 或 "pm"
```

## 函数参考

| 函数 | 描述 | 返回值 |
|------|------|--------|
| `LOGDEBUG/INFO/SUCCESS/WARNING/ERROR` | 分级日志记录 | 始终为 0 |
| `str_strip` | 去除前导和尾随空白 | - |
| `get_ini_value` | 从 INI 文件获取值 | 0=成功, 1=文件错误, 2=键未找到, 99=参数缺失 |
| `get_var` | 从环境或配置加载变量 | 0=成功, 3=未找到 |
| `version_gt/lt/eq/ge/le` | 版本比较 | 0=真, 1=假 |
| `debug` | 启用 xtrace 调试模式 | 0 |
| `gracefully_abort` | 处理用户中断 | 退出码 1 |
| `proc_killer` | 优雅终止进程 | 0=成功, 1=错误 |
| `tmout` | 带超时运行命令 | 124=超时, 否则为命令退出码 |
| `is_ip_in_network` | 检查 IP 是否在网段内 | 0=是, 1=否, 2=参数无效 |
| `detect_system_info` | 获取系统信息 | 0=成功, 1=不满足要求 |

## 环境变量

- `log_file`：日志文件路径（默认：`/dev/null`）
- `DEBUG`：设置为 `true` 启用调试模式
- `VM_PRODUCT_NAME_PATTERNS`：自定义虚拟机检测正则表达式

## 颜色代码

函数库导出以下颜色控制变量：
- `SETCOLOR_DEBUG`：白色
- `SETCOLOR_NORMAL`：默认
- `SETCOLOR_SUCCESS`：绿色
- `SETCOLOR_WARNING`：黄色
- `SETCOLOR_ERROR`：红色

## 最佳实践

1. 始终在脚本开头引入此函数库
2. 设置 `log_file` 变量以持久化日志
3. 检查关键操作的返回值
4. 在脚本中使用 `set -euo pipefail` 以获得更好的错误处理
5. 导出需要在子 shell 中使用的变量

## 故障排除

**问：日志中不显示颜色**
- 确保终端支持 ANSI 颜色代码
- 检查输出是否被管道传输（管道中默认禁用颜色）

**问：`detect_system_info` 失败**
- 安装 `jq`：`apt install jq` 或 `yum install jq`
- 确保 Bash 版本为 4.0 或更高：`bash --version`

**问：版本比较不工作**
- 验证 `sort` 命令支持 `-V` 参数
- 使用 `sort --version` 检查

## 许可证

MIT © [Hektorwang]

## 贡献

欢迎贡献！请确保：
- 函数有良好的文档
- 实现了错误处理
- 代码遵循现有风格约定
- 保持 Bash 4.0+ 兼容性

## 更新日志

查看 [release-note.md](release-note.md) 了解版本历史和变更。
