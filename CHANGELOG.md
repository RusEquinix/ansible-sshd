# Changelog

## [0.4] - 2017-07-23

### Changed
* Overhauled the templates due to some cases where they caused a complete lockout

## [0.3] - 2017-03-13

### Added
* Support for Debian 7 and Ubuntu 16

## [0.2] - 2017-03-10

### Added
* 'sshd_hardened' var/flag that will apply sshd_config parameters to harden the SSH server

### Changed
* Moved the following vars from the OS-specific YAML file into the Jinja2 template:

```yaml
sshd_conf_val_hostkey:
  - /etc/ssh/ssh_host_ecdsa_key
  - /etc/ssh/ssh_host_ed25519_key
  - /etc/ssh/ssh_host_rsa_key
sshd_conf_val_listenaddress:
  - "0.0.0.0"
  - "[::]"
```

* Updated README.md

### Fixed

## [0.1] - 2017-03-07
### Added
* Initial commit of ansible-sshd role
* Support for Debian 8 'Jessie' and CentOS 7