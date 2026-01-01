---
title: Wildcard cert via Let's Encrypt
date: 2019-05-04 00:00:00.0
updated: 2021-07-27 19:06:12.786
summary: Wildcard cert via Let's Encrypt
categories: ["Tech"]
tags: ["certificate"]
---


Install acme.sh {#installacmesh}
--------------------------------

    $ curl https://get.acme.sh | sh

After installation, `.acme.sh` folder where issued certs will be placed in created under user home path ($HOME), `acme.sh` alias created for manual operation and a daily cron job created for automatical renewal.

Cron job example:

    0 0 * * * "/home/user/.acme.sh"/acme.sh --cron --home "/home/user/.acme.sh" > /dev/null

Generate access key {#generateaccesskey}
----------------------------------------

### For [Aliyun](https://ram.console.aliyun.com/users) {#foraliyun}

Create RAM user only for program and grant it following privileges.

* AliyunDNSFullAccess
* AliyunDomainFullAccess

    $ export Ali_Key="your-api-key"
    $ export Ali_Secret="your-api-secret"

### For [GoDaddy](https://developer.godaddy.com/keys) {#forgodaddy}

    $ export GD_Key="your-api-key"
    $ export GD_Secret="your-api-secret"

Issue certification {#issuecertification}
-----------------------------------------

In order to execute `renew-hook` successfully after renewal, it is recommended to install acme.sh with root user, or move the acme renewal job(via `crontab -l`, `sudo crontab -e`) under root user.

    $ acme.sh --issue --dns dns_ali -d yourdomain.com -d *.yourdomain.com --renew-hook "nginx -s reload"

Nginx config {#nginxconfig}
---------------------------

    ssl on; 
    ssl_certificate /home/user/.acme.sh/yourdomain.com/fullchain.cer;
    ssl_certificate_key /home/user/.acme.sh/yourdomain.com/yourdomain.com.key;

Renew manually {#renewmanually}
-------------------------------

    $ acme.sh --renew -d yourdomain.com --force

Reference: <https://github.com/Neilpang/acme.sh>