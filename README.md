# openssh-server-2fa
Bastille template to bootstrap openssh-server with two factor authentication provided by `pam_google_authenticator`, with password authentication turned off, and key authentication turned on.

## Status
[![pipeline status](https://gitlab.com/bastillebsd-templates/openssh-server/badges/master/pipeline.svg)](https://gitlab.com/bastillebsd-templates/openssh-server/commits/master)

## Bootstraping

1) Download the template:
```shell
bastille bootstrap https://gitlab.com/russelltadams/openssh-server-2fa
```

2) If you havn't already created a bastille jail to use, create one. Here's a generic example, see the [Bastille docs](https://bastille.readthedocs.io/en/latest/index.html) for details and customization options.
```shell
bastille create my2fa-bastion 12.2-RELEASE 192.168.100.42 em0
```

## Usage
1) Apply the template to the jail `my2fa-bastion`.
```shell
bastille template my2fa-bastion russelltadams/openssh-server-2fa
```

2) Console into `my2fa-bastion` for post-configuration. 
```shell
bastille console my2fa-bastion
```


## Optional

Bastille templates are rediculously simple and powerful. You can easily add things to suit you that are not already present in this repo, or even layer other templates. Since this template deals with with sshd there are some hooks already present for custimization. You can extend on this easily, just read the Bastille docs. 

### Custom configure sshd and authentication
1) Edit `/etc/ssh/sshd_config` in the template repo for sshd options.
2) Edit `/etc/pam.d/sshd` in the template repo for authentication options.   
3) Use custom `sshd_flags` in jailed ssh service by editing the template sub-commnd `SYSRC` and updating `sshd_flags=""`


## Post apply configuration
1) Console into your new jail, `bastille console my2fa-bastion`, you will be root. 
2) As root add a user that will have remote ssh access to the jail, `adduser myuser`.
3) While still logged in as root, switch to the new user, `login myuser`.
4) As `myuser` add a public key to authorized_keys. `vi .ssh/authorized_keys`. This is the key `myuser` will authenticate against with a matching private key you have on the remote client machine. 
5) As `myuser`, run `google_authenticator` and answer each of the questions and then scan the QR code using the Google Authenticator app, or a similar app like Authy. 
6) Exit the jail console session and ssh to the jail as `myuser`. If your key authentication is successful you will be asked for a `verification code:`, this is the code from Google Authenticator or Authy. If you present the wrong key, your verification code will fail. If you do not offer a key you will be denied, and not asked for password.


