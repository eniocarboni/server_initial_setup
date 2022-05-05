# Ansible Role: server_initial_setup
[![Build](https://github.com/eniocarboni/server_initial_setup/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/eniocarboni/server_initial_setup/actions/workflows/ci.yml) [![GPL License](https://img.shields.io/badge/license-GPL-blue.svg)](https://www.gnu.org/licenses/) [![Donate](https://img.shields.io/badge/Donate-PayPal-green.svg)](https://www.paypal.me/EnioCarboni)

An Ansible Role that initial setup a server/vm

## Role Variables

The available variables are listed below (sorted temporally according to use), for all references see `defaults/main.yml`

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

Boolean true or false to auto security updates packages using (def. no):
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

### hostname

The hostname to set (def. *no hostname set*)

```
hostname: myhostname
```

### timezone

The Timezone to set in this server (def. *Europe/Rome*)
for list of timezone see command: `timedatectl list-timezones`

```
timezone: Europe/Rome
```

### locales

A locale's list to enable, with default to:
for list of locales see command: `localectl list-locales`

```
locales:
  - en_US.UTF-8
  - it_IT.UTF-8
```

### locale_def

The default locale (def. *en_US.UTF-8*)

```
locale_def: en_US.UTF-8
```

### ssh_keys

Add or remove ssh authorized keys to *.ssh/authorized_keys* file (def. [])

Each element of the array/list is a dictionary of key/value where:

* *re*: its value is the regular expression to look for in every line in *.ssh/authorized_keys*
* *key*: its value is the ssh key to add
* *state*: its value, if exists must be `present` or `absent` with `present` as default for update or insert the ssh key. If `absent` the ssh key will be removed

See example below

```
ssh_keys: []
```

### disable_ipv6

Boolean to disable or enable ipv6 (def. *true*, ipv6 disabled)

```
disable_ipv6 = true
```

### sysctls

Dictionary of key/value to use with sysctl command (def. is empty dict).
The key/value will be saved in */etc/sysctl.d/999-sysctl-server_initial_setup.conf*

Some values can be changed by other software such as *ufw firewall* on startup:
* see */etc/ufw/sysctl.conf* on Ubuntu system

```
sysctls: {}
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

Configure firewall *firewalld* for ReadHat OS (RedHat, Centos, RockyLinux).
You can add service with *service_enable:*

Default value to:

```
firewall: true
firewalld:
  service_enable:
    - ssh
```

### force_iptables

Boolean to force use *iptables* / *ip6tables* instead of ufw or firewalld (def. false)

```
force_iptables: false
```

### iptables_rules_path

Path where save the templates (*iptables_main_rules.j2* and *ip6tables_main_rules.j2*) shell for *iptables_rules* and *ip6tables_rules*
  (def. /root/iptables_rules)

```
iptables_rules_path: "/root/iptables_rules"
```

### iptables_rules

It's an array of *iptables* rules.
 Rules can be inserted with or without leading *iptables*.
 *iptables_rules* needs option *force_iptables: true*
 (def. [])

```
iptables_rules: []
```

### ip6tables_rules

It's an array of *ip6tables* rules.
 Rules can be inserted with or without leading *ip6tables*.
 *ip6tables_rules* needs option *force_iptables: true*
(def. [])

```
ip6tables_rules: []
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

### Example 3: with use of custom variables sysctl and iptables

```
---
- hosts: all
  become: true

  roles:
    - role: eniocarboni.server_initial_setup
      hostname: "privhostname"
      timezone: "Europe/Rome"
      packages:
        - wget
      sysctls:
        vm.swappiness: 10
        net.ipv4.conf.all.accept_source_route: 0
      disable_ipv6: false
      force_iptables: true
      iptables_rules_path: "/root/iptables_rules"
      iptables_rules:
        - "-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT"
        - "-A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT"
        - "-A INPUT -p udp --syn -m state --state NEW --dport 53 -j ACCEPT"
        - "-A INPUT -p udp -m state --state NEW --dport 53 -j ACCEPT"
        - "-A INPUT -p tcp --dport 22 -j ACCEPT"
        - "-P INPUT DROP"
        - "-P OUTPUT ACCEPT"
      ip6tables_rules: []
```

### Example 4: ssh_keys

```
---
- hosts: all
  become: true

  roles:
    - role: eniocarboni.server_initial_setup
    ssh_keys:
      - re: "eniocarboni_rsa_2048bit"
        key: "ssh-rsa AAAA...hgl==  eniocarboni_rsa_2048bit"
      - re: "oldkeys"
        state: absent
```

License
-------

GNU General Public License v3.0, see LICENSE file 

Author Information
------------------

This role was created in 2022 by Enio Carboni
