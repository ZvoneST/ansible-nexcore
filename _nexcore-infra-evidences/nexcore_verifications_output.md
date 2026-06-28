```shell
PLAY [Verify — users, security, logging, automount, patching (all hosts)] ******

TASK [Gathering Facts] *********************************************************
ok: [servera]
ok: [serverb]

TASK [Gather all required groups] **********************************************
ok: [servera] => (item=ops)
ok: [serverb] => (item=ops)
ok: [servera] => (item=dev)
ok: [serverb] => (item=dev)
ok: [servera] => (item=marketing)
ok: [serverb] => (item=marketing)

TASK [Assert each user account exists] *****************************************
ok: [servera] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [serverb] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [servera] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})

TASK [Stat sudoers files for privileged users] *********************************
ok: [servera] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})
ok: [serverb] => (item={'name': 'ops01', 'groups': ['ops'], 'sudo': True})

TASK [Assert sudoers file present for privileged users] ************************
ok: [servera] => (item=ops01) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/ops01"
            }
        },
        "item": {
            "groups": [
                "ops"
            ],
            "name": "ops01",
            "sudo": true
        },
        "stat": {
            "atime": 1782540768.5294096,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c61478132f55118cb5fd2ee8a328b12345eba665",
            "ctime": 1782408841.5397005,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 202996009,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0440",
            "mtime": 1782408840.672694,
            "nlink": 1,
            "path": "/etc/sudoers.d/ops01",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": false,
            "rusr": true,
            "size": 29,
            "uid": 0,
            "version": "2182496398",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": false,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    },
    "msg": "All assertions passed"
}
ok: [serverb] => (item=ops01) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/ops01"
            }
        },
        "item": {
            "groups": [
                "ops"
            ],
            "name": "ops01",
            "sudo": true
        },
        "stat": {
            "atime": 1782541264.0560772,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c61478132f55118cb5fd2ee8a328b12345eba665",
            "ctime": 1782408838.049009,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67646481,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0440",
            "mtime": 1782408837.055009,
            "nlink": 1,
            "path": "/etc/sudoers.d/ops01",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": false,
            "rusr": true,
            "size": 29,
            "uid": 0,
            "version": "3628033709",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": false,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    },
    "msg": "All assertions passed"
}

TASK [Stat sudoers files for non-privileged users] *****************************
ok: [servera] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev01', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [serverb] => (item={'name': 'dev02', 'groups': ['dev'], 'sudo': False})
ok: [servera] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})
ok: [serverb] => (item={'name': 'mkt01', 'groups': ['marketing'], 'sudo': False})

TASK [Assert NO sudoers file for non-privileged users] *************************
ok: [servera] => (item=dev01) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/dev01"
            }
        },
        "item": {
            "groups": [
                "dev"
            ],
            "name": "dev01",
            "sudo": false
        },
        "stat": {
            "exists": false
        }
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=dev02) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/dev02"
            }
        },
        "item": {
            "groups": [
                "dev"
            ],
            "name": "dev02",
            "sudo": false
        },
        "stat": {
            "exists": false
        }
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=mkt01) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/mkt01"
            }
        },
        "item": {
            "groups": [
                "marketing"
            ],
            "name": "mkt01",
            "sudo": false
        },
        "stat": {
            "exists": false
        }
    },
    "msg": "All assertions passed"
}
ok: [serverb] => (item=dev01) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/dev01"
            }
        },
        "item": {
            "groups": [
                "dev"
            ],
            "name": "dev01",
            "sudo": false
        },
        "stat": {
            "exists": false
        }
    },
    "msg": "All assertions passed"
}
ok: [serverb] => (item=dev02) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/dev02"
            }
        },
        "item": {
            "groups": [
                "dev"
            ],
            "name": "dev02",
            "sudo": false
        },
        "stat": {
            "exists": false
        }
    },
    "msg": "All assertions passed"
}
ok: [serverb] => (item=mkt01) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/sudoers.d/mkt01"
            }
        },
        "item": {
            "groups": [
                "marketing"
            ],
            "name": "mkt01",
            "sudo": false
        },
        "stat": {
            "exists": false
        }
    },
    "msg": "All assertions passed"
}

TASK [Read sshd_config] ********************************************************
ok: [servera]
ok: [serverb]

TASK [Assert SSH password authentication is disabled] **************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Collect service facts] ***************************************************
ok: [servera]
ok: [serverb]

TASK [Assert firewalld is running] *********************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Get SELinux mode] ********************************************************
ok: [servera]
ok: [serverb]

TASK [Assert SELinux is enforcing] *********************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [List open firewall ports] ************************************************
ok: [servera]
ok: [serverb]

TASK [Assert each network-open port is open] ***********************************
ok: [servera] => (item=80/tcp) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 80,
        "proto": "tcp"
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=25/tcp) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 25,
        "proto": "tcp"
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=53/tcp) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 53,
        "proto": "tcp"
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=53/udp) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 53,
        "proto": "udp"
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=514/tcp) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 514,
        "proto": "tcp"
    },
    "msg": "All assertions passed"
}
ok: [serverb] => (item=2049/tcp) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 2049,
        "proto": "tcp"
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=143/tcp) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 143,
        "proto": "tcp"
    },
    "msg": "All assertions passed"
}

TASK [List firewall rich rules] ************************************************
ok: [servera]
ok: [serverb]

TASK [Assert each source-restricted port is open only from its source] *********
skipping: [servera]
ok: [serverb] => (item=3306/tcp from 192.168.50.85) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "port": 3306,
        "proto": "tcp",
        "source": "192.168.50.85"
    },
    "msg": "All assertions passed"
}

TASK [Assert rsyslog is running] ***********************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat rsyslog nexcore config] *********************************************
ok: [servera]
ok: [serverb]

TASK [Assert rsyslog nexcore config present] ***********************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Read logrotate config for health log] ************************************
ok: [servera]
ok: [serverb]

TASK [Assert health log rotates daily and is kept 14 days] *********************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat autofs master drop-in] **********************************************
ok: [servera]
ok: [serverb]

TASK [Stat autofs company_share map] *******************************************
ok: [servera]
ok: [serverb]

TASK [Assert autofs config files present and service running] ******************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat health check script] ************************************************
ok: [servera]
ok: [serverb]

TASK [Assert health check script is present and executable] ********************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Read root crontab] *******************************************************
ok: [servera]
ok: [serverb]

TASK [Assert both cron jobs are scheduled] *************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY [Verify — DNS, web, email, log receiver (servera)] ************************

TASK [Gathering Facts] *********************************************************
ok: [servera]

TASK [Assert dnsmasq is running] ***********************************************
ok: [servera]

TASK [Assert dnsmasq service running] ******************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Resolve wiki and www A records] ******************************************
ok: [servera] => (item=wiki.nexcore.local)
ok: [servera] => (item=www.nexcore.local)

TASK [Assert A records resolve to servera IP] **********************************
ok: [servera] => (item=wiki.nexcore.local) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "cmd": [
            "dig",
            "+short",
            "wiki.nexcore.local",
            "@127.0.0.1"
        ],
        "delta": "0:00:00.025035",
        "end": "2026-06-27 16:26:53.956007",
        "failed": false,
        "invocation": {
            "module_args": {
                "_raw_params": "dig +short wiki.nexcore.local @127.0.0.1",
                "_uses_shell": false,
                "argv": null,
                "chdir": null,
                "creates": null,
                "executable": null,
                "removes": null,
                "stdin": null,
                "stdin_add_newline": true,
                "strip_empty_ends": true
            }
        },
        "item": "wiki.nexcore.local",
        "msg": "",
        "rc": 0,
        "start": "2026-06-27 16:26:53.930972",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "192.168.50.85",
        "stdout_lines": [
            "192.168.50.85"
        ]
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=www.nexcore.local) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "cmd": [
            "dig",
            "+short",
            "www.nexcore.local",
            "@127.0.0.1"
        ],
        "delta": "0:00:00.024784",
        "end": "2026-06-27 16:26:55.007071",
        "failed": false,
        "invocation": {
            "module_args": {
                "_raw_params": "dig +short www.nexcore.local @127.0.0.1",
                "_uses_shell": false,
                "argv": null,
                "chdir": null,
                "creates": null,
                "executable": null,
                "removes": null,
                "stdin": null,
                "stdin_add_newline": true,
                "strip_empty_ends": true
            }
        },
        "item": "www.nexcore.local",
        "msg": "",
        "rc": 0,
        "start": "2026-06-27 16:26:54.982287",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "192.168.50.85",
        "stdout_lines": [
            "192.168.50.85"
        ]
    },
    "msg": "All assertions passed"
}

TASK [Resolve MX record] *******************************************************
ok: [servera]

TASK [Assert MX record exists for the domain] **********************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Assert httpd is running] *************************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat both vhost config files] ********************************************
ok: [servera] => (item=/etc/httpd/conf.d/wiki.conf)
ok: [servera] => (item=/etc/httpd/conf.d/wordpress.conf)

TASK [Assert both vhost files exist] *******************************************
ok: [servera] => (item=/etc/httpd/conf.d/wiki.conf) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/httpd/conf.d/wiki.conf"
            }
        },
        "item": "/etc/httpd/conf.d/wiki.conf",
        "stat": {
            "atime": 1782543943.621858,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "utf-8",
            "checksum": "ea007a3a6bc52e30d4a60c554f07a81be070992e",
            "ctime": 1782413121.013828,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67726163,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1782413120.1718216,
            "nlink": 1,
            "path": "/etc/httpd/conf.d/wiki.conf",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 405,
            "uid": 0,
            "version": "4268024236",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=/etc/httpd/conf.d/wordpress.conf) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "/etc/httpd/conf.d/wordpress.conf"
            }
        },
        "item": "/etc/httpd/conf.d/wordpress.conf",
        "stat": {
            "atime": 1782543946.351879,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "utf-8",
            "checksum": "fbf59666a37d485f56c4785440fe0519e4728eda",
            "ctime": 1782413122.8318415,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67726189,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1782413122.1288364,
            "nlink": 1,
            "path": "/etc/httpd/conf.d/wordpress.conf",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 412,
            "uid": 0,
            "version": "2040078957",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    },
    "msg": "All assertions passed"
}

TASK [Check SELinux context on web roots] **************************************
ok: [servera] => (item=/var/www/wiki)
ok: [servera] => (item=/var/www/wordpress)

TASK [Assert web roots carry httpd_sys_rw_content_t] ***************************
ok: [servera] => (item=/var/www/wiki) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "cmd": [
            "ls",
            "-Zd",
            "/var/www/wiki"
        ],
        "delta": "0:00:00.003528",
        "end": "2026-06-27 16:26:58.842370",
        "failed": false,
        "invocation": {
            "module_args": {
                "_raw_params": "ls -Zd /var/www/wiki",
                "_uses_shell": false,
                "argv": null,
                "chdir": null,
                "creates": null,
                "executable": null,
                "removes": null,
                "stdin": null,
                "stdin_add_newline": true,
                "strip_empty_ends": true
            }
        },
        "item": "/var/www/wiki",
        "msg": "",
        "rc": 0,
        "start": "2026-06-27 16:26:58.838842",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "unconfined_u:object_r:httpd_sys_rw_content_t:s0 /var/www/wiki",
        "stdout_lines": [
            "unconfined_u:object_r:httpd_sys_rw_content_t:s0 /var/www/wiki"
        ]
    },
    "msg": "All assertions passed"
}
ok: [servera] => (item=/var/www/wordpress) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_loop_var": "item",
        "changed": false,
        "cmd": [
            "ls",
            "-Zd",
            "/var/www/wordpress"
        ],
        "delta": "0:00:00.003420",
        "end": "2026-06-27 16:26:59.798823",
        "failed": false,
        "invocation": {
            "module_args": {
                "_raw_params": "ls -Zd /var/www/wordpress",
                "_uses_shell": false,
                "argv": null,
                "chdir": null,
                "creates": null,
                "executable": null,
                "removes": null,
                "stdin": null,
                "stdin_add_newline": true,
                "strip_empty_ends": true
            }
        },
        "item": "/var/www/wordpress",
        "msg": "",
        "rc": 0,
        "start": "2026-06-27 16:26:59.795403",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "unconfined_u:object_r:httpd_sys_rw_content_t:s0 /var/www/wordpress",
        "stdout_lines": [
            "unconfined_u:object_r:httpd_sys_rw_content_t:s0 /var/www/wordpress"
        ]
    },
    "msg": "All assertions passed"
}

TASK [Probe HTTP endpoints (informational only)] *******************************
ok: [servera] => (item=wiki.nexcore.local)
ok: [servera] => (item=www.nexcore.local)

TASK [Show HTTP status codes] **************************************************
ok: [servera] => (item=wiki.nexcore.local) => {
    "msg": "http://wiki.nexcore.local -> HTTP 301 (200/30x once the app is installed)"
}
ok: [servera] => (item=www.nexcore.local) => {
    "msg": "http://www.nexcore.local -> HTTP 200 (200/30x once the app is installed)"
}

TASK [Assert postfix is running] ***********************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Read postfix main.cf] ****************************************************
ok: [servera]

TASK [Assert postfix accepts mail for the domain] ******************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Read postfix home_mailbox] ***********************************************
ok: [servera]

TASK [Assert postfix delivers into the shared Maildir] *************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Assert dovecot is running] ***********************************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Read effective dovecot protocols and mail_location] **********************
ok: [servera]

TASK [Assert dovecot serves IMAP from the same Maildir as postfix] *************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Check the IMAP port is listening] ****************************************
ok: [servera]

TASK [Assert dovecot is listening on the IMAP port] ****************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Locate the mutt IMAP client] *********************************************
ok: [servera]

TASK [Assert a mail client (mutt) is installed for retrieval] ******************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Read system mutt defaults] ***********************************************
ok: [servera]

TASK [Assert mutt is allowed to use the plaintext IMAP server] *****************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat remote log directory] ***********************************************
ok: [servera]

TASK [Assert /var/log/remote exists on the receiver] ***************************
ok: [servera] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY [Verify — database, LVM, NFS, log forwarder (serverb)] ********************

TASK [Gathering Facts] *********************************************************
ok: [serverb]

TASK [Collect service facts] ***************************************************
ok: [serverb]

TASK [Assert mariadb is running] ***********************************************
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [List databases] **********************************************************
ok: [serverb]

TASK [Assert wiki and wordpress databases exist] *******************************
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat logical volume device] **********************************************
ok: [serverb]

TASK [Assert logical volume exists] ********************************************
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Check the share is mounted] **********************************************
ok: [serverb]

TASK [Read active NFS exports] *************************************************
ok: [serverb]

TASK [Assert the share is exported] ********************************************
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat the shared Company_Share directory] *********************************
ok: [serverb]

TASK [Assert Company_Share exists with the shared group and setgid perms] ******
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Check nfs-server is active] **********************************************
ok: [serverb]

TASK [Read rsyslog nexcore config] *********************************************
ok: [serverb]

TASK [Assert forwarder targets the receiver over TCP] **************************
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat the backup directory] ***********************************************
ok: [serverb]

TASK [Assert the backup directory exists, is root-owned and private] ***********
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Stat the backup script] **************************************************
ok: [serverb]

TASK [Assert the backup script is present and executable by root only] *********
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

TASK [Read root crontab] *******************************************************
ok: [serverb]

TASK [Assert the nightly backup cron job is scheduled] *************************
ok: [serverb] => {
    "changed": false,
    "msg": "All assertions passed"
}

PLAY RECAP *********************************************************************
servera                    : ok=58   changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
serverb                    : ok=50   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
