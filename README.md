# ansible-role-ssh

Ansible role for setting up openssh.

  - [Supported Platforms](#Supported-Platforms)
  - [Requirements](#Requirements)
  - [Defaults and Variables](#Defaults-and-Variables)
    - [defaults/main/main.yml](#defaultsmainmainyml)
    - [defaults/main/ssh.yml](#defaultsmainsshyml)
    - [defaults/main/sshd.yml](#defaultsmainsshdyml)
    - [defaults/main/sshd_authentication.yml](#defaultsmainsshdauthenticationyml)
    - [defaults/main/sshd_directives.yml](#defaultsmainsshddirectivesyml)
    - [defaults/main/sshd_gssapi.yml](#defaultsmainsshdgssapiyml)
    - [defaults/main/sshd_kerberos.yml](#defaultsmainsshdkerberosyml)
  - [Dependencies](#Dependencies)
  - [Scenarios and example playbooks](#Scenarios-and-example-playbooks)
  - [License and Author](#License-and-Author)
  - [References](#References)

## Supported Platforms

* Amazon Linux 2
* Arch Linux
* Centos 6, 7
* Debian 8, 9, 10
* OpenSuse Leap 15
* OpenSuse Tumbleweed
* Oracle Linux 6, 7
* Ubuntu 16.04, 18.04

## Requirements

Ansible 2.8 or higher is required, as module openssh_keypair is used.

OpenSSH Version 5.7 or above.

## Defaults and Variables

The default values for all variables are stored in the following files:

* defaults/main/main.yml
* defaults/main/ssh.yml
* defaults/main/sshd.yml
* defaults/main/sshd_authentication.yml
* defaults/main/sshd_directives.yml
* defaults/main/sshd_gssapi.yml
* defaults/main/sshd_hostbased.yml
* defaults/main/sshd_kerberos.yml

### defaults/main/main.yml

The file main.yml contains variables with defaults values that affect both, ssh client and sshd (the server).

| variable | default value | description |
| -------- | ------------- | ----------- |
| role_ssh_enabled | false | determine whether role is enabled (true) or not (false) |

The set of allowed algorithms is stored in the dict ssh_algorithms and is used to intersect with the detected supported algorithms:

```yaml
ssh_algorithms:
  ciphers:
    - chacha20-poly1305@openssh.com
    - aes256-gcm@openssh.com
    - aes128-gcm@openssh.com
    - aes256-ctr
    - aes192-ctr
    - aes128-ctr
  kexs:
    - sntrup4591761x25519-sha512@tinyssh.org
    - curve25519-sha256@libssh.org
    - curve25519-sha256
    - diffie-hellman-group18-sha512
    - diffie-hellman-group16-sha512
    - diffie-hellman-group14-sha256
    - diffie-hellman-group-exchange-sha256
  hostkeys:
    - ssh-ed25519-cert-v01@openssh.com
    - rsa-sha2-512-cert-v01@openssh.com
    - rsa-sha2-256-cert-v01@openssh.com
    - ssh-rsa-cert-v01@openssh.com
    - ssh-ed25519
    - rsa-sha2-512
    - rsa-sha2-256
    - ssh-rsa
  macs:
    - hmac-sha2-512-etm@openssh.com
    - hmac-sha2-256-etm@openssh.com
    - umac-128-etm@openssh.com
    - hmac-sha2-512
    - hmac-sha2-256
```

### defaults/main/ssh.yml

| variable | default value | description |
| -------- | ------------- | ----------- |
| ssh_enabled | True | enable configuration of /etc/ssh/ssh_config |
| ssh_deploy_key | '~/.ssh/id_ed25519.pub' | local publickey that is added to remote users authorized_keys file, so you do not lock yourself out, because the default configuration of this role is to only allow pubkey authentication. |
| ssh_conf_backup | 'no' | create a backup when replacing /etc/ssh/ssh_config |
| ssh_host_config | {} | host specific configuration, for example: |
|                 |    | `ssh_host_config:` |
|                 |    | `  testhost:` |
|                 |    | `    X11Forwarding: 'yes'` |
|                 |    | `    GSSAPIAuthentication: 'yes'` |
| ssh_port | '22' | default port ssh tries to connect to |
| ssh_address_family | 'inet' | address family type |
| ssh_challenge_response_authentication | 'yes' | enable challenge response (keyboard-interactive) authentication |
| ssh_enable_ssh_keysign | 'no' | enable ssh-keysign, must be enabled on a client, if you want to do hostbased authentication |
| ssh_gssapi_authentication | 'no' | enable GSSAPI authentication |
| ssh_hostbased_authentication | 'no' | enable hostbased authentication |
| ssh_identity_files | ['\~/.ssh/identity', '\~/.ssh/id_rsa', '\~/.ssh/id_ed25519' ] | where ssh looks for identity files |
| ssh_password_authentication | 'yes' | enable password authentication |
| ssh_pubkey_authentication | 'yes' | enable public key athentication |
| ssh_rekey_limit_data | '512M' | rekey limit (data), this is after 512M of data exchanged |
| ssh_rekey_limit_time | '1800' | rekey limit (time), this is after 1800 seconds |
| ssh_strict_host_key_checking | 'ask' | enable strict host key checking (known_hosts) |
| ssh_test_create_key | False | this should be left to False, as it is used for testing only. When true an ssh key is generated for the remote user root and add to his authorized_keys file. In the pytest this is used to perform a login with `ssh -q localhost exit` to check pubkey authentication. |

### defaults/main/sshd.yml

| variable | default value | description |
| -------- | ------------- | ----------- |
| sshd_enabled | true | enable configuration of /etc/ssh/sshd_config |
| sshd_moduli_file | '/etc/ssh/moduli' | location of DH moduli file |
| sshd_moduli_minimum | 3072 | minimum length od DH parameters |
| sshd_host_key_regenerate | false | regenerate ssh host keys |
| sshd_rsa_keylength | 4096 | length of RSA keys that are created by the role |
| sshd_port | 22 | sshd listen port |
| sshd_address_family | 'inet' | sshd address family |
| sshd_listen_addr_v4 | [ "{{ ansible_default_ipv4.address \| default(ansible_all_ipv4_addresses[0]) }}" ] | IPv4 interface addresses sshd binds to |
| sshd_listen_addr_v6 | [] | IPv6 interface addresses sshd binds to |
| sshd_login_grace_time | '60' |  |
| sshd_permit_root_login | 'no' |  |
| sshd_max_auth_tries | 3 |  |
| sshd_max_sessions | 3 |  |
| sshd_pubkey_auth | 'yes' |  |
... and many more tbd.

### defaults/main/sshd_authentication.yml

### defaults/main/sshd_directives.yml

### defaults/main/sshd_gssapi.yml

### defaults/main/sshd_kerberos.yml

## Dependencies

None.

## Scenarios and example playbooks

This role by default configures pubkey authentication only, using reasonably secure settings.

### Running on localhost

### Public Key Authentication only for remote host

This one is the easiest, just generate a local ssh key with, if you do not have one:

```shell
ssh-keygen -t ed25519
```

Be sure to adjust the host pattern `ssh-servers`to your needs in your inventory file.

Then you can use a playbook like this to deploy:

```yaml
---
# play: test-site
# file: site.yml

- hosts: ssh-servers
  roles:
    - role: ansible-role-ssh
```

If you already have an existing key, i.e. an rsa key, change the following variable:

```yaml
ssh_deploy_key: '~/.ssh/id_rsa.pub'
```

You can do this in your inventory or just from the commandline:

```shell
ansible-playbook site.yml --extra-vars '{"ssh_deploy_key": "~/.ssh/id_rsa.pub"}'
```

## License and Author

* Author:: Jonas Mauer (<jam@kabelmail.net>)
* Copyright:: 2019, Jonas Mauer

Licensed under MIT License;
See LICENSE file in repository.

## References

* [FreeBSD Manual Pages - sshd_config\(5\)](https://www.freebsd.org/cgi/man.cgi?sshd_config)
* [Uni Konstanz - Starke Authentifizioerungsmethoden](https://www.kim.uni-konstanz.de/e-mail-und-internet/it-sicherheit-und-privatsphaere/sicherer-server-it-dienst/linux-fernadministration-mit-pam-und-ssh/starke-authentifizierungsmethoden/)
* [SSH absichern - Stephan Klein](https://klein-gedruckt.de/2015/04/ssh-absichern/)
* [OpenSSH Tip: Check Syntax Errors before Restarting SSHD Server](https://www.cyberciti.biz/tips/checking-openssh-sshd-configuration-syntax-errors.html)
* [BetterCrypto.org: OpenSSH](https://bettercrypto.org/#_openssh)
* [Abe Singer - Hostbased SSH](https://www.usenix.org/system/files/login/articles/09_singer.pdf)
* [DNS-based SSH host key verification](https://ayesh.me/sshfp-verification)
* [Hardening SSH](https://medium.com/@jasonrigden/hardening-ssh-1bcb99cd4cef)
* [How to create an SSH certificate authority](https://jameshfisher.com/2018/03/16/how-to-create-an-ssh-certificate-authority/)
* [SSH Host Key Signing - ein unterschätztes Feature](https://www.sipgate.de/blog/ssh-host-key-signing-ein-unterschaetztes-feature)
