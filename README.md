# MySQL `Master/Master` -- `Active/Passive` Replication with Keepalived

Here is an effective, Idempotent and easy to use Ansible Roles for Installing MySQL Replication with Keepalived Virtual IP


⚠ **However this repo was tested and works well, It's created for Testing.**


### Features:

- [x] Configures `LVM`
- [x] Configures `Keepalived`
- [x] Configures `MySQL` Replication
- [x] Harden incoming traffic with `IPTables`
- [x] These Roles are idempotent

> Example

![Imgur](https://i.imgur.com/yHBRgNp.png)

---

### Note on Best Practices

Applying the Best practices for MySQL Replication in this repo is a goal.
It's only a matter of updating the configuration template here:
* `roles/mysql-replication/templates/my.cnf.active.j2`
* `roles/mysql-replication/templates/my.cnf.passive.j2`

So Please contribute by adding any good practice you have Experienced

---

### Test result

| Distro   | Tested   | 
|---       |---       |
| Centos 7 | Yes      |
| Centos 8 | N/A      |
| Ubuntu 18.04 | N/A  |
| Ubuntu 20.04 | N/A  |

---

### Usage

```bash
git clone https://github.com/eslam-gomaa/mysql-active-passive-replication-Ansible.git
cd mysql-active-passive-replication-Ansible
```

 

* Update hosts file

> **Note** -- do NOT change the Group Name `mysql-replication` 

> Currently this repo supports 2 masters only (As 2 masters are enough for most implementations)
```bash
[mysql-replication]
192.168.122.191 role=active    # Master-Active   Active Must be here
192.168.122.108 role=passive   # Master-Passive  Passive Must be here
```

* Update Global Config

`group_vars/all/global_vars.yml`

```yaml
virtual_IP: 192.168.122.200
mysql_username: admin
mysql_password: password22
mysql_port: 3306

msyql_root_password: password22
mysql_replication_user: replication
mysql_replication_password: repl_pass

configure_IPtables: True
ssh_allowed_range: '0.0.0.0/0'
msql_allowed_range: '0.0.0.0/0'
flush_old_rules: False

selinux: permissive # Temporary workaround for: Keepalived_vrrp[1566]: Couldn't setgid: 1000 (Operation not permitted)
                    # --> The issue is related to "SELinux" -- can be fixed later.

lvm: True           # If False will use the default dir
data_disks:         # will be used if lvm == "True"
  - /dev/vdb
  - /dev/vdc
data_volume_fs: ext4
```

* Run the playbook
```bash
ansible-playbook mysql-replication.yaml -i <HOSTS-FILE>
```

### Questions

##### How to Add more disks to the Volume Group ?

> * Append the new disk in the `data_disks` list in the global variables:

```yaml
data_disks:         # will be used if lvm == "True"
  - /dev/vdb
  - /dev/vdc
  - New_ONe
```

> upon running the playbook again, the new disk will be added to the `MySQL` `Volume Group`,
>but you have to extend the `Logical Volume` Manually


---


Maintainer: [Eslam Gomaa](https://www.linkedin.com/in/eslam-gomaa/)
