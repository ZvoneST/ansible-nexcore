---

## Evidence That Deployed Services Are Accessible

The screenshots below were captured the live deployment and demonstrate each business service working end to end.

User provisioning comes first. Logging in and running `id` for `dev01` and `ops01` on both hosts confirms
that the accounts exist with the correct primary and role groups and the shared `company` membership,
identically across `servera` and `serverb` (Picture 1). This is the data-driven `users` role in action —
a single `nexcore_users` list producing consistent accounts everywhere.

![Verify dev and ops user groups](_01_verify_dev_and_ops_user_groups.png)

*Picture 1. `id dev01` / `id ops01` on servera and serverb — matching UIDs, role groups (`dev`, `ops`) and group membership across both managed hosts.*

The web tier is served correctly. Browsing `http://wiki.nexcore.local` returns the rendered MediaWiki
Main Page (Picture 2), which confirms that Apache, the templated virtual host, DNS resolution, PHP, and
the connection from MediaWiki to the remote MariaDB all work together under enforcing SELinux. The
WordPress site renders at `http://nexcore.local` with the NexCore theme (Picture 3), and the
authenticated `/wp-admin/` dashboard running WordPress 6.5.3 is reachable (Picture 4), proving that the
second virtual host and its database are fully operational.

![MediaWiki main page](_02_mediawiki_main_page_access.png)

*Picture 2. MediaWiki Main Page at `wiki.nexcore.local` — "MediaWiki has been installed", served by Apache on servera.*

![WordPress main page](_03_wordpress_main_page_access.png)

*Picture 3. WordPress public site at `www`/`nexcore.local` — NexCore landing page.*

![WordPress admin dashboard](_04_wordpress_admin_dashboard_access.png)

*Picture 4. WordPress 6.5.3 admin dashboard at `nexcore.local/wp-admin/`.*

DNS is confirmed independently. `dig wiki.nexcore.local` returns `servera`'s address 192.168.50.85 and
`dig MX nexcore.local` returns `10 servera.`, showing that the dnsmasq A and MX records are live
(Picture 5). At the HTTP layer, a `curl` to the wiki virtual host returns 301 (its configured redirect)
and the WordPress site returns 200, confirming that both applications respond correctly over HTTP
(Picture 6).

![DNS records for MediaWiki and mail](_05_dns_records_for_mediawiki_and_mail.png)

*Picture 5. dnsmasq resolving the `wiki` A record to 192.168.50.85 and the domain MX record to `servera`.*

![HTTP status for MediaWiki and WordPress](_06_http_status_for_mediawiki_and_wordpress.png)

*Picture 6. HTTP status codes — `wiki.nexcore.local` → 301, `www.nexcore.local` → 200.*

Shared storage works as designed. Running `exportfs -v` on `serverb` shows `/mnt/shared` exported to
`192.168.50.0/24` (Picture 7). Logging in as `ops01` and `dev01` to write files into `~/Company_Share`
and then listing the share for every user across both servers confirms that the AutoFS mount works and
that the `2770` setgid `company` group lets all staff read and write the same files: a file created by one
user is owned by the `company` group and is visible to everyone (Picture 8).

![NFS export configuration](_07_nfs_export_configuration_check.png)

*Picture 7. `exportfs -v` on serverb — `/mnt/shared` exported to the LAN with `sec=sys,rw`.*

![Shared directory access and permissions](_08_shared_directory_access_and_file_permissions.png)

*Picture 8. Files written by `ops01` and `dev01` appear in every user's auto-mounted `~/Company_Share`, group-owned by `company` with setgid (`drwxrws---`) — confirmed on both servera and serverb.*

Internal email delivery is verified next. Sending mail from `ops01` to `dev02@nexcore.local` and reading
the recipient's `Maildir/new/` shows the message delivered locally by Postfix (Picture 9), and the
Postfix `maillog` records the transaction with `status=sent (delivered to maildir)` (Picture 10).

![Internal email delivery test](_09_internal_email_delivery_test.png)

*Picture 9. Mail from `ops01` → `dev02@nexcore.local` delivered into `dev02`'s Maildir, shown via `cat`.*

![Postfix mail delivery log](_10_postfix_mail_delivery_log_check.png)

*Picture 10. `/var/log/maillog` — Postfix queue ID B45EC809DEB1, `status=sent (delivered to maildir)`.*

Centralised logging is demonstrated by generating a `logger` message on `serverb` and finding it in
`/var/log/remote/serverb.log` on `servera`, which proves the forwarder-to-receiver pipeline over TCP 514
(Picture 11).

![Remote logging from serverb](_11_remote_logging_from_serverb_check.png)

*Picture 11. `nexcore-test` messages emitted on serverb are received and stored on servera under `/var/log/remote/serverb.log`.*

The security posture holds on the live systems. firewalld is running, active, and enabled on both hosts
(Picture 12), and SELinux reports `state: enforcing` on both (Picture 13) — the two non-negotiable
hardening rules.

![firewalld running and enabled](_12_firewalld_running_and_enabled_check.png)

*Picture 12. firewalld `--state` = running, `is-active` = active, `is-enabled` = enabled on servera and serverb.*

![SELinux enforcing mode](_13_selinux_enforcing_mode_check.png)

*Picture 13. SELinux `state: enforcing` (policy `targeted`) on both managed hosts.*

The backup mechanism is in place: the backup directory (`0700`, root-owned), the executable dump script,
and the scheduled `nexcore database backup` cron entry are all present on `serverb` (Picture 14), and the
script performs a compressed `mysqldump` of the wiki and wordpress databases with 14-day retention.

![Database backup script and cron](_14_database_backup_script_and_cron_check.png)

*Picture 14. `/var/backups/nexcore` (0700), the `nexcore-db-backup.sh` script, and the root cron job scheduling the nightly dump.*

Finally, mail retrieval over IMAP completes the picture. Dovecot is active and enabled, serving IMAP from
`maildir:~/Maildir` and listening on port 143 (Picture 15). An end-to-end round trip — sending a message
to `mkt01` and confirming it lands in the Maildir (Picture 16) — is then retrieved through the IMAP server
with `mutt`, where the inbox lists the test messages (Picture 17) and the opened message shows its full
body (Picture 18). This confirms that Postfix delivery and Dovecot retrieval share the same Maildir,
exactly as the single `mail_spool_dir` variable intends.

![Dovecot status and IMAP port](_15_dovecot_service_status_and_imap_port_check.png)

*Picture 15. Dovecot active/enabled, `protocols = imap`, `mail_location = maildir:~/Maildir`, and `ss` showing it listening on `0.0.0.0:143`.*

![Send test mail and verify Maildir delivery](_16_send_test_mail_and_verify_maildir_delivery.png)

*Picture 16. Test mail from `dev01` → `mkt01` delivered into `mkt01`'s `Maildir/new/`.*

![mutt mailbox message list](_17_mutt_mailbox_test_messages_list.png)

*Picture 17. `mutt` connected over IMAP listing the delivered test messages in the mailbox.*

![mutt opened IMAP message](_18_mutt_opened_imap_test_message.png)

*Picture 18. The test message opened in `mutt` over IMAP — "IMAP round-trip test body" from `dev01@nexcore.local`.*