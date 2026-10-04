# release-note

## Version=1.1.0

1. fix(func): `__log` 改为两路分离写入——彩色输出到终端、纯文本追加写日志各自独立, 修复原 `{...} | tee -a` 分组块把 ANSI 转义符一并写进 `log_file` 的问题
2. fix(func): `str_strip` 改为 perl 优先, 覆盖全部 Unicode 空白(NBSP U+00A0、全角空格 U+3000 等), 无 perl 时退化为新增纯 bash 实现 `str_strip_alternative`(原 sed 实现不支持 `\u` 转义, 会误剥首尾 `u`/`3`/`0` 字符)
3. fix(func): `backup_dir_with_rotation` 备份时间戳 pattern 由 12 位修正为 14 位(%Y%m%d%H%M%S), 修复轮换清理永远匹配不到旧备份的问题
4. feat(func): 新增 `array_to_json`、`associate_array_to_json`、`backup_dir_with_rotation`、`is_element_in_array`、`str_strip_alternative`
5. change(func): 移除导出变量 `SETCOLOR_*`, 颜色处理内化到 `__log`; 直接引用这些变量的脚本应改用 LOG* 函数
6. docs: `func` 文件头部加 MIT SPDX 标识与版权声明
7. docs: README.md / README.zh-CN.md 同步本版函数说明——新增数组与 JSON、目录备份章节与函数参考条目, 日志系统改为两路描述(终端彩色、日志文件纯文本), 删除 `SETCOLOR_*` 颜色代码节, 修正故障排除中"管道默认禁用颜色"的错误说法, 系统要求补 `jq`/`perl` 可选项; `README.cn.md` 更名 `README.zh-CN.md`

## Version=1.0.0

1. feat(func): 日志系统——`__log` + LOGDEBUG/LOGINFO/LOGSUCCESS/LOGWARNING/LOGERROR 五级, 时间戳与调用行号自动记录, stdout/stderr 分流与 `log_file` 文件写入双路
2. feat(func): 配置管理——`get_ini_value` INI 解析(节名大小写不敏感), `get_var` 变量加载(支持环境变量覆盖)
3. feat(func): 字符串处理 `str_strip`(首尾空白剥离); 版本比较 `version_gt/lt/eq/ge/le`(sort -V 自然排序, 支持复杂版本串如 1.1.1q)
4. feat(func): 进程管理——`proc_killer`(信号与宽限时间可配, SIGTERM 自动升级 SIGKILL), `tmout` 轻量超时; 时间工具 `_conv2sec`(s/m/h/d 单位与小数值如 0.5h)
5. feat(func): 网络工具 `is_ip_in_network`(CIDR 与子网掩码两种格式, 纯 awk 实现保证可移植); 系统检测 `detect_system_info`(发行版/包管理器/服务管理器/VM 与物理机识别, JSON 输出依赖 jq)
6. feat(func): 附加工具 `debug`(xtrace 模式, 移除 eval 防注入)、`gracefully_abort`(用户中断处理)、`convert_syslog_timestamp`(syslog 时间转换, 示例函数)
7. fix: `_conv2sec` 单位提取的未初始化变量修复; `debug` 由 eval 改为间接参数展开, 消除代码注入面
8. 兼容: Bash 4.0+(关联数组依赖), 实测 4.0/4.3/4.4/5.0/5.1; 覆盖 Debian/Ubuntu 系、RHEL/CentOS/Rocky/AlmaLinux 系、Arch、Alpine、openSUSE、openEuler/HCE
9. 已知限制: `detect_system_info` 依赖 jq; `convert_syslog_timestamp` 为示例需按需定制, 跨年日志处理需额外开发; 部分终端模拟器彩色输出可能异常
