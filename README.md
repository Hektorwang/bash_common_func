# Bash Function Library

A comprehensive collection of reusable Bash functions for logging, configuration management, string processing, array/JSON conversion, process control, directory backup, and system detection.

## Requirements

- Bash 4.0 or higher
- Standard Unix utilities: `awk`, `sed`, `date`, `sort`
- Optional: `jq` (required for `detect_system_info`, `array_to_json`, `associate_array_to_json`)
- Optional: `perl` (required for `str_strip` full Unicode whitespace coverage; falls back to a pure-bash implementation when absent)

## Features

### Logging System
- Color-coded log levels (DEBUG, INFO, SUCCESS, WARNING, ERROR)
- DEBUG/WARNING/ERROR go to stderr, INFO/SUCCESS go to stdout
- Colored terminal output and plain-text log-file writing are two separate paths: `log_file` never contains ANSI escape codes
- Automatic timestamp and caller line number tracking
- Functions: `LOGDEBUG`, `LOGINFO`, `LOGSUCCESS`, `LOGWARNING`, `LOGERROR`

### Configuration Management
- INI file parsing with section and key support
- Case-insensitive section matching
- Automatic variable loading from config files
- Functions: `get_ini_value`, `get_var`

### String Processing
- Strip leading/trailing whitespace
- `str_strip` prefers `perl` and covers the full Unicode whitespace set (NBSP U+00A0, ideographic space U+3000, etc.)
- Automatically falls back to a pure-bash implementation when `perl` is unavailable
- Functions: `str_strip`, `str_strip_alternative`

### Array & JSON Utilities
- Convert a regular array to a JSON array string
- Convert an associative array to a JSON object string (key/value pairs)
- Check whether an element exists in an array
- Functions: `array_to_json`, `associate_array_to_json`, `is_element_in_array` (the first two require `jq`)

### Directory Backup
- Back up a target directory as `<dir>.bak_<YYYYMMDDHHMMSS>`
- Automatic rotation cleanup with a configurable maximum number of backups (default 5)
- Function: `backup_dir_with_rotation`

### Version Comparison
- Semantic version comparison (gt, lt, eq, ge, le)
- Natural sorting support
- Functions: `version_gt`, `version_lt`, `version_eq`, `version_ge`, `version_le`

### Process Management
- Graceful process termination with timeout
- SIGTERM followed by SIGKILL if needed
- Custom signal and grace period support
- Functions: `proc_killer`, `tmout`

### Time Utilities
- Convert time strings to seconds (supports s/m/h/d units)
- Decimal value support (e.g., 0.5h)
- Timeout command implementation
- Functions: `_conv2sec`, `tmout`

### Network Utilities
- Check if IP address is within a network range
- Support for CIDR notation and subnet masks
- Function: `is_ip_in_network`

### System Detection
- Detect Linux distribution and version
- Identify package manager (apt, yum, dnf, etc.)
- Detect service manager (systemd, openrc, runit)
- Distinguish between VM and physical machine
- Function: `detect_system_info`

## Usage

### Basic Setup

```bash
#!/usr/bin/env bash
source /path/to/func

# Optional: Set log file (always plain text)
export log_file="/var/log/myscript.log"
```

### Logging Examples

```bash
LOGINFO "Starting application"
LOGSUCCESS "Operation completed successfully"
LOGWARNING "Configuration file not found, using defaults"
LOGERROR "Failed to connect to database"
LOGDEBUG "Variable value: $my_var"
```

### Configuration Management

```bash
# config.ini:
# [database]
# host = localhost
# port = 3306

db_host=$(get_ini_value "config.ini" "database" "host")
db_port=$(get_ini_value "config.ini" "database" "port")

# Or use get_var to check environment first, then config
get_var "config.ini" "database" "db_host"
echo "Database host: $db_host"
```

### Array & JSON

```bash
# Regular array → JSON array
arr=("value1" "value2" "value3")
array_to_json "${arr[@]}"          # ["value1","value2","value3"]

# Associative array → JSON object
declare -A assoc=([key1]="value1" [key2]="value2")
associate_array_to_json "${!assoc[@]}" "${assoc[@]}"
# {"key1":"value1","key2":"value2"}

# Check element membership
fruits=("apple" "banana")
if is_element_in_array "apple" "${fruits[@]}"; then
    echo "apple is in the list"
fi
```

### Directory Backup

```bash
# Back up /etc/myapp, keep at most 5 copies (default is 5)
backup_dir_with_rotation "/etc/myapp" 5
# Produces /etc/myapp.bak_20261005093000
```

### Version Comparison

```bash
if version_gt "2.1.0" "2.0.5"; then
    echo "Version 2.1.0 is greater than 2.0.5"
fi

if version_ge "$current_version" "$required_version"; then
    echo "Version requirement satisfied"
fi
```

### Process Control

```bash
# Kill process gracefully with 30s timeout
proc_killer 12345 "TERM" "30s"

# Run command with timeout
if tmout 5s curl https://example.com; then
    echo "Request completed within 5 seconds"
else
    echo "Request timed out"
fi
```

### Network Utilities

```bash
# Check if IP is in network (CIDR notation)
if is_ip_in_network "192.168.1.100" "192.168.1.0/24"; then
    echo "IP is in the network"
fi

# Check with subnet mask
if is_ip_in_network "10.0.0.50" "10.0.0.0" "255.255.255.0"; then
    echo "IP is in the network"
fi
```

### System Detection

```bash
# Requires jq to be installed
system_info=$(detect_system_info)
echo "$system_info" | jq -r '.os_distribution'
echo "$system_info" | jq -r '.machine_type'  # "vm" or "pm"
```

## Function Reference

| Function | Description | Return Codes |
|----------|-------------|--------------|
| `LOGDEBUG/INFO/SUCCESS/WARNING/ERROR` | Log messages with levels | Always 0 |
| `str_strip` | Remove leading/trailing whitespace (perl preferred) | - |
| `str_strip_alternative` | Pure-bash whitespace stripping (fallback of `str_strip`) | - |
| `get_ini_value` | Get value from INI file | 0=success, 1=file error, 2=key not found, 99=missing args |
| `get_var` | Load variable from env or config | 0=success, 3=not found |
| `array_to_json` | Convert array to JSON array string | - |
| `associate_array_to_json` | Convert associative array to JSON object string | - |
| `is_element_in_array` | Check element membership in array | 0=yes, 1=no |
| `backup_dir_with_rotation` | Back up directory with rotation | 0=success, 1=missing args or dir not found, 2=backup failed |
| `version_gt/lt/eq/ge/le` | Compare versions | 0=true, 1=false |
| `debug` | Enable xtrace mode | 0 |
| `gracefully_abort` | Handle user interruption | Exits with 1 |
| `proc_killer` | Kill process gracefully | 0=success, 1=error |
| `tmout` | Run command with timeout | 124=timeout, command exit code otherwise |
| `is_ip_in_network` | Check IP in network range | 0=yes, 1=no, 2=invalid args |
| `detect_system_info` | Get system information | 0=success, 1=requirements not met |

## Environment Variables

- `log_file`: Path to log file (default: `/dev/null`), always plain text
- `DEBUG`: Set to `true` to enable debug mode
- `VM_PRODUCT_NAME_PATTERNS`: Custom regex for VM detection

## Best Practices

1. Always source this library at the beginning of your script
2. Set `log_file` variable for persistent logging
3. Check return codes for critical operations
4. Use `set -euo pipefail` in your scripts for better error handling
5. Export variables that need to be available in subshells

## Troubleshooting

**Q: Does the log file contain ANSI color codes?**
- No. Colored output goes to the terminal only; everything written to `log_file` is plain text
- If you see garbled escape sequences on screen, the terminal does not support ANSI colors

**Q: `detect_system_info` fails**
- Install `jq`: `apt install jq` or `yum install jq`
- Ensure Bash version is 4.0 or higher: `bash --version`

**Q: Version comparison not working**
- Verify `sort` command supports `-V` flag
- Use `sort --version` to check

## License

MIT © [Hektorwang]

## Contributing

Contributions are welcome! Please ensure:
- Functions are well-documented
- Error handling is implemented
- Code follows existing style conventions
- Bash 4.0+ compatibility is maintained

## Changelog

See [release-note.md](release-note.md) for version history and changes.
