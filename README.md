# Ansible Role: server_initial_setup
[![Build](https://github.com/eniocarboni/server_initial_setup/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/eniocarboni/server_initial_setup/actions/workflows/ci.yml) [![GPL License](https://img.shields.io/badge/license-GPL-blue.svg)](https://www.gnu.org/licenses/) [![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.me/EnioCarboni)

An Ansible Role that initial setup a server/vm

## Role Variables

The available variables are listed below, for all references see `defaults/main.yml`

### locales

A locale's list to enable, with default to:
```
locales:
  - en_US.UTF-8
  - it_IT.UTF-8
```

### locale_def

The default locale (def. *en_US.UTF-8*)

### timezone

The Timezone to set in this server (def. *Europe/Rome*)

### hostname

The hostname to set (def. *no hastname set*)

```
hostname: myhostname
```

### disable_ipv6

Boolean to disable or enable ipv6 (def. *true*, ipv6 disabled)

```
disable_ipv6 = true
```

### firewall

Boolean true or false to enable or not a firewall (def. true).
On Ubuntu/Debian use *ufw* firewall while on RedHat system use *firewalld* firewall

```
firewall: true
```

### ufw

Configure firewall ufw only for Ubuntu/Debian OS.
You can add profiles with *profile_enable:* or rules with *rules:*

Default value to:

```
firewall: true
ufw:
  profile_enable:
    - OpenSSH
```

### firewalld

Configure firewall firewalld for ReadHat OS (RedHat, Centos, RockyLinux).
You can add service with *service_enable:*

Default value to:

```
firewall: true
firewalld:
  service_enable:
    - ssh
```

### software_update

Boolean true or false for update all software during profilinga (def. true)

```
software_update: true
```

### packages

List of packages to install with default to:

```
packages:
  - wget
  - openssh-server
```

### auto_upgrade

Enable auto upgrade of security updates packages using (def. no):
* *unattended-upgrades* for Ubuntu OS;
* *dnf-automatic* for RedHat OS version greter equal then 8;
* *yum-cron* for RedHat OS version less than 8

```
auto_upgrade: no
```

### screen_or_tmux

Boolean true or false for install *screen* or *tmux* package (def. true).
Install *screen* on all OS except for RedHat OS version greater equal then 8.
When *screen* is installed there will also be the .screenrc file under /root

```
screen_or_tmux: true
```

## Dependencies

No dependencies in particolar

## Example Playbook

Install with:

```
ansible-galaxy install eniocarboni.server_initial_setup
```

### Example 1: use default variables

```
---
- hosts: all
  become: true

  roles:
    - eniocarboni.server_initial_setup
```

### Example 2: with use of custom variables

```
---
- hosts: all
  become: true

  roles:
    - role: eniocarboni.server_initial_setup
      hostname: "privhostname"
      timezone: "America/New_York"
      packages:
        - wget
        - virtualbox
        - vagrant
      disable_ipv6: false
```

License
-------

GNU General Public License v3.0, see LICENSE file 

Author Information
------------------

This role was created in 2022 by Enio Carboni
