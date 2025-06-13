# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-06-10

### Added

- Initial release of java_install Ansible role
- Support for Oracle JDK, Adoptium OpenJDK, and system package manager installations
- Support for Java versions 8, 11, 17, 21, 23, 24
- Automatic handling of Oracle JDK authentication requirements
- Cross-platform support (Debian/Ubuntu, RHEL/CentOS/Rocky/AlmaLinux)
- Environment variable configuration (JAVA_HOME, PATH)
- Comprehensive test suite
- Detailed documentation and usage examples

### Features

- Idempotent installations
- Override existing installations option
- Custom installation paths
- Manual JDK file support for restricted downloads
- Automatic symlink creation
- Package manager integration for OpenJDK

### Supported Platforms

- Ubuntu 18.04, 20.04, 22.04, 24.04
- Debian 10, 11, 12
- RHEL 7, 8, 9
- CentOS 7, 8
- Rocky Linux 8, 9
- AlmaLinux 8, 9
- Fedora 35-39
