# ansible-sshd: Ansible role for configuring OpenSSH

This role configures the OpenSSH daemon by using templates to create the sshd_config file.

* By default configures the SSH daemon with the defaults for a given distribution.
* Supports all sshd_config options (see [defaults/sshd_config.man](defaults/sshd_config.man)).
* Tests the sshd_config before reloading sshd.

**IMPORTANT!** Please test this role thoroughly before applying in production as it has the potential
to break things, horrible things, e.g. remote access to your server.

## Requirements

This role should support most any *nix running OpenSSH.  While this role will ensure that the
SSH server is installed, you might have trouble running Ansible on a server without SSH running :)

So far, it has been tested on:

* CentOS 7
* Debian wheezy(7), jessie(8)
* Ubuntu Xenial (16 LTS)

Support for other *nix OSes can be easily added by creating a Jinja2 template out of the
distribution's 'sshd_config' file and added to the list of [templates](templates/), and then 
adding a [vars](vars/) file as well. Ansible will use the facts gathered to determine the 
current running OS and use the correct YAML vars file, using the following:
`{{ ansible_distribution }}_{{ ansible_distribution_major_version }}.yml`

## Role variables
All of the sshd_config parameters are in the format `sshd_conf_val_...` For example, the `UsePrivilegeSeparation`
parameter is associated with the variable `sshd_conf_val_useprivilegeseparation`. For a list of all *possible*
sshd_config parameter variables, see [vars/_sshd_default.yml](vars/_sshd_default.yml).

Any of these variables can be overridden in your Playbooks (see example Playbooks below).

**NOTE** that not all variables are supported by each *nix OS. Each distribution will run a particular version and 
will also have certain options compiled in/out. While I have dumped the supported variables in each OS [vars](vars/) file,
it is still prudent to run `sshd -T` on your server to see all of the parameters your OpenSSH implementation supports.

### OS specific vars files
When no overriding vars are used in your plays, this role will provide an sshd_config that *should*
match the OS default. There are two files where variables are stored:

* YAML vars file (`{{ ansible_distribution }}_{{ ansible_distribution_major_version }}.yml` e.g. Debian_8.yml) stored in [vars](vars/). These are the variables that pertain to OpenSSH operation (OS package names, file locations, permissions).
* Jinja2 template (`{{ ansible_distribution }}_{{ ansible_distribution_major_version }}_sshd_config.j2` e.g. Debian_8_sshd_config.j2) stored in [templates](templates/). These are the OS-specific sshd_config template files that also contain the default parameters.

At a minimum, the YAML vars file for each OS should contain the following variables (from the [Debian 8 vars](vars/Debian_8.yml) file):

```yaml
---
sshd_os_supported: True

sshd_config_file: /etc/ssh/sshd_config
sshd_binary: /usr/sbin/sshd
sshd_service: ssh
sshd_packages:
  - openssh-server
  - openssh-sftp-server
sshd_config_file_owner: root
sshd_config_file_group: root
sshd_config_file_mode: "0644"
```

**NOTE** that `sshd_os_supported` needs to be set to `True` for the vars and Jinja2 template files to be used.

### Supported Flags
While it is possible to override one or more sshd_config parameters using vars in your Playbooks, there are some flags that can be set to govern certain overall configuration "sets".

* `sshd_hardened`: by default this is set to `False`. If set to `True` in your Playbook, it will override a certain set of OpenSSH parameters that will lead to a more secure OpenSSH configuration by using the vars from [vars/hardened_config.yml](vars/hardened_config.yml). **IMPORTANT:** Setting this to `True` will set `PasswordAuthentication no` and `PermitRootLogin no` so make sure that you have SSH keys setup and working properly!

## Dependencies

None

## Example Playbook

```yaml
---
# file: playbook.yml

- hosts: all
  become: True
  
  vars_files:
    - vars/sshd_conf_overides.yml

  roles:
    - ansible-sshd
```

## License

BSD, 3-clause

## Author

Dave Heebner <dave@rockpenguin.com>

&copy; 2017, Rockpenguin Technology, LLC
