# Local Host

## Generate Twisted Edwards curve SSH key

`ssh-keygen -o -a 100 -t ed25519`

add ssh public key to Digital Ocean droplet upon creation.

## configure ssh for first time SSH connection

`vi ~/.ssh/config`

```
Host newhostname
        Hostname SERVER_IP_ADDRESS
        User root
        Port 22
        IdentityFile ~/.ssh/keyfile
```
# Remote VPS First Time Config

## Secure SSH Access

Secure access to your VPS by updating sshd_config. This will prevent any logins from anything but your authorized ssh key. 

`cd /etc/ssh`

`vi sshd_config`

Change the port to an obscure port number.

```sh
# Package generated configuration file
# See the sshd_config(5) manpage for details

# What ports, IPs and protocols we listen for
Port ##### obscure port number
# Use these options to restrict which interfaces/protocols sshd will bind to
#ListenAddress ::
#ListenAddress 0.0.0.0
Protocol 2
# HostKeys for protocol version 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
#Privilege Separation is turned on for security
UsePrivilegeSeparation yes

# Lifetime and size of ephemeral version 1 server key
KeyRegenerationInterval 3600
ServerKeyBits 1024

# Logging
SyslogFacility AUTH
LogLevel VERBOSE

# Authentication:
LoginGraceTime 120
PermitRootLogin no
StrictModes yes

RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile      %h/.ssh/authorized_keys

# Don't read the user's ~/.rhosts and ~/.shosts files
IgnoreRhosts yes
# For this to work you will also need host keys in /etc/ssh_known_hosts
RhostsRSAAuthentication no
# similar for protocol version 2
HostbasedAuthentication no
# Uncomment if you don't trust ~/.ssh/known_hosts for RhostsRSAAuthentication
#IgnoreUserKnownHosts yes

# To enable empty passwords, change to yes (NOT RECOMMENDED)
PermitEmptyPasswords no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no

# Kerberos options
#KerberosAuthentication no
#KerberosGetAFSToken no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes

# GSSAPI options
#GSSAPIAuthentication no
#GSSAPICleanupCredentials yes

X11Forwarding yes
X11DisplayOffset 10
PrintMotd no
PrintLastLog yes
TCPKeepAlive yes
#UseLogin no

#MaxStartups 10:30:60
Banner /etc/issue.net

AllowUsers core
DenyUsers  root
# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

Subsystem sftp /usr/lib/openssh/sftp-server

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin yes
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM no
```

reset the ssh service

`service sshd restart`

## Add Core System Administration User

`adduser core`

make the new user a super user

`usermod -aG sudo core`

add an authorized_keys file to the new users .ssh folder

`su - core`

`mkdir .ssh`

`cd .ssh`

`vi authorized_keys`

paste the contents of your public ssh key in the authorized_key file

Harden the users permissions for OpenSSH standards

`chmod go-w ~/`

`chmod 700 ~/.ssh`

`chmod 600 ~/.ssh/authorized_keys`

## *Before exiting your current root session start another terminal and ssh into the VPS with the new configuration. If something is misconfigured you can back up and troubleshoot within the initial root session*

# Local Host

## Configure local SSH for new remote sshd_config 

`vi ~/.ssh/config`

```
Host newhostname
        Hostname SERVER_IP_ADDRESS
        User core
        Port ##### new obscure port
        IdentityFile ~/.ssh/keyfile
```

`ssh newhostname`

# Next Steps

Digital Ocean offers simple Firewalls that can be configured to attach to your VPS's virtual NIC. Take advantage of this and add a rule that only allows incoming traffic to the new obscure SSH port.