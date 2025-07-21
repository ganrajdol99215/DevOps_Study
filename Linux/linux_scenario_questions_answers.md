#  Advanced Scenario-Based Linux Interview Questions & Answers
**Level: Intermediate to Expert**

---

##  System & Network Troubleshooting

### 1. Login Troubleshooting
**Issue:** User can't log in.  
**Solution:**  
- Check `/var/log/auth.log` or `/var/log/secure`
- Verify shell in `/etc/passwd`
- Ensure account isn't locked: `passwd -S username`
- Check SSH key or password expiration

---

### 2. Disk Full (/) Partition
**Issue:** Root partition is full.  
**Solution:**  
- Use `du -shx /* | sort -h` to find largest dirs  
- Delete old logs: `/var/log/*`, clear apt/yum cache  
- Check deleted files held by processes: `lsof | grep deleted`  
- Reboot if required

---

### 3. CPU Spike
**Issue:** Service consumes 100% CPU.  
**Solution:**  
- Use `top`, `htop`, or `ps -eo pid,ppid,%cpu,%mem,cmd --sort=-%cpu`  
- Check logs for loops or issues  
- Restart or throttle the process

---

### 4. SSH Debugging
**Issue:** Cannot SSH.  
**Solution:**  
- Check network/firewall  
- Confirm `sshd` is running  
- Review `/var/log/auth.log`  
- Test with `ssh -vvv user@host`

---

### 5. Disk Full  Logs
**Issue:** Log files consuming space.  
**Solution:**  
- Rotate logs: `logrotate`  
- Truncate files: `> /var/log/filename`  
- Use `du -sh *` inside `/var/log`

---

### 6. Log Corruption
**Issue:** Junk in log file.  
**Solution:**  
- Check encoding: `file logfile`  
- Check filesystem for errors: `dmesg`, `fsck`  
- Restore from backup

---

### 7. Silent Crontab
**Issue:** Cronjob not running.  
**Solution:**  
- Confirm cron is running: `systemctl status cron`  
- Check job output: `MAILTO`, redirect stdout/stderr  
- Use full paths in script

---

### 8. Package Installation Fails
**Issue:** `yum` or `apt` broken.  
**Solution:**  
- Clean metadata: `yum clean all` / `apt clean`  
- Rebuild db: `rpm --rebuilddb`  
- Fix locks: `rm /var/lib/dpkg/lock*`

---

### 9. Sudo Not Working
**Issue:** Added to sudoers, but denied.  
**Solution:**  
- Re-login to refresh groups  
- Check `/etc/sudoers` with `visudo`  
- Validate group: `id username`

---

### 10. Kernel Panic
**Issue:** System panic.  
**Solution:**  
- View logs via serial or remote KVM  
- Boot into rescue mode or previous kernel  
- Check hardware/RAM  
- Use `kdump`, analyze `/var/crash`

---

### 11. Zombie Process
**Issue:** Defunct process.  
**Solution:**  
- Use `ps aux | grep Z`  
- Find PPID, restart parent  
- Only reboot cleans zombies

---

### 12. Too Many Open Files
**Issue:** FD limit hit.  
**Solution:**  
- View with `lsof`  
- Increase limits in `/etc/security/limits.conf`  
- Tune `fs.file-max` via `sysctl`

---

### 13. High Memory Process
**Issue:** Process uses too much RAM.  
**Solution:**  
- Use `top`, `ps aux --sort=-%mem`  
- Kill if needed: `kill -9 PID`  
- Use `smem`, `pmap` for detailed view

---

### 14. Missing Home Directory
**Issue:** `/home/username` gone.  
**Solution:**  
- Mount from NFS or backup  
- Create with `useradd -m` or `mkdir`, `chown` manually

---

### 15. Time Sync Issue
**Issue:** Incorrect server time.  
**Solution:**  
- Use `timedatectl`  
- Sync with `chronyc sources` or `ntpq -p`  
- Restart time services

---

### 16. Recently Changed Files
**Command:**  
```bash
find / -type f -mmin -10
```

---

### 17. Deleted Files Still Consuming Space
**Issue:** Deleted file in use.  
**Solution:**  
- Use `lsof | grep deleted`  
- Kill process to release

---

### 18. High Load Average
**Issue:** Load high, CPU low.  
**Solution:**  
- Check I/O: `iostat -xz`, `vmstat`  
- Blocked processes: `top -1`, `D` state  
- Use `sar`

---

### 19. NFS Mount Fails
**Solution:**  
- Check network/firewall  
- Use `showmount -e server`, `rpcinfo -p`  
- Restart NFS services

---

### 20. SSH Fails by Hostname
**Issue:** Ping works, SSH fails.  
**Solution:**  
- Check `/etc/hosts`, DNS, `nsswitch.conf`  
- Clear DNS cache

---

##  File System & Storage

### 21. Extend Partition Without Unmount
**Solution:**  
- Use `lvextend` + `resize2fs` or `xfs_growfs`  
- Can be done live on LVM/XFS

---

### 22. Add New Disk
**Steps:**  
- Use `fdisk` or `parted`  
- `mkfs` to format  
- Mount + update `/etc/fstab`

---

### 23. Who is Writing to File
```bash
lsof | grep filename
inotifywait -m /path
```

---

### 24. Recover Deleted File
**Options:**  
- If still in use: `lsof`  
- Use `extundelete`, `debugfs`

---

### 25. Disk I/O Performance
**Tools:**  
- `iotop`, `iostat`, `blktrace`, `sar -d`

---

### 26. Analyze Disk Space
**Tools:**  
- `ncdu`, `du -sh *`, `find /path -size +500M`

---

### 27. Remount as RW
```bash
mount -o remount,rw /mountpoint
```

---

### 28. Deleted File in Use
**Solution:**  
- Use `lsof | grep deleted`  
- Kill the process to release space

---

### 29. Create & Mount Swap File
```bash
fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' >> /etc/fstab
```

---

### 30. Find Unused Large Files
```bash
find / -type f -size +500M -atime +30
```

---

### 31. Fix FS with fsck
```bash
umount /dev/sdX
fsck -y /dev/sdX
```

---

### 32. Inode Exhaustion
**Fix:**  
- Use `df -i`  
- Find dir: `find / -xdev -type f | xargs -n 1 dirname | sort | uniq -c | sort -n`

---

### 33. Fastest Large File Transfer
**Options:**  
- `rsync -z`, `scp -C`, `bbcp`, or `nc` with compression

---

### 34. df vs du Mismatch
**Cause:**  
- Deleted files held by processes  
- Use `lsof | grep deleted`

---

### 35. Largest Directory in /var
```bash
du -sh /var/* | sort -h
```
