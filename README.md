# Java Installation Ansible Role

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-java__install-blue.svg)](https://galaxy.ansible.com/your-username/java_install)
[![MIT License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A comprehensive and flexible Ansible role for installing Java across multiple distributions and platforms. Supports both automated downloads and manual installations for Oracle JDK versions that require authentication.

## Features

- **Multiple Java Distributions**: Oracle JDK, Adoptium OpenJDK (Eclipse Temurin), and OpenJDK via package managers
- **Version Flexibility**: Supports Java versions 8, 11, 17, 21, 23, 24+
- **Authentication Handling**: Gracefully handles Oracle JDK versions requiring manual download
- **Cross-Platform**: Works on Debian/Ubuntu and RHEL/CentOS systems
- **Environment Setup**: Automatically configures `JAVA_HOME` and `PATH`
- **Idempotent**: Safe to run multiple times
- **Override Support**: Can replace existing Java installations

## Requirements

- Ansible 2.9+
- Target systems: Linux (Debian/Ubuntu, RHEL/CentOS/Rocky/AlmaLinux)
- Root or sudo access on target systems

### Environment Setup

For development and testing:
```bash
export ANSIBLE_ROLES_PATH=<path to role>
```

## Role Variables

### Core Variables

| Variable            | Default       | Description                                         | Required |
| ------------------- | ------------- | --------------------------------------------------- | -------- |
| `java_distribution` | `"oracle"`    | Java distribution (`oracle`, `adoptium`, `openjdk`) | No       |
| `java_version`      | `"21"`        | Java version to install                             | No       |
| `install_path`      | `"/opt/java"` | Base installation directory                         | No       |
| `set_java_home`     | `true`        | Configure JAVA_HOME environment variable            | No       |

### Advanced Variables

| Variable                | Default       | Description                            |
| ----------------------- | ------------- | -------------------------------------- |
| `java_platform`         | `"linux-x64"` | Target platform                        |
| `java_package_type`     | `"jdk"`       | Package type (`jdk` or `jre`)          |
| `use_package_manager`   | `true`        | Use system package manager for OpenJDK |
| `override_existing_jdk` | `false`       | Replace existing Java installation     |
| `use_custom_jdk_file`   | `false`       | Force use of custom JDK file           |
| `custom_jdk_path`       | `undefined`   | Path to manually downloaded JDK        |

## 📚 Usage Examples

### Quick Start - Latest Java

```yaml
- name: Install latest Java
  hosts: servers
  become: true
  roles:
    - role: java_install
```

### Oracle JDK 21 (Automatic Download)

```yaml
- name: Install Oracle JDK 21
  hosts: servers
  become: true
  vars:
    java_distribution: "oracle"
    java_version: "21"
    install_path: "/opt/java"
  roles:
    - role: java_install
```

### Oracle JDK with Manual Download (Required for versions 8, 11, 17)

```yaml
- name: Install Oracle JDK 17
  hosts: servers
  become: true
  vars:
    java_distribution: "oracle"
    java_version: "17"
    custom_jdk_path: "/home/user/Downloads/jdk-17_linux-x64_bin.tar.gz"
  roles:
    - role: java_install
```

### Adoptium OpenJDK

```yaml
- name: Install Adoptium OpenJDK
  hosts: servers
  become: true
  vars:
    java_distribution: "adoptium"
    java_version: "17"
  roles:
    - role: java_install
```

### OpenJDK via Package Manager (Headless)

```yaml
- name: Install OpenJDK via package manager
  hosts: servers
  become: true
  vars:
    java_distribution: "openjdk"
    java_version: "11"
    use_package_manager: true
  roles:
    - role: java_install
```

**Note**: Package manager installations use `openjdk-headless` packages for lightweight deployments without GUI components.

## Oracle JDK Authentication

Oracle JDK versions **8, 11, and 17** require authentication and cannot be automatically downloaded. For these versions:

1. **Download manually** from [Oracle's website](https://www.oracle.com/java/technologies/downloads/)
2. **Accept the license agreement**
3. **Provide the file path** using `custom_jdk_path`

### Command Line Override

```bash
ansible-playbook site.yml -e "custom_jdk_path=/path/to/jdk-17_linux-x64_bin.tar.gz"
```

## Dependencies
- **Ansible Collections**: `community.general`
- **System Packages**: `curl`, `wget`, `tar`, `gzip` (automatically installed during testing)

## Example Playbooks

### Multi-Environment Setup

```yaml
- name: Install Java across environments
  hosts: all
  become: true
  vars:
    java_configs:
      development:
        java_distribution: "adoptium"
        java_version: "17"
      production:
        java_distribution: "oracle"
        java_version: "21"
        install_path: "/opt/oracle-java"

  tasks:
    - name: Install Java for environment
      include_role:
        name: java_install
      vars: "{{ java_configs[environment] }}"
```

### With Custom Installation Path

```yaml
- name: Install Java with custom path
  hosts: java_servers
  become: true
  vars:
    java_distribution: "oracle"
    java_version: "21"
    install_path: "/usr/local/java"
    set_java_home: true
  roles:
    - role: java_install
```

## Testing

This role uses [Molecule](https://molecule.readthedocs.io/) for comprehensive testing across multiple scenarios and platforms.

### Test Scenarios

The role includes four distinct Molecule test scenarios:

1. **`default`** - Basic Adoptium OpenJDK installation
2. **`adoptium_jdk`** - Adoptium-specific testing
3. **`oracle_jdk`** - Oracle JDK installation testing
4. **`package_manager_openjdk`** - Package manager-based OpenJDK installation

### Running Tests

#### Prerequisites

```bash
pip install molecule[docker] ansible-core
```

#### Run All Tests

```bash
# Test all scenarios
molecule test

# Test specific scenario
molecule test -s adoptium_jdk
molecule test -s oracle_jdk
molecule test -s package_manager_openjdk
```

### Test Platforms

Each scenario tests against:
- **Ubuntu 22.04** (using `geerlingguy/docker-ubuntu2204-ansible`)
- **Rocky Linux 9** (using `rockylinux:9`)

### Supported Platforms

- ✅ Ubuntu 18.04, 20.04, 22.04
- ✅ Debian 10, 11, 12
- ✅ RHEL 7, 8, 9
- ✅ CentOS 7, 8
- ✅ Rocky Linux 8, 9
- ✅ AlmaLinux 8, 9

## 🔍 Troubleshooting

### Common Issues

**Oracle JDK Download Fails**

```
Solution: Use custom_jdk_path for Oracle JDK versions 8, 11, 17
```

**Permission Denied**

```
Solution: Ensure ansible user has sudo privileges
```

**Java Not Found After Installation**

```
Solution: Source the profile or restart ansible.builtin.shell session
source /etc/profile
```

### Debug Mode

Run with increased verbosity:

```bash
ansible-playbook site.yml -vvv
```

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Run tests (`molecule test`)
4. Commit your changes (`git commit -m 'Add amazing feature'`)
5. Push to the branch (`git push origin feature/amazing-feature`)
6. Open a Pull Request

### Development Setup

```bash
git clone https://github.com/your-username/ansible-java-install.git
cd ansible-java-install

# Install development dependencies
pip install molecule[docker] ansible-core
pip install molecule-docker
ansible-galaxy collection install community.general

# Set role path
export ANSIBLE_ROLES_PATH=<path to role>

# Run tests
molecule test
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ✨ Author

**Mubarak Ibrahim**

- GitHub: [@Mubbyrex](https://github.com/mubbyrex)
- LinkedIn: [Mubarak Ibrahim](https://www.linkedin.com/in/mubarakibrahimofficial5/)

## Acknowledgments

- Adoptium project for providing excellent OpenJDK distributions
- Oracle for Java development
- The Ansible community for best practices and feedback
- Molecule testing framework for comprehensive test coverage

## Version Support Matrix

| Java Version | Oracle JDK | Adoptium | OpenJDK (Package) |
| ------------ | ---------- | -------- | ----------------- |
| 8            | ⚠️ Manual  | ✅ Auto  | ✅ Auto           |
| 11           | ⚠️ Manual  | ✅ Auto  | ✅ Auto           |
| 17           | ⚠️ Manual  | ✅ Auto  | ✅ Auto           |
| 21           | ✅ Auto    | ✅ Auto  | ✅ Auto           |
| 23           | ✅ Auto    | ✅ Auto  | ⚠️ Limited        |
| 24+          | ✅ Auto    | ✅ Auto  | ⚠️ Limited        |

⚠️ Manual = Requires manual download
✅ Auto = Automatic download supported

## Quick Reference

```bash
# Basic installation
ansible-playbook -i inventory site.yml

# With custom JDK
ansible-playbook -i inventory site.yml -e "custom_jdk_path=/path/to/jdk.tar.gz"

# Run specific Molecule test
molecule test -s adoptium_jdk

# Debug mode
ansible-playbook -i inventory site.yml -vvv

# Set role path for development
export ANSIBLE_ROLES_PATH=<path to role>
```