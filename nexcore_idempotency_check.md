```shell
PLAY [Users and SSH provisioning — all managed hosts] **************************

TASK [Gathering Facts] *********************************************************
ok: [servera]
ok: [serverb]

TASK [users : Ensure user groups exist] ****************************************
ok: [servera] => (item=ops)
ok: [serverb] => (item=ops)
ok: [servera] => (item=dev)
ok: [serverb] => (item=dev)
ok: [servera] => (item=marketing)
ok: [serverb] => (item=marketing)

TASK [users : Ensure the shared Company_Share group exists] ********************
ok: [servera]
ok: [serverb]

TASK [users : Ensure user accounts exist] **************************************
ok: [servera] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [serverb] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [servera] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})

TASK [users : Ensure control-node SSH key directory exists] ********************
ok: [servera -> localhost]

TASK [users : Generate per-user SSH keypairs on the control node] **************
ok: [servera -> localhost] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [servera -> localhost] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [servera -> localhost] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [servera -> localhost] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})

TASK [users : Deploy SSH authorised keys] **************************************
ok: [servera] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [serverb] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [servera] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})

TASK [users : Disable SSH password authentication] *****************************
ok: [servera]
ok: [serverb]

TASK [users : Grant sudo to privileged users via sudoers.d] ********************
ok: [servera] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [serverb] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})

TASK [users : Remove sudoers.d entries for non-privileged users] ***************
ok: [servera] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})
ok: [serverb] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})

PLAY [Security hardening — all managed hosts] **********************************

TASK [Gathering Facts] *********************************************************
ok: [servera]
ok: [serverb]

TASK [security : Ensure firewalld is installed and enabled] ********************
ok: [servera]
ok: [serverb]

TASK [security : Start and enable firewalld] ***********************************
ok: [servera]
ok: [serverb]

TASK [security : Open required ports for this host] ****************************
ok: [servera] => (item={'port': 80, 'proto': 'tcp'})
ok: [serverb] => (item={'port': 2049, 'proto': 'tcp'})
ok: [servera] => (item={'port': 25, 'proto': 'tcp'})
ok: [serverb] => (item={'port': 3306, 'proto': 'tcp'})
ok: [servera] => (item={'port': 53, 'proto': 'tcp'})
ok: [servera] => (item={'port': 53, 'proto': 'udp'})
ok: [servera] => (item={'port': 514, 'proto': 'tcp'})
ok: [servera] => (item={'port': 143, 'proto': 'tcp'})

TASK [security : Assert SELinux is enforcing] **********************************
ok: [servera]
ok: [serverb]

PLAY [DNS (dnsmasq) — servera] *************************************************

TASK [Gathering Facts] *********************************************************
ok: [servera]

TASK [dns : Install dnsmasq] ***************************************************
ok: [servera]

TASK [dns : Deploy dnsmasq configuration] **************************************
ok: [servera]

TASK [dns : Enable and start dnsmasq] ******************************************
ok: [servera]

TASK [dns : Point this host at the local DNS resolver] *************************
ok: [servera]

PLAY [MariaDB — serverb] *******************************************************

TASK [Gathering Facts] *********************************************************
ok: [serverb]

TASK [database : Install MariaDB server] ***************************************
ok: [serverb]

TASK [database : Enable and start MariaDB] *************************************
ok: [serverb]

TASK [database : Deploy MariaDB server configuration] **************************
ok: [serverb]

TASK [database : Apply MariaDB configuration before managing users] ************

TASK [database : Set MariaDB root password] ************************************
ok: [serverb]

TASK [database : Deploy .my.cnf for root authentication] ***********************
ok: [serverb]

TASK [database : Create application databases] *********************************
ok: [serverb] => (item=wiki)
ok: [serverb] => (item=wordpress)

TASK [database : Create wiki database user] ************************************
ok: [serverb]

TASK [database : Create WordPress database user] *******************************
ok: [serverb]

PLAY [LVM + NFS storage — serverb] *********************************************

TASK [Gathering Facts] *********************************************************
ok: [serverb]

TASK [storage : Install LVM and NFS server packages] ***************************
ok: [serverb]

TASK [storage : Create sparse backing file for the loopback PV] ****************
ok: [serverb]

TASK [storage : Restrict permissions on the backing file] **********************
ok: [serverb]

TASK [storage : Deploy systemd unit that attaches the loopback device at boot] ***
ok: [serverb]

TASK [storage : Flush handlers so the unit is reloaded before it is started] ***

TASK [storage : Enable and start the loopback attach service] ******************
ok: [serverb]

TASK [storage : Discover the loop device backing the PV] ***********************
ok: [serverb]

TASK [storage : Fail if the loopback device is not attached] *******************
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [storage : Record the loop device path] ***********************************
ok: [serverb]

TASK [storage : Create LVM volume group] ***************************************
ok: [serverb]

TASK [storage : Create logical volume] *****************************************
ok: [serverb]

TASK [storage : Format logical volume with XFS] ********************************
ok: [serverb]

TASK [storage : Create NFS export mount point] *********************************
ok: [serverb]

TASK [storage : Mount the logical volume] **************************************
ok: [serverb]

TASK [storage : Create the shared Company_Share directory] *********************
ok: [serverb]

TASK [storage : Deploy /etc/exports from template] *****************************
ok: [serverb]

TASK [storage : Enable and start NFS server] ***********************************
ok: [serverb]

PLAY [Nightly database backups — serverb] **************************************

TASK [Gathering Facts] *********************************************************
ok: [serverb]

TASK [backup : Ensure backup directory exists] *********************************
ok: [serverb]

TASK [backup : Deploy nightly database backup script] **************************
ok: [serverb]

TASK [backup : Schedule nightly database backup] *******************************
ok: [serverb]

PLAY [Apache + MediaWiki + WordPress — servera] ********************************

TASK [Gathering Facts] *********************************************************
ok: [servera]

TASK [web : Install Apache and PHP] ********************************************
ok: [servera]

TASK [web : Create web root directories] ***************************************
ok: [servera] => (item=/var/www/wiki)
ok: [servera] => (item=/var/www/wordpress)

TASK [web : Download MediaWiki archive] ****************************************
ok: [servera]

TASK [web : Extract MediaWiki] *************************************************
skipping: [servera]

TASK [web : Download WordPress archive] ****************************************
ok: [servera]

TASK [web : Extract WordPress] *************************************************
skipping: [servera]

TASK [web : Set ownership of web directories] **********************************
ok: [servera] => (item=/var/www/wiki)
ok: [servera] => (item=/var/www/wordpress)

TASK [web : Apply httpd_sys_content_t SELinux context to web roots] ************
ok: [servera] => (item=/var/www/wiki)
ok: [servera] => (item=/var/www/wordpress)

TASK [web : Allow Apache to connect to the database (SELinux boolean)] *********
ok: [servera]

TASK [web : Deploy MediaWiki virtual host] *************************************
ok: [servera]

TASK [web : Deploy WordPress virtual host] *************************************
ok: [servera]

TASK [web : Enable and start Apache] *******************************************
ok: [servera]

PLAY [AutoFS per-user mounts — all managed hosts] ******************************

TASK [Gathering Facts] *********************************************************
ok: [servera]
ok: [serverb]

TASK [automount : Install autofs and NFS client] *******************************
ok: [servera]
ok: [serverb]

TASK [automount : Deploy auto.master entry for per-user mounts] ****************
ok: [servera]
ok: [serverb]

TASK [automount : Deploy per-user auto map] ************************************
ok: [servera]
ok: [serverb]

TASK [automount : Enable and start autofs] *************************************
ok: [servera]
ok: [serverb]

PLAY [Postfix email — servera] *************************************************

TASK [Gathering Facts] *********************************************************
ok: [servera]

TASK [email : Install Postfix and mail utilities] ******************************
ok: [servera]

TASK [email : Deploy Postfix main.cf from template] ****************************
ok: [servera]

TASK [email : Enable and start Postfix] ****************************************
ok: [servera]

TASK [email : Install Dovecot IMAP server] *************************************
ok: [servera]

TASK [email : Deploy Dovecot configuration from template] **********************
ok: [servera]

TASK [email : Enable and start Dovecot] ****************************************
ok: [servera]

TASK [email : Allow mutt to use the plaintext IMAP server] *********************
ok: [servera]

PLAY [Centralised logging — all managed hosts] *********************************

TASK [Gathering Facts] *********************************************************
ok: [servera]
ok: [serverb]

TASK [logging : Install rsyslog] ***********************************************
ok: [servera]
ok: [serverb]

TASK [logging : Deploy rsyslog configuration for this host role] ***************
ok: [servera]
ok: [serverb]

TASK [logging : Create remote log directory on receiver] ***********************
skipping: [serverb]
ok: [servera]

TASK [logging : Configure logrotate for nexcore-health.log] ********************
ok: [servera]
ok: [serverb]

TASK [logging : Enable and start rsyslog] **************************************
ok: [servera]
ok: [serverb]

PLAY [Patching and maintenance — all managed hosts] ****************************

TASK [Gathering Facts] *********************************************************
ok: [servera]
ok: [serverb]

TASK [patching : Update all packages] ******************************************
ok: [servera]
ok: [serverb]

TASK [patching : Check if reboot is required] **********************************
skipping: [servera]
skipping: [serverb]

TASK [patching : Reboot if kernel or core libraries were updated] **************
skipping: [servera]
skipping: [serverb]

TASK [patching : Deploy health check script] ***********************************
ok: [servera]
ok: [serverb]

TASK [patching : Schedule daily health check at 03:00] *************************
ok: [servera]
ok: [serverb]

TASK [patching : Schedule weekly package cache cleanup on Sunday at 04:00] *****
ok: [servera]
ok: [serverb]

PLAY RECAP *********************************************************************
servera                    : ok=55   changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0   
serverb                    : ok=58   changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0   
```
