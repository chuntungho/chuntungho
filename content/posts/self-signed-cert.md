---
title: Self-signed certificate
date: 2019-10-25 00:00:00.0
updated: 2021-07-27 19:06:02.143
summary: Generate self-signed certifiacte via openssl.
categories: ["Tech"]
tags: ["certificate"]
---


**Prerequisite**: OpenSSL installed on Linux.

Generate CA Root Cert {#generatecarootcert}
-------------------------------------------

```shell
# generate CA key, password required
openssl genrsa -des3 -out ca.key 2048

# remove password if needed
openssl rsa -in ca.key -out ca.key

# generate CA cert, valid for 10 years
# "Common Name" is used for identity, e.g. "CA"
openssl req -new -x509 -key ca.key -out ca.pem -days 3650
```

Issue cert from request {#issuecertfromrequest}
-----------------------------------------------

```shell
# generate private key, similar to CA key, or just use CA key
openssl genrsa -des3 -out ssl.key 2048
openssl rsa -in ssl.key -out ssl.key

# generate cert req
# "Common Name" can be inputed with your domain
openssl req -new -key ssl.key -out ssl.csr

# issue cert by CA Root Cert
openssl x509 -req -days 3650 -in ssl.csr -CA ca.pem -CAkey ca.key -CAcreateserial -out ssl.pem
```

Config in Nginx {#configinnginx}
--------------------------------

```shell
ssl_certificate /path/to/cert/ssl.pem;
ssl_certificate_key /path/to/cert/ssl.key;
```