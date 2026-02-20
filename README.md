# Bash Function Library

A comprehensive collection of reusable Bash functions for logging, configuration management, process control, and system detection.

## Requirements

- Bash 4.0 or higher
- Standard Unix utilities: `awk`, `sed`, `date`, `sort`
- Optional: `jq` (required for `detect_system_info` function)

## Features

### Logging System
- Color-coded log levels (DEBUG, INFO, SUCCESS, WARNING, ERROR)
- Automatic timestamp and line number tracking
- File and console output support
- Functions: `LOGDEBUG`, `LOGINFO`, `LOGSUCCESS`, `LOGWARNING`, `LOGERROR`

### Configuration Management
- INI file parsing with section and key support
- Case-insensitive section matching
- Automatic variable loading from config files
- Functions: `get_ini_value`, `get_var`

### String Processing
- Strip leading/trailing whitespace, tabs, newlines
- Support for full-width spaces (CJK)
- Function: `str_strip`

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

# Optional: Set log file
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
| `str_strip` | Remove leading/trailing whitespace | - |
| `get_ini_value` | Get value from INI file | 0=success, 1=file error, 2=key not found, 99=missing args |
| `get_var` | Load variable from env or config | 0=success, 3=not found |
| `version_gt/lt/eq/ge/le` | Compare versions | 0=true, 1=false |
| `debug` | Enable xtrace mode | 0 |
| `gracefully_abort` | Handle user interruption | Exits with 1 |
| `proc_killer` | Kill process gracefully | 0=success, 1=error |
| `tmout` | Run command with timeout | 124=timeout, command exit code otherwise |
| `is_ip_in_network` | Check IP in network range | 0=yes, 1=no, 2=invalid args |
| `detect_system_info` | Get system information | 0=success, 1=requirements not met |

## Environment Variables

- `log_file`: Path to log file (default: `/dev/null`)
- `DEBUG`: Set to `true` to enable debug mode
- `VM_PRODUCT_NAME_PATTERNS`: Custom regex for VM detection

## Color Codes

The library exports color control variables:
- `SETCOLOR_DEBUG`: White
- `SETCOLOR_NORMAL`: Default
- `SETCOLOR_SUCCESS`: Green
- `SETCOLOR_WARNING`: Yellow
- `SETCOLOR_ERROR`: Red

## Best Practices

1. Always source this library at the beginning of your script
2. Set `log_file` variable for persistent logging
3. Check return codes for critical operations
4. Use `set -euo pipefail` in your scripts for better error handling
5. Export variables that need to be available in subshells

## Troubleshooting

**Q: Colors not showing in logs**
- Ensure your terminal supports ANSI color codes
- Check if output is being piped (colors disabled in pipes by default)

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
