## Submission File for Unit 5: Archiving and Logging Data Homework

Please edit and save this file while updating it with the commands and file (configuration and rules) edits you used to solve your homework.

---

### `tar`: Create, extract, compress, and manage tar backup archives

Command to **extract** the `TarDocs.tar` archive to the current directory:

- tar xvvf TarDocs.tar 

Command to **create** the `Javaless_Doc.tar` archive from the `TarDocs/` directory, while excluding the `TarDocs/Documents/Java` directroy:

- tar cvvf Javaless_Docs.tar --exclude=Java ~/Projects/TarDocs/Documents 

Ensuring `Java/` is not in the new `Javaless_Docs.tar` archive:

- tar tvvf Javaless_Docs.tar | grep Java 
**Bonus:** Command to create an incremental archive called `logs_backup_tar.gz` with only changed files to `snapshot.file` for the `/var/log` directory.

- sudo tar cvvWf logs_backup.tar.gz --listed-incremental=snapshot.file.snar /var/log 


#### `tar` Critical Thinking

Why wouldn't you use the options `-x` and `-c` at the same with `tar`?

- because c is to create and x is to excute the tar... so first we need to create it and then execute it. e.g. 1st  tar cvf and then tar xvf 
---

### `cron`: Create, manage and automate various cron jobs

Cron job for backing up the `/var/log/auth.log` file:

- 00 06 * * 3 sudo tar -zcvvf auth_backup.tgz /var/log/auth.log 

--
### `bash scripting`: Write basic bash scripts

Brace expansion command to create the four subdirectories:

- mkdir -p ~/backups/{freemen,diskuse,openlist,freedisk} 

Command and file edit to create `system.sh` (you can copy and paste it here):

- nano system.sh

- Within the script, have the following:

    ```bash
    #!/bin/bash
    # Free memory output to a free_mem.txt file
    free -h > ~/backups/freemem/free_mem.txt

    # Disk usage output to a disk_usage.txt file
    du -h > ~/backups/diskuse/disk_usage.txt

    # List open files to a open_list.txt file
    ls -l > ~/backups/openlist/open_list.txt

    # Free disk space to a free_disk.txt file
    df -h > ~/backups/freedisk/free_disk.txt
    
    ```

Command to make the `system.sh` script executable:

- chmod +x system.sh 

Commands to to confirm script's execution:
- ls -l ~/backups/freemem/free_mem.txt
- ls -l ~/backups/diskuse/disk_usage.txt
- ls -l ~/backups/openlist/open_list.txt
- ls -l ~/backups/freedisk/free_disk.txt

Command to copy `system` to system-wide cron directory:

- @weekly ./system.sh  

---

### `journalctl`: Perform various log filtering techniques

Command to return `journalctl` messages with priorities from emergency to error:

- journalctl -p emerg..err 

Command to return `systemd-journald` messages:

- sudo journalctl -u systemd-journald | less 

Comand to prune archived journal files except the most recent 2:

- sudo journalctl --vacuum-file=2

**Bonus** Command to filter all log messages with priority levels between 0 and 2, output to `/home/sysadmin/Priority_High.txt`

- journalctl -p 0..2 > /home/student/Priority_High.txt

**Bonus 2** Command and file edit to automate the last command in a daily cronjob:

- @daily sudo journalctl -p 0..2 > /home/student/Priority_High.txt

- Within the `crontab` file, add the following:

    ```bash
   @daily sudo journalctl -p 0..2 > /home/student/Priority_High.txt
    ```

---

### `rsyslog`: Priority based log file creation

Command and file edit to record all `mail` log messages, except for `debug` to `/var/log/mail.log`:

- sudo service rsyslog stop
"nano /etc/rsyslog.d/50-default.conf"

- Add within the configuration file:

    ```bash
    'mail.debug           /var/log/mail.log'
    ```

Command and file edit to record all `boot` log messages, except for `info` and `debug` to `/var/log/boot.log`:

- 
sudo service rsyslog stop
"nano /etc/rsyslog.d/50-default.conf"
  

- Add within the configuration file:

    ```bash
    'boot.!={info,debug}        /var/log/boot.log `
    ```

---

### `logrotate`: Manage log file sizes

Command and file edit that backs up authentication messages to `/var/log/auth.log`:

- Run `sudo nano /etc/logrotate.conf` to edit the `logrotate` configuration file.

- Add within the configuration file:

    ```bash
    `/var/log/auth.log {
        weekly
        rotate 7
        notifempty
        missingok
        delaycompress
        
        }`
    ```

---

### BONUS ACTIVITY `auditd`: Check for policy and file violations.

Command to verify `auditd` is active:

- `systemctl status auditd`

Command and file edit to set number of retained logs and maximum log file size:

- `sudo nano /etc/audit/auditd.conf``

- Add within the configuration file:

    ```bash
    # This file controls the configuration of the audit daemon
     max_log_file = 8
     num_logs = 5
    ```

Command and file edit using `auditd` itself to set rules for `/etc/shadow`, `/etc/passwd` and `/var/log/auth.log`:

- `sudo nano /etc/audit/rules.d/audit.rules

- Add within the `rules` file:

    ```bash
      -w /etc/shadow -p wra -k hashpass_audit
      -w /etc/passwd -p wra -k userpass_audit
      -w /var/log/auth.log -p wra -k authlog_audit
    ```

Command to restart `auditd`:

- `sudo systemctl restart auditd`

Command to list all `auditd` rules:

- `sudo auditctl -l`

Command to produce an audit report:

- `sudo aureport -m`

Command to use `auditd` to watch `/var/log/cron`:

- sudo auditctl -w /var/log/cron  

Command to re-verify `auditd` rules:

- `sudo auditctl -l`

---
© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
