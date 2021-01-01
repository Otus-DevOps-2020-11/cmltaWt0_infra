# cmltaWt0_infra
cmltaWt0 Infra repository


Homework #5
===

Solution 1:
---

```
ssh -i ~/.ssh/appuser -J appuser@<BASTION_IP> appuser@<SOMEINTERNALHOST_IP>
```


Real example:

```
➜ ssh -i ~/.ssh/appuser -J appuser@130.193.51.130 appuser@10.130.0.28
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-26-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Thu Dec 31 09:33:37 2020 from 10.130.0.5


➜ appuser@someinternalhost:~$ ip a show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether d0:0d:ff:d1:9c:37 brd ff:ff:ff:ff:ff:ff
    inet 10.130.0.28/24 brd 10.130.0.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::d20d:ffff:fed1:9c37/64 scope link
       valid_lft forever preferred_lft forever
```

Solution 2:
---

```
➜  cat ~/.ssh/config
Host *
    UseKeychain yes

Host simpleinternalhost
    User appuser
    HostName <SOMEINTERNALHOST_IP>
    ProxyJump bastion

Host bastion
    User appuser
    HostName <BASTION_IP>

➜ ssh simpleinternalhost
```


Real example:
```
➜  cat ~/.ssh/config
Host *
    UseKeychain yes

Host simpleinternalhost
    User appuser
    HostName 10.130.0.28
    ProxyJump bastion

Host bastion
    User appuser
    HostName 130.193.51.130


➜ bash -c "echo alias simpleinternalhost=\'ssh simpleinternalhost\'" >> ~/.aliases

➜ source ~/.aliases

➜ simpleinternalhost
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-26-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Thu Dec 31 10:03:53 2020 from 10.130.0.5

appuser@someinternalhost:~$ ip a show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether d0:0d:ff:d1:9c:37 brd ff:ff:ff:ff:ff:ff
    inet 10.130.0.28/24 brd 10.130.0.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::d20d:ffff:fed1:9c37/64 scope link
       valid_lft forever preferred_lft forever
```


## Configuration data for VPN testing

bastion_IP = 130.193.51.130

someinternalhost_IP = 10.130.0.28


## Configuration

Two VMs:

- bastion
- someinternalhost w/o External IP


Pritunl vpn server is installed on Bastion host and used to access an internal network.

Configuration file: cloud-bastion.ovpn

## Additional task

Using sslip.io and Let's Encrypt implement valid cert for Pritunl admin dashboard:

### Steps:

- install certbot
- pritunl reset-ssl-cert
- re-enter the letsencrypt domain in the pritunl gui


### Test:


```
➜ wget https://130-193-51-130.sslip.io

--2021-01-01 12:11:33--  https://130-193-51-130.sslip.io/
Resolving 130-193-51-130.sslip.io (130-193-51-130.sslip.io)... 130.193.51.130
Connecting to 130-193-51-130.sslip.io (130-193-51-130.sslip.io)|130.193.51.130|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://130-193-51-130.sslip.io/login [following]
--2021-01-01 12:11:34--  https://130-193-51-130.sslip.io/login
Reusing existing connection to 130-193-51-130.sslip.io:443.
HTTP request sent, awaiting response... 200 OK
Length: 72833 (71K) [text/html]
Saving to: ‘index.html’

index.html                     100%[====================================================>]  71,13K   289KB/s    in 0,2s

2021-01-01 12:11:34 (289 KB/s) - ‘index.html’ saved [72833/72833]
```
