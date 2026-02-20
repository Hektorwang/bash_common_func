# Release Notes

## Version=1.0.0

2026-02-20

### Initial Release

This is the first stable release of the Bash Function Library, providing a comprehensive set of utilities for shell scripting.

### Features

#### Logging System
- Implemented color-coded logging with 5 levels: DEBUG, INFO, SUCCESS, WARNING, ERROR
- Automatic timestamp and line number tracking in log messages
- Dual output support: console (stdout/stderr) and file
- Configurable log file path via `log_file` environment variable

#### Configuration Management
- INI file parser with `get_ini_value` function
- Case-insensitive section matching
- Automatic variable loading with `get_var` function
- Support for environment variable override

#### String Processing
- `str_strip` function for removing leading/trailing whitespace
- Support for tabs, newlines, carriage returns, and full-width spaces (CJK)

#### Version Comparison
- Five comparison functions: `version_gt`, `version_lt`, `version_eq`, `version_ge`, `version_le`
- Natural version sorting using `sort -V`
- Support for complex version strings (e.g., "1.1.1q")

#### Process Management
- `proc_killer` function for graceful process termination
- Configurable signal and grace period
- Automatic escalation from SIGTERM to SIGKILL
- `tmout` function as a lightweight timeout implementation

#### Time Utilities
- `_conv2sec` function for time string conversion
- Support for s/m/h/d units
- Decimal value support (e.g., 0.5h = 30 minutes)

#### Network Utilities
- `is_ip_in_network` function for IP range checking
- Support for CIDR notation (192.168.1.0/24)
- Support for subnet mask format (255.255.255.0)
- Pure AWK implementation for portability

#### System Detection
- `detect_system_info` function for comprehensive system information
- Distribution detection (Ubuntu, CentOS, Debian, etc.)
- Package manager identification (apt, yum, dnf, zypper, pacman, apk)
- Service manager detection (systemd, openrc, runit)
- VM vs Physical Machine detection
- JSON output format using jq

#### Additional Utilities
- `debug` function for enabling xtrace mode
- `gracefully_abort` for handling user interruptions
- `convert_syslog_timestamp` for syslog time conversion (example function)

### Requirements
- Bash 4.0 or higher (required for associative arrays in `detect_system_info`)
- Standard Unix utilities: awk, sed, date, sort
- Optional: jq (required only for `detect_system_info`)

### Bug Fixes
- Fixed uninitialized variable bug in `_conv2sec` function (unit extraction)
- Replaced unsafe `eval` usage in `debug` function with indirect parameter expansion

### Security Improvements
- Removed `eval` from `debug` function to prevent code injection
- Proper quoting throughout the codebase
- Safe handling of user input in all functions

### Documentation
- Comprehensive inline documentation for all functions
- Parameter descriptions and return code documentation
- Usage examples in README.md
- Chinese documentation (README.cn.md)

### Known Limitations
- `detect_system_info` requires jq to be installed
- Color output may not work in all terminal emulators
- `convert_syslog_timestamp` is provided as an example and may need customization
- Cross-year log processing in `convert_syslog_timestamp` requires additional handling

### Compatibility
- Tested on Bash 4.0, 4.3, 4.4, 5.0, 5.1
- Compatible with major Linux distributions:
  - Debian/Ubuntu family
  - RHEL/CentOS/Rocky/AlmaLinux family
  - Arch Linux
  - Alpine Linux
  - openSUSE
  - openEuler/HCE

### Breaking Changes
None (initial release)

### Deprecations
None (initial release)

### Migration Guide
Not applicable (initial release)

---

## Upcoming Features (Planned for v1.1.0)

- Enhanced error messages with suggestions
- Additional string manipulation functions
- File locking utilities
- Retry mechanism with exponential backoff
- Configuration file validation
- Unit tests using bats-core
- Performance benchmarks

---

## Contributing

We welcome contributions! Please see the README.md for contribution guidelines.

## Support

For bug reports and feature requests, please open an issue on the project repository.

---

**Note**: This library follows [Semantic Versioning](https://semver.org/). Version numbers are formatted as MAJOR.MINOR.PATCH.
