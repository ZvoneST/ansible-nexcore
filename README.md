# NexCore Infrastructure

**Project assignment**  
**Course:** Managing DevOps Solutions Based on the Linux Operating System  
**Institution:** Algebra Bernays University  
**Year:** 2026

---

## Table of Contents

1. [Project Overview and Objective](#1-project-overview-and-objective)
2. [Architecture and Inventory](#2-architecture-and-inventory)
3. [Repository Structure and Execution](#3-repository-structure-and-execution)
4. [Implementation by Business Requirements](#4-implementation-by-business-requirements)
   - [4.1 Variable Hierarchy in the Project](#41-variable-hierarchy-in-the-project)
   - [4.2 Role Overview and Key Decisions](#42-role-overview-and-key-decisions)
5. [Challenges and Solutions](#5-challenges-and-solutions)
   - [5.1 The `ansible` Service User](#51-the-ansible-service-user)
   - [5.2 LVM/NFS Without a Free Disk](#52-lvmnfs-without-a-free-disk)
6. [Security Aspects](#6-security-aspects)
7. [Validation and Evidence of Execution](#7-validation-and-evidence-of-execution)
8. [Conclusion](#8-conclusion)

---

## 1. Project Overview and Objective

The NexCore project fully automates the build and configuration of a two-server environment on RHEL 9 (Red Hat Enterprise Linux 9) by using Ansible. The entire infrastructure is described declaratively, as code (Infrastructure as Code), which means that an identical environment can be built repeatedly with a single command and without manual intervention.

The solution covers eleven business requirements (BR-01 to BR-11): from user management and security hardening, through web applications (MediaWiki and WordPress), a database (MariaDB), DNS, shared storage (LVM + NFS), and email (Postfix + Dovecot), to centralized logging, automated patching, and database backups. This provides four key benefits:

- **Repeatability** - the same environment can be built an unlimited number of times with an identical result.
- **Idempotence** - running the same code multiple times brings the system into the desired state without unnecessary changes.
- **Documentation** - the code is also the documentation; the configuration of each service is visible and versioned.
- **Verifiability** - a separate playbook automatically confirms that the system state is exactly as required.

## 2. Architecture and Inventory

The environment consists of a control node (workstation), from which Ansible is executed, and two managed servers logically divided into the `web` and `db` groups.

This separation allows individual roles to be applied only to the appropriate servers. The environment domain is `nexcore.local`.

| Node | IP / Group | Role in the Environment |
| --- | --- | --- |
| `workstation` | control node | Control node: running Ansible playbooks, generating and storing users' SSH keys and the vault password (outside the inventory of managed servers). |
| `servera` | `web` | DNS (`dnsmasq`), Apache + MediaWiki 1.41.1 + WordPress 6.5.3, Postfix + Dovecot (IMAP), rsyslog log receiver. |
| `serverb` | `db` | MariaDB, LVM + NFS shared storage, nightly database backups, rsyslog log sender. |
| `servera` + `serverb` | `all` | Users and SSH, security hardening (`firewalld` + SELinux), AutoFS shared directory, patches, and health checks. |

Ansible connects to the servers as the `ansible` service user via an SSH key and elevates privileges using `sudo`. Variables are arranged hierarchically: `group_vars/all.yml` contains global settings (domain, DNS, user list), `group_vars/web.yml` and `group_vars/db.yml` contain layer-specific settings, and the files under `host_vars/` contain host-specific settings such as open firewall ports and the role in the logging system.

## 3. Repository Structure and Execution

The repository follows a standard Ansible project structure. Each role contains the subdirectories `tasks/` (tasks), `handlers/` (handlers, for example restarting a service after a change), and `templates/` (Jinja2 configuration templates). This makes each service a complete and reusable unit.

| File / Directory | Purpose |
| --- | --- |
| `site.yml` / `verify.yml` | Main playbook (orchestration of 11 roles) and playbook for automated verification. |
| `ansible.cfg` / `inventory.ini` | Basic settings and the server list with groups. |
| `group_vars/`, `host_vars/` | Variables at the group level and at the individual server level. |
| `roles/` | Eleven roles: `users`, `security`, `dns`, `database`, `storage`, `backup`, `web`, `automount`, `email`, `logging`, `patching`. |
| `_nexcore-infra-evidences/` | Evidence: verification outputs, idempotence check, and 18 screenshots. |

Deployment is performed in several steps. The main playbook intentionally follows a logical dependency order: users and security -> DNS, database, storage -> web and other services. Each section is marked with a tag, so it is possible to run only part of the configuration:

```bash
ansible-galaxy collection install -r requirements.yml
ansible-playbook bootstrap_users.yml    # service user
ansible-playbook site.yml               # full build
ansible-playbook verify.yml             # correctness verification
```

The application database passwords and the MariaDB root password are stored encrypted (Ansible Vault, AES256) in `group_vars/db.yml`. Unencrypted secrets are never stored in the repository. The vault password and sensitive artifacts are excluded through `.gitignore`. The project also includes configuration for `ansible-lint` and `yamllint` to maintain code consistency and quality.

## 4. Implementation by Business Requirements

The following table maps the eleven requirements to the corresponding roles. A brief explanation follows below.

| Requirement | Role | Implementation Summary |
| --- | --- | --- |
| BR-05 | `users` | Users, groups, and SSH from the single `nexcore_users` list; password login disabled; `sudo` assigned in a data-driven way. |
| BR-08 | `security` | `firewalld` opens only the required ports (MySQL restricted to `servera` using a rich rule); SELinux permanently runs in Enforcing mode. |
| BR-04 | `dns` | `dnsmasq` with A records (`wiki`/`www` -> `servera`) and an MX record for the domain; external queries are forwarded to public resolvers. |
| BR-01/02 | `database` | MariaDB with the `wiki` and `wordpress` databases; database users are restricted to their own databases and access only from the address of `servera`. |
| BR-03 | `storage` | LVM on a loopback file -> XFS -> NFS export; the `Company_Share` directory uses permissions `2770` (`setgid`, group `company`). |
| BR-06 | `automount` | AutoFS mounts the shared directory into `~/Company_Share` on demand using a direct map; all users share the same files. |
| BR-07 | `email` | Postfix (Maildir delivery) + Dovecot (IMAP); the shared `mail_spool_dir` path prevents divergence; `mutt` is used as the client. |
| BR-01/02 | `web` | Apache + PHP; MediaWiki and WordPress in separate virtual hosts; SELinux context and database access boolean configured. |
| BR-09/11 | `logging` | rsyslog sender -> receiver over TCP 514; `logrotate` rotates the health-check log daily, keeps it for 14 days, and compresses it. |
| BR-10 | `patching` | Package updates with controlled restart; cron: daily health check at 03:00 and weekly cache cleanup. |
| Additional | `backup` | Nightly compressed `mysqldump` of databases at 01:30 in a protected `0700` directory; 14-day retention. |

### 4.1 Variable Hierarchy in the Project

Variables are organized into three levels according to Ansible best practices, following the principle of decreasing generality. At the highest level, `group_vars/all.yml` contains settings shared by the entire environment, such as the domain, DNS, user list, and shared group. The layer below, `group_vars/web.yml` and `group_vars/db.yml`, contains settings specific to a particular group of servers, namely the web layer and the database/storage layer. The lowest level, `host_vars/servera.yml` and `host_vars/serverb.yml`, defines settings for each individual server, such as the list of open firewall ports and the role in the logging system. Values that are strictly tied to a single role reside within the roles themselves and their templates.

This hierarchy was chosen for several reasons. It reduces duplication because a value is defined in one place and inherited where needed, so a change such as a network or domain change is made in one place. It avoids hard-coding values in tasks, allowing the same unchanged code to run on different servers by changing only variables. It respects Ansible precedence rules, where more specific values override more general ones (`host_vars` > `group_vars/group` > `group_vars/all`), enabling targeted exceptions without modifying shared settings. Finally, secrets are isolated in an encrypted layer (Ansible Vault in `group_vars/db.yml`), separated from the rest of the configuration.

### 4.2 Role Overview and Key Decisions

The implementation is divided into eleven independent, reusable roles. The following table provides a short description of each role and the key design decision made while creating it.

| Role | Short Description | Key Design Decision |
| --- | --- | --- |
| `users` | Creates users, groups, and SSH access from the `nexcore_users` list. | A single authoritative list as the source of truth; keys are generated on the control node; password login is disabled; `sudo` is managed through `sudoers.d` in a deterministic way. |
| `security` | Firewall (`firewalld`) and SELinux verification. | Only necessary ports are opened; source-restricted ports are implemented as rich rules; SELinux remains Enforcing and is verified rather than disabled. |
| `dns` | Local DNS server (`dnsmasq`) with A and MX records. | Lightweight `dnsmasq` is selected instead of heavier BIND; it listens only on localhost and the LAN; external queries go to public resolvers. |
| `database` | MariaDB with application databases and users. | Access is allowed only from `servera` by IP address; `skip-name-resolve` protects authorization checks; passwords are stored in Vault. |
| `storage` | LVM logical volume, XFS, and NFS export. | The PV is a sparse loopback file because `serverb` has no free disk; systemd recreates it at boot; `Company_Share` uses `2770`/`setgid` and the `company` group. |
| `backup` | Nightly database backups. | `mysqldump` uses `--single-transaction` and `gzip`; the directory is `0700` and accessible only by root; retention is 14 days; execution is scheduled by cron. |
| `web` | Apache, PHP, MediaWiki, and WordPress. | Separate virtual hosts; SELinux context `httpd_sys_rw_content_t` and database-access boolean; application versions parameterized through variables. |
| `automount` | AutoFS mounting of the shared directory. | A direct map is used instead of an indirect map under `/home`, preventing home directories from being shadowed; mounting happens on demand. |
| `email` | Postfix for sending and Dovecot for IMAP retrieval. | A shared `mail_spool_dir` path is used for delivery and retrieval; plaintext without TLS is aligned with the LAN posture; `mutt` is used as an example client. |
| `logging` | Centralized rsyslog and rotation with `logrotate`. | The role (`forwarder`/`receiver`) is selected with the `rsyslog_role` variable; transfer uses reliable TCP; log rotation keeps 14 days of logs. |
| `patching` | Package updates and health checks. | Controlled reboot only when `needs-restarting` requests it; cron jobs run daily checks and weekly cache cleanup. |

## 5. Challenges and Solutions

### 5.1 The `ansible` Service User

**Problem:** Ansible logs in as the `ansible` account, but on a new server that account does not yet exist. As a result, `site.yml` cannot connect and cannot create the account by itself.

**Solution:** A one-time `bootstrap_users.yml` playbook is run through an admin account. It:

- creates the `ansible` group and user in `wheel`, with `/bin/bash` and a home directory;
- sets the password from the vault, only as a console fallback because SSH remains key-only;
- adds passwordless `sudo` through `/etc/sudoers.d/ansible`, never modifying `/etc/sudoers` directly;
- installs the admin public key into `~ansible/.ssh/authorized_keys`, with directory permissions `0700` and key permissions `0600`.

**Rule:** bootstrap = once, as admin; `site.yml` = continuously, as `ansible`. This keeps the main playbook clean and idempotent.

### 5.2 LVM/NFS Without a Free Disk

**Problem:** An LVM volume group and logical volume are required for NFS, but `serverb` has no free disk and the existing VG is full.

**Solution:** LVM is built on a sparse loopback file: 11 GB under `/home`, slightly larger than the 10 GB LV because of metadata.

**Boot behavior:** `nexcore-loopback.service` recreates the loop device at every boot, and the XFS mount waits for it through `x-systemd.requires=...`.

**Verification:** the role runs `losetup` with `check_mode: false` and asserts that the device is attached before building the VG.

## 6. Security Aspects

Security is built in at multiple levels, consistently following the principle of least privilege:

- Server access is allowed only through SSH keys; password login is disabled.
- The firewall opens only the required ports, and database access is additionally restricted to a single source address.
- SELinux permanently runs in Enforcing mode, with precise contexts and booleans for the web layer.
- Secrets are encrypted with Ansible Vault; unencrypted secrets are never stored in the repository.
- Database backups are stored in a directory accessible only to the `root` user (`0700`).

## 7. Validation and Evidence of Execution

Correctness is proven in several ways. The `verify.yml` playbook encodes checks as `ansible.builtin.assert` tasks: existence of users, groups, and sudoers files; open firewall ports; SELinux mode; service status; configuration files; and the state of the database, NFS, LVM, and cron jobs. All checks pass without any errors:

```text
verify.yml    servera: ok=58 failed=0    serverb: ok=50 failed=0
```

Running the main playbook again introduces no changes, which proves idempotence:

```text
site.yml (2x)    servera: changed=0    serverb: changed=0
```

Evidence, verification output, and supporting descriptions are collected in the following files:
- [project_report_evidences.md](https://github.com/ZvoneST/ansible-nexcore/blob/dev/_nexcore-infra-evidences/project_report_evidences.md)
- [nexcore_idempotency_check.md](https://github.com/ZvoneST/ansible-nexcore/blob/dev/_nexcore-infra-evidences/nexcore_idempotency_check.md)
- [nexcore_verifications_output.md](https://github.com/ZvoneST/ansible-nexcore/blob/dev/_nexcore-infra-evidences/nexcore_verifications_output.md)

## 8. Conclusion

The NexCore project fulfills the defined business requirements and demonstrates how complete server infrastructure can be described as code and built repeatedly and securely with Ansible. The solution is modular (eleven independent roles), parameterized (variables at the group and server levels), secure (Vault, firewall, SELinux, SSH keys), and correct (automated verification and confirmed idempotence).