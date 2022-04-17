#Курсовая работа по итогам модуля "DevOps и системное администрирование"

##Задание
(1) Создайте виртуальную машину Linux.

Делаю на VPS, надеюсь это не будет считаться ошибкой.

(2) Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.

```
root@cadcixztkv:~# apt install ufw
Reading package lists... Done
Building dependency tree
Reading state information... Done
ufw is already the newest version (0.36-6ubuntu1).
ufw set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 12 not upgraded.
root@cadcixztkv:~# ufw app list
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
root@cadcixztkv:~# ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
root@cadcixztkv:~# ufw allow 'Nginx Full'
Rule added
Rule added (v6)
root@cadcixztkv:~# ufw allow 'OpenSSH'
Rule added
Rule added (v6)
root@cadcixztkv:~# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:7e:c9:a0:a0:14 brd ff:ff:ff:ff:ff:ff
    inet 62.113.102.35/32 brd 62.113.102.35 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::27e:c9ff:fea0:a014/64 scope link
       valid_lft forever preferred_lft forever
root@cadcixztkv:~# ufw allow in on lo
Rule added
Rule added (v6)
root@cadcixztkv:~# ufw status
Status: active

To                         Action      From
--                         ------      ----
Nginx Full                 ALLOW       Anywhere
OpenSSH                    ALLOW       Anywhere
Anywhere on lo             ALLOW       Anywhere
Nginx Full (v6)            ALLOW       Anywhere (v6)
OpenSSH (v6)               ALLOW       Anywhere (v6)
Anywhere (v6) on lo        ALLOW       Anywhere (v6)


```

(3) Установите hashicorp vault (инструкция по ссылке).

```
root@cadcixztkv:~# apt install jq
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libjq1
The following NEW packages will be installed:
  jq libjq1
0 upgraded, 2 newly installed, 0 to remove and 18 not upgraded.
Need to get 171 kB of archives.
After this operation, 450 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 libjq1 amd64 1.6-1ubuntu0.20.04.1 [121 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 jq amd64 1.6-1ubuntu0.20.04.1 [50.2 kB]
Fetched 171 kB in 1s (147 kB/s)
Selecting previously unselected package libjq1:amd64.
(Reading database ... 97879 files and directories currently installed.)
Preparing to unpack .../libjq1_1.6-1ubuntu0.20.04.1_amd64.deb ...
Unpacking libjq1:amd64 (1.6-1ubuntu0.20.04.1) ...
Selecting previously unselected package jq.
Preparing to unpack .../jq_1.6-1ubuntu0.20.04.1_amd64.deb ...
Unpacking jq (1.6-1ubuntu0.20.04.1) ...
Setting up libjq1:amd64 (1.6-1ubuntu0.20.04.1) ...
Setting up jq (1.6-1ubuntu0.20.04.1) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.7) ...
root@cadcixztkv:~# ufw status
Status: inactive
root@cadcixztkv:~# cd /tmp/ && wget https://hashicorp-releases.website.yandexcloud.net/vault/1.9.3/vault_1.9.3_linux_amd64.zip
--2022-04-15 18:05:17--  https://hashicorp-releases.website.yandexcloud.net/vault/1.9.3/vault_1.9.3_linux_amd64.zip
Resolving hashicorp-releases.website.yandexcloud.net (hashicorp-releases.website.yandexcloud.net)... 213.180.193.247, 2a02:6b8::1da
Connecting to hashicorp-releases.website.yandexcloud.net (hashicorp-releases.website.yandexcloud.net)|213.180.193.247|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 68962699 (66M) [application/zip]
Saving to: ‘vault_1.9.3_linux_amd64.zip’

vault_1.9.3_linux_amd64.zip       100%[============================================================>]  65.77M  12.8MB/s    in 4.2s

Last-modified header invalid -- time-stamp ignored.
2022-04-15 18:05:22 (15.6 MB/s) - ‘vault_1.9.3_linux_amd64.zip’ saved [68962699/68962699]

root@cadcixztkv:/tmp# unzip vault_1.9.3_linux_amd64.zip’
unzip:  cannot find or open vault_1.9.3_linux_amd64.zip’, vault_1.9.3_linux_amd64.zip’.zip or vault_1.9.3_linux_amd64.zip’.ZIP.
root@cadcixztkv:/tmp# unzip vault_1.9.3_linux_amd64.zip
Archive:  vault_1.9.3_linux_amd64.zip
  inflating: vault
root@cadcixztkv:/tmp# mv vault /usr/local/bin/
root@cadcixztkv:/tmp# cd /usr/local/bin/
root@cadcixztkv:/usr/local/bin# ls
rar  unrar  vault


```

(4) Cоздайте центр сертификации по инструкции (ссылка) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).

```
root@cadcixztkv:~# VAULT_UI=true vault server -dev -dev-root-token-id="root"
==> Vault server configuration:

             Api Address: http://127.0.0.1:8200
                     Cgo: disabled
         Cluster Address: https://127.0.0.1:8201
              Go Version: go1.17.5
              Listener 1: tcp (addr: "127.0.0.1:8200", cluster address: "127.0.0.1:8201", max_request_duration: "1m30s", max_request_size: "33554432", tls: "disabled")
               Log Level: info
                   Mlock: supported: true, enabled: false
           Recovery Mode: false
                 Storage: inmem
                 Version: Vault v1.9.3
             Version Sha: 7dbdd57243a0d8d9d9e07cd01eb657369f8e1b8a

==> Vault server started! Log data will stream in below:

2022-04-15T18:07:21.583Z [INFO]  proxy environment: http_proxy="\"\"" https_proxy="\"\"" no_proxy="\"\""
2022-04-15T18:07:21.584Z [WARN]  no `api_addr` value specified in config or in VAULT_API_ADDR; falling back to detection if possible, but this value should be manually set
2022-04-15T18:07:21.584Z [INFO]  core: Initializing VersionTimestamps for core
2022-04-15T18:07:21.585Z [INFO]  core: security barrier not initialized
2022-04-15T18:07:21.585Z [INFO]  core: security barrier initialized: stored=1 shares=1 threshold=1
2022-04-15T18:07:21.586Z [INFO]  core: post-unseal setup starting
2022-04-15T18:07:21.588Z [INFO]  core: loaded wrapping token key
2022-04-15T18:07:21.588Z [INFO]  core: Recorded vault version: vault version=1.9.3 upgrade time="2022-04-15 18:07:21.588587117 +0000 UTC m=+0.071667465"
2022-04-15T18:07:21.588Z [INFO]  core: successfully setup plugin catalog: plugin-directory="\"\""
2022-04-15T18:07:21.588Z [INFO]  core: no mounts; adding default mount table
2022-04-15T18:07:21.591Z [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2022-04-15T18:07:21.592Z [INFO]  core: successfully mounted backend: type=system path=sys/
2022-04-15T18:07:21.592Z [INFO]  core: successfully mounted backend: type=identity path=identity/
2022-04-15T18:07:21.597Z [INFO]  core: successfully enabled credential backend: type=token path=token/
2022-04-15T18:07:21.602Z [INFO]  core: restoring leases
2022-04-15T18:07:21.606Z [INFO]  rollback: starting rollback manager
2022-04-15T18:07:21.614Z [INFO]  identity: entities restored
2022-04-15T18:07:21.614Z [INFO]  identity: groups restored
2022-04-15T18:07:21.614Z [INFO]  core: post-unseal setup complete
2022-04-15T18:07:21.615Z [INFO]  core: root token generated
2022-04-15T18:07:21.615Z [INFO]  core: pre-seal teardown starting
2022-04-15T18:07:21.615Z [INFO]  expiration: lease restore complete
2022-04-15T18:07:21.625Z [INFO]  rollback: stopping rollback manager
2022-04-15T18:07:21.625Z [INFO]  core: pre-seal teardown complete
2022-04-15T18:07:21.625Z [INFO]  core.cluster-listener.tcp: starting listener: listener_address=127.0.0.1:8201
2022-04-15T18:07:21.626Z [INFO]  core.cluster-listener: serving cluster requests: cluster_listen_address=127.0.0.1:8201
2022-04-15T18:07:21.626Z [INFO]  core: post-unseal setup starting
2022-04-15T18:07:21.626Z [INFO]  core: loaded wrapping token key
2022-04-15T18:07:21.626Z [INFO]  core: successfully setup plugin catalog: plugin-directory="\"\""
2022-04-15T18:07:21.626Z [INFO]  core: successfully mounted backend: type=system path=sys/
2022-04-15T18:07:21.627Z [INFO]  core: successfully mounted backend: type=identity path=identity/
2022-04-15T18:07:21.627Z [INFO]  core: successfully mounted backend: type=cubbyhole path=cubbyhole/
2022-04-15T18:07:21.627Z [INFO]  core: successfully enabled credential backend: type=token path=token/
2022-04-15T18:07:21.628Z [INFO]  core: restoring leases
2022-04-15T18:07:21.628Z [INFO]  expiration: lease restore complete
2022-04-15T18:07:21.628Z [INFO]  rollback: starting rollback manager
2022-04-15T18:07:21.628Z [INFO]  identity: entities restored
2022-04-15T18:07:21.628Z [INFO]  identity: groups restored
2022-04-15T18:07:21.628Z [INFO]  core: post-unseal setup complete
2022-04-15T18:07:21.628Z [INFO]  core: vault is unsealed
2022-04-15T18:07:21.630Z [INFO]  expiration: revoked lease: lease_id=auth/token/root/h577686a71eb0844cc1b36bb6edaf6de104cdd6205ec483ebe8bddc07f0d02207
2022-04-15T18:07:21.633Z [INFO]  core: successful mount: namespace="\"\"" path=secret/ type=kv
2022-04-15T18:07:21.636Z [INFO]  secrets.kv.kv_e7dc3891: collecting keys to upgrade
2022-04-15T18:07:21.636Z [INFO]  secrets.kv.kv_e7dc3891: done collecting keys: num_keys=1
2022-04-15T18:07:21.636Z [INFO]  secrets.kv.kv_e7dc3891: upgrading keys finished
WARNING! dev mode is enabled! In this mode, Vault runs entirely in-memory
and starts unsealed with a single unseal key. The root token is already
authenticated to the CLI, so you can immediately begin using Vault.

You may need to set the following environment variable:

    $ export VAULT_ADDR='http://127.0.0.1:8200'

The unseal key and root token are displayed below in case you want to
seal/unseal the Vault or re-authenticate.

Unseal Key: DOkYmPO/sUezfTUQOlxdRbeWEOO+efg+lhv4J7txUvA=
Root Token: root

Development mode should NOT be used in production installations!

2022-04-15T18:09:35.241Z [INFO]  core: successful mount: namespace="\"\"" path=pki/ type=pki
2022-04-15T18:11:14.149Z [INFO]  core: successful mount: namespace="\"\"" path=pki_int/ type=pki

```

```
root@cadcixztkv:~# export VAULT_ADDR=http://127.0.0.1:8200
root@cadcixztkv:~# vault status
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    1
Threshold       1
Version         1.9.3
Storage Type    inmem
Cluster Name    vault-cluster-2514a5e0
Cluster ID      4460367f-2a6f-a331-5551-77c9b23d2e2a
HA Enabled      false
root@cadcixztkv:~# vault login root
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                root
token_accessor       hQT5vxiCDV6r14UfizgwqtgQ
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
root@cadcixztkv:~# vault secrets enable pki
Success! Enabled the pki secrets engine at: pki/
root@cadcixztkv:~# vault write -format=json pki/root/generate/internal \
>  common_name="pki-ca-root" ttl=87600h | tee \
> >(jq -r .data.certificate > ca.pem) \
> >(jq -r .data.issuing_ca > issuing_ca.pem) \
> >(jq -r .data.private_key > ca-key.pem)
{
  "request_id": "e736cc52-d835-4d75-d9b3-3c45168add97",
  "lease_id": "",
  "lease_duration": 0,
  "renewable": false,
  "data": {
    "certificate": "-----BEGIN CERTIFICATE-----\nMIIDNTCCAh2gAwIBAgIUXUtSRoiL0VQNLJzjSIsD2QpNHuQwDQYJKoZIhvcNAQEL\nBQAwFjEUMBIGA1UEAxMLcGtpLWNhLXJvb3QwHhcNMjIwNDE1MTgwOTQ5WhcNMjIw\nNTE3MTgxMDE4WjAWMRQwEgYDVQQDEwtwa2ktY2Etcm9vdDCCASIwDQYJKoZIhvcN\nAQEBBQADggEPADCCAQoCggEBAL996889MlQOXD+A8h/gVF7kZzXkOHgt1zXTGuwE\nACbjhidmovEk4HUO0J3E5cq3SeXexb6M5fGPeLv8DDRvuCo3Xee4HHFEK7mdtsoE\nOcOlpY5SIoHVP3ySjQDU7lGb8sS6m7ldj88yIXpzSA2OY2j3iVx3Ld8LnG3OJ7WV\n6SA+p+qIMNKikhDfdlbmnsbtwbLfUsMM+NFLN0xgZmuLwNKo1rQuiGM9LMMjvj/H\ngyMNrxtgYCBjGHCWaIVU4EwzD5l/zS7ULtnhSx0uGFeHhPxQa9xTfv+vHcw6X4W3\nm84O0c5I1FFt6BCfx4KvslN8P6xOFNeBHsCdZ8PDDCdzNIUCAwEAAaN7MHkwDgYD\nVR0PAQH/BAQDAgEGMA8GA1UdEwEB/wQFMAMBAf8wHQYDVR0OBBYEFBR9AI56r0Ng\nEC9yf7nWkaRtYwEPMB8GA1UdIwQYMBaAFBR9AI56r0NgEC9yf7nWkaRtYwEPMBYG\nA1UdEQQPMA2CC3BraS1jYS1yb290MA0GCSqGSIb3DQEBCwUAA4IBAQA/k6WHfXCj\nJwKY+K9T26NLs/O8OkpBhhVPf5t1po/Q1YaCD8ro2q3fULS9GHpqiBrS/JBr5nt5\nwoSVHSiMwm8wpMgyQoSkzcm9tkzHjq78V2Qag/dZANs+qwIftsmqvpK8poK3ntcp\nfIvLrIxb/M0+iRee35+coTpU98+ntysCVhRq0rYWtsPFWYT15RIJG/NLB1wlDd2w\nHtL1YEi4dOG6j/F2Oqd3fL4dkSIliGk8FCHkdOeiKPzLCYBhOzTyg87UyF8f3koq\nw6rRHSWiA0IMy6/cC7xrSwVPuVCqw/Wa5dN3TKzHEL29cVoOZ9wThIMvDWu5yEYU\nuaM0/LbSUQyK\n-----END CERTIFICATE-----",
    "expiration": 1652811018,
    "issuing_ca": "-----BEGIN CERTIFICATE-----\nMIIDNTCCAh2gAwIBAgIUXUtSRoiL0VQNLJzjSIsD2QpNHuQwDQYJKoZIhvcNAQEL\nBQAwFjEUMBIGA1UEAxMLcGtpLWNhLXJvb3QwHhcNMjIwNDE1MTgwOTQ5WhcNMjIw\nNTE3MTgxMDE4WjAWMRQwEgYDVQQDEwtwa2ktY2Etcm9vdDCCASIwDQYJKoZIhvcN\nAQEBBQADggEPADCCAQoCggEBAL996889MlQOXD+A8h/gVF7kZzXkOHgt1zXTGuwE\nACbjhidmovEk4HUO0J3E5cq3SeXexb6M5fGPeLv8DDRvuCo3Xee4HHFEK7mdtsoE\nOcOlpY5SIoHVP3ySjQDU7lGb8sS6m7ldj88yIXpzSA2OY2j3iVx3Ld8LnG3OJ7WV\n6SA+p+qIMNKikhDfdlbmnsbtwbLfUsMM+NFLN0xgZmuLwNKo1rQuiGM9LMMjvj/H\ngyMNrxtgYCBjGHCWaIVU4EwzD5l/zS7ULtnhSx0uGFeHhPxQa9xTfv+vHcw6X4W3\nm84O0c5I1FFt6BCfx4KvslN8P6xOFNeBHsCdZ8PDDCdzNIUCAwEAAaN7MHkwDgYD\nVR0PAQH/BAQDAgEGMA8GA1UdEwEB/wQFMAMBAf8wHQYDVR0OBBYEFBR9AI56r0Ng\nEC9yf7nWkaRtYwEPMB8GA1UdIwQYMBaAFBR9AI56r0NgEC9yf7nWkaRtYwEPMBYG\nA1UdEQQPMA2CC3BraS1jYS1yb290MA0GCSqGSIb3DQEBCwUAA4IBAQA/k6WHfXCj\nJwKY+K9T26NLs/O8OkpBhhVPf5t1po/Q1YaCD8ro2q3fULS9GHpqiBrS/JBr5nt5\nwoSVHSiMwm8wpMgyQoSkzcm9tkzHjq78V2Qag/dZANs+qwIftsmqvpK8poK3ntcp\nfIvLrIxb/M0+iRee35+coTpU98+ntysCVhRq0rYWtsPFWYT15RIJG/NLB1wlDd2w\nHtL1YEi4dOG6j/F2Oqd3fL4dkSIliGk8FCHkdOeiKPzLCYBhOzTyg87UyF8f3koq\nw6rRHSWiA0IMy6/cC7xrSwVPuVCqw/Wa5dN3TKzHEL29cVoOZ9wThIMvDWu5yEYU\nuaM0/LbSUQyK\n-----END CERTIFICATE-----",
    "serial_number": "5d:4b:52:46:88:8b:d1:54:0d:2c:9c:e3:48:8b:03:d9:0a:4d:1e:e4"
  },
  "warnings": null
}
root@cadcixztkv:~# curl -s http://localhost:8200/v1/pki/ca/pem | openssl x509 -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            5d:4b:52:46:88:8b:d1:54:0d:2c:9c:e3:48:8b:03:d9:0a:4d:1e:e4
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = pki-ca-root
        Validity
            Not Before: Apr 15 18:09:49 2022 GMT
            Not After : May 17 18:10:18 2022 GMT
        Subject: CN = pki-ca-root
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:bf:7d:eb:cf:3d:32:54:0e:5c:3f:80:f2:1f:e0:
                    54:5e:e4:67:35:e4:38:78:2d:d7:35:d3:1a:ec:04:
                    00:26:e3:86:27:66:a2:f1:24:e0:75:0e:d0:9d:c4:
                    e5:ca:b7:49:e5:de:c5:be:8c:e5:f1:8f:78:bb:fc:
                    0c:34:6f:b8:2a:37:5d:e7:b8:1c:71:44:2b:b9:9d:
                    b6:ca:04:39:c3:a5:a5:8e:52:22:81:d5:3f:7c:92:
                    8d:00:d4:ee:51:9b:f2:c4:ba:9b:b9:5d:8f:cf:32:
                    21:7a:73:48:0d:8e:63:68:f7:89:5c:77:2d:df:0b:
                    9c:6d:ce:27:b5:95:e9:20:3e:a7:ea:88:30:d2:a2:
                    92:10:df:76:56:e6:9e:c6:ed:c1:b2:df:52:c3:0c:
                    f8:d1:4b:37:4c:60:66:6b:8b:c0:d2:a8:d6:b4:2e:
                    88:63:3d:2c:c3:23:be:3f:c7:83:23:0d:af:1b:60:
                    60:20:63:18:70:96:68:85:54:e0:4c:33:0f:99:7f:
                    cd:2e:d4:2e:d9:e1:4b:1d:2e:18:57:87:84:fc:50:
                    6b:dc:53:7e:ff:af:1d:cc:3a:5f:85:b7:9b:ce:0e:
                    d1:ce:48:d4:51:6d:e8:10:9f:c7:82:af:b2:53:7c:
                    3f:ac:4e:14:d7:81:1e:c0:9d:67:c3:c3:0c:27:73:
                    34:85
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Certificate Sign, CRL Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier:
                14:7D:00:8E:7A:AF:43:60:10:2F:72:7F:B9:D6:91:A4:6D:63:01:0F
            X509v3 Authority Key Identifier:
                keyid:14:7D:00:8E:7A:AF:43:60:10:2F:72:7F:B9:D6:91:A4:6D:63:01:0F

            X509v3 Subject Alternative Name:
                DNS:pki-ca-root
    Signature Algorithm: sha256WithRSAEncryption
         3f:93:a5:87:7d:70:a3:27:02:98:f8:af:53:db:a3:4b:b3:f3:
         bc:3a:4a:41:86:15:4f:7f:9b:75:a6:8f:d0:d5:86:82:0f:ca:
         e8:da:ad:df:50:b4:bd:18:7a:6a:88:1a:d2:fc:90:6b:e6:7b:
         79:c2:84:95:1d:28:8c:c2:6f:30:a4:c8:32:42:84:a4:cd:c9:
         bd:b6:4c:c7:8e:ae:fc:57:64:1a:83:f7:59:00:db:3e:ab:02:
         1f:b6:c9:aa:be:92:bc:a6:82:b7:9e:d7:29:7c:8b:cb:ac:8c:
         5b:fc:cd:3e:89:17:9e:df:9f:9c:a1:3a:54:f7:cf:a7:b7:2b:
         02:56:14:6a:d2:b6:16:b6:c3:c5:59:84:f5:e5:12:09:1b:f3:
         4b:07:5c:25:0d:dd:b0:1e:d2:f5:60:48:b8:74:e1:ba:8f:f1:
         76:3a:a7:77:7c:be:1d:91:22:25:88:69:3c:14:21:e4:74:e7:
         a2:28:fc:cb:09:80:61:3b:34:f2:83:ce:d4:c8:5f:1f:de:4a:
         2a:c3:aa:d1:1d:25:a2:03:42:0c:cb:af:dc:0b:bc:6b:4b:05:
         4f:b9:50:aa:c3:f5:9a:e5:d3:77:4c:ac:c7:10:bd:bd:71:5a:
         0e:67:dc:13:84:83:2f:0d:6b:b9:c8:46:14:b9:a3:34:fc:b6:
         d2:51:0c:8a
-----BEGIN CERTIFICATE-----
MIIDNTCCAh2gAwIBAgIUXUtSRoiL0VQNLJzjSIsD2QpNHuQwDQYJKoZIhvcNAQEL
BQAwFjEUMBIGA1UEAxMLcGtpLWNhLXJvb3QwHhcNMjIwNDE1MTgwOTQ5WhcNMjIw
NTE3MTgxMDE4WjAWMRQwEgYDVQQDEwtwa2ktY2Etcm9vdDCCASIwDQYJKoZIhvcN
AQEBBQADggEPADCCAQoCggEBAL996889MlQOXD+A8h/gVF7kZzXkOHgt1zXTGuwE
ACbjhidmovEk4HUO0J3E5cq3SeXexb6M5fGPeLv8DDRvuCo3Xee4HHFEK7mdtsoE
OcOlpY5SIoHVP3ySjQDU7lGb8sS6m7ldj88yIXpzSA2OY2j3iVx3Ld8LnG3OJ7WV
6SA+p+qIMNKikhDfdlbmnsbtwbLfUsMM+NFLN0xgZmuLwNKo1rQuiGM9LMMjvj/H
gyMNrxtgYCBjGHCWaIVU4EwzD5l/zS7ULtnhSx0uGFeHhPxQa9xTfv+vHcw6X4W3
m84O0c5I1FFt6BCfx4KvslN8P6xOFNeBHsCdZ8PDDCdzNIUCAwEAAaN7MHkwDgYD
VR0PAQH/BAQDAgEGMA8GA1UdEwEB/wQFMAMBAf8wHQYDVR0OBBYEFBR9AI56r0Ng
EC9yf7nWkaRtYwEPMB8GA1UdIwQYMBaAFBR9AI56r0NgEC9yf7nWkaRtYwEPMBYG
A1UdEQQPMA2CC3BraS1jYS1yb290MA0GCSqGSIb3DQEBCwUAA4IBAQA/k6WHfXCj
JwKY+K9T26NLs/O8OkpBhhVPf5t1po/Q1YaCD8ro2q3fULS9GHpqiBrS/JBr5nt5
woSVHSiMwm8wpMgyQoSkzcm9tkzHjq78V2Qag/dZANs+qwIftsmqvpK8poK3ntcp
fIvLrIxb/M0+iRee35+coTpU98+ntysCVhRq0rYWtsPFWYT15RIJG/NLB1wlDd2w
HtL1YEi4dOG6j/F2Oqd3fL4dkSIliGk8FCHkdOeiKPzLCYBhOzTyg87UyF8f3koq
w6rRHSWiA0IMy6/cC7xrSwVPuVCqw/Wa5dN3TKzHEL29cVoOZ9wThIMvDWu5yEYU
uaM0/LbSUQyK
-----END CERTIFICATE-----
root@cadcixztkv:~# vault secrets enable -path pki_int pki
Success! Enabled the pki secrets engine at: pki_int/
root@cadcixztkv:~# vault write -format=json pki_int/intermediate/generate/internal \
> common_name="pki-ca-int" ttl=43800h | tee \
> >(jq -r .data.csr > pki_int.csr) \
> >(jq -r .data.private_key > pki_int.pem)
{
  "request_id": "2b47f878-8fdc-eb69-d8d3-7189c4e5a8af",
  "lease_id": "",
  "lease_duration": 0,
  "renewable": false,
  "data": {
    "csr": "-----BEGIN CERTIFICATE REQUEST-----\nMIICgjCCAWoCAQAwFTETMBEGA1UEAxMKcGtpLWNhLWludDCCASIwDQYJKoZIhvcN\nAQEBBQADggEPADCCAQoCggEBAJbFb1zoKcIXbdJ2Wbob0Ym7bMtikSJ/k2xXSnTB\nM/fAi1SG85ANvXlsTiEnSEDI/rvYCCIwffF41N0DcoutHp43DJ7NiFme7m8coKSz\nEfQ39ZwTepDnHLxggV1RjHqN+mYJgE7K+WQl3zEsKk57cc/0DFDWlDQPWN/NKmd/\nn6kr3XHTItDCYfYumXPZ7mT7EQbl/onin4GPOpTbL8C8KWXobkbJ80PMkxZ89VdS\nZh5u0R4XcNaMmerEu8celj8AdUDZtss/HqUNHL6oddbPn8jTeyDPOv15ETxzOBDz\naRb0e/I6ov+Ij/xVQalIj/+GX1s9RiF92q2sPj+z9j4rIAsCAwEAAaAoMCYGCSqG\nSIb3DQEJDjEZMBcwFQYDVR0RBA4wDIIKcGtpLWNhLWludDANBgkqhkiG9w0BAQsF\nAAOCAQEAa7EvQcRtP93SXM93eIsbEnyEAWGZB4bjVp1hq+ciP4kPlnF0LPVuJUi7\nPS8KNsY8Dh6L9WenNyEs5vBkvgVrRK6xPnlmnrPCvNyAQ9wKKluJInqGM0EHtgE5\nvU5iVtkS7Jn4fffqPLK8AlVDh7XjwXSPGik1pIw5WfTIF5YGaEF+ETqIGOlHFx5N\n85Piq+ucQWketFadFYLzkASF6vZ79OJTNxkZehBLA2ssCOfWAM1/kMq8XsvmNErH\nCLhRuBDuz5OXFev1kvfVnsa9Dvg/jaMEd6wfsfpRzfZe54sD4JmrwObj7OO1AcO+\nfQLR4sHp037dNfh+qweCbIfiPe6GOg==\n-----END CERTIFICATE REQUEST-----"
  },
  "warnings": null
}
root@cadcixztkv:~# vault write -format=json pki/root/sign-intermediate \
> csr=@pki_int.csr \
> common_name="pki-ca-int" ttl=43800h | tee \
> >(jq -r .data.certificate > pki_int.pem) \
> >(jq -r .data.issuing_ca > pki_int_issuing_ca.pem)
{
  "request_id": "6458e5b0-2546-bcba-5009-f16f6804a072",
  "lease_id": "",
  "lease_duration": 0,
  "renewable": false,
  "data": {
    "certificate": "-----BEGIN CERTIFICATE-----\nMIIDMzCCAhugAwIBAgIUT/4MfhC/qvEjnVOLXhd4xb8JgOEwDQYJKoZIhvcNAQEL\nBQAwFjEUMBIGA1UEAxMLcGtpLWNhLXJvb3QwHhcNMjIwNDE1MTgxMTQzWhcNMjIw\nNTE3MTgxMjEzWjAVMRMwEQYDVQQDEwpwa2ktY2EtaW50MIIBIjANBgkqhkiG9w0B\nAQEFAAOCAQ8AMIIBCgKCAQEAlsVvXOgpwhdt0nZZuhvRibtsy2KRIn+TbFdKdMEz\n98CLVIbzkA29eWxOISdIQMj+u9gIIjB98XjU3QNyi60enjcMns2IWZ7ubxygpLMR\n9Df1nBN6kOccvGCBXVGMeo36ZgmATsr5ZCXfMSwqTntxz/QMUNaUNA9Y380qZ3+f\nqSvdcdMi0MJh9i6Zc9nuZPsRBuX+ieKfgY86lNsvwLwpZehuRsnzQ8yTFnz1V1Jm\nHm7RHhdw1oyZ6sS7xx6WPwB1QNm2yz8epQ0cvqh11s+fyNN7IM86/XkRPHM4EPNp\nFvR78jqi/4iP/FVBqUiP/4ZfWz1GIX3araw+P7P2PisgCwIDAQABo3oweDAOBgNV\nHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4EFgQUKfxnhlxe2xBo\neAEQ2VHPvdlLEcAwHwYDVR0jBBgwFoAUFH0AjnqvQ2AQL3J/udaRpG1jAQ8wFQYD\nVR0RBA4wDIIKcGtpLWNhLWludDANBgkqhkiG9w0BAQsFAAOCAQEApztwZuhBeSej\nPBV2rqaY0gpSu4hbZ3YDKoeiowpGziq26YidtCb6+rzZA8aHi09lZBJFPUDD2FRc\ngeKmHPCRNNDpgd45JZyraE7kkdx93rWO4+wR1ZBDauimyKjTtzMC2b/gNpIxZ2la\nkNGM39QjbMg/VnOuArW+oGHDQw6wYaZY9zYfHcyHT0+E9mDRdDO/i2cw7AwLJI2h\n5pWEcYt9bLo4GaBldpFYUq1jCnA4cYfw3QXi5J8ZMjTzatn+v7epb2Vj6aSbSc/Q\nYmTdMNWU6G9/KI3nh7PHDEB0f/UriepXPSFGD6O7VBjopXJ2P7Xf2AjozuAxIJha\n+PADmYUcgg==\n-----END CERTIFICATE-----",
    "expiration": 1652811133,
    "issuing_ca": "-----BEGIN CERTIFICATE-----\nMIIDNTCCAh2gAwIBAgIUXUtSRoiL0VQNLJzjSIsD2QpNHuQwDQYJKoZIhvcNAQEL\nBQAwFjEUMBIGA1UEAxMLcGtpLWNhLXJvb3QwHhcNMjIwNDE1MTgwOTQ5WhcNMjIw\nNTE3MTgxMDE4WjAWMRQwEgYDVQQDEwtwa2ktY2Etcm9vdDCCASIwDQYJKoZIhvcN\nAQEBBQADggEPADCCAQoCggEBAL996889MlQOXD+A8h/gVF7kZzXkOHgt1zXTGuwE\nACbjhidmovEk4HUO0J3E5cq3SeXexb6M5fGPeLv8DDRvuCo3Xee4HHFEK7mdtsoE\nOcOlpY5SIoHVP3ySjQDU7lGb8sS6m7ldj88yIXpzSA2OY2j3iVx3Ld8LnG3OJ7WV\n6SA+p+qIMNKikhDfdlbmnsbtwbLfUsMM+NFLN0xgZmuLwNKo1rQuiGM9LMMjvj/H\ngyMNrxtgYCBjGHCWaIVU4EwzD5l/zS7ULtnhSx0uGFeHhPxQa9xTfv+vHcw6X4W3\nm84O0c5I1FFt6BCfx4KvslN8P6xOFNeBHsCdZ8PDDCdzNIUCAwEAAaN7MHkwDgYD\nVR0PAQH/BAQDAgEGMA8GA1UdEwEB/wQFMAMBAf8wHQYDVR0OBBYEFBR9AI56r0Ng\nEC9yf7nWkaRtYwEPMB8GA1UdIwQYMBaAFBR9AI56r0NgEC9yf7nWkaRtYwEPMBYG\nA1UdEQQPMA2CC3BraS1jYS1yb290MA0GCSqGSIb3DQEBCwUAA4IBAQA/k6WHfXCj\nJwKY+K9T26NLs/O8OkpBhhVPf5t1po/Q1YaCD8ro2q3fULS9GHpqiBrS/JBr5nt5\nwoSVHSiMwm8wpMgyQoSkzcm9tkzHjq78V2Qag/dZANs+qwIftsmqvpK8poK3ntcp\nfIvLrIxb/M0+iRee35+coTpU98+ntysCVhRq0rYWtsPFWYT15RIJG/NLB1wlDd2w\nHtL1YEi4dOG6j/F2Oqd3fL4dkSIliGk8FCHkdOeiKPzLCYBhOzTyg87UyF8f3koq\nw6rRHSWiA0IMy6/cC7xrSwVPuVCqw/Wa5dN3TKzHEL29cVoOZ9wThIMvDWu5yEYU\nuaM0/LbSUQyK\n-----END CERTIFICATE-----",
    "serial_number": "4f:fe:0c:7e:10:bf:aa:f1:23:9d:53:8b:5e:17:78:c5:bf:09:80:e1"
  },
  "warnings": [
    "The expiration time for the signed certificate is after the CA's expiration time. If the new certificate is not treated as a root, validation paths with the certificate past the issuing CA's expiration time will fail."
  ]
}
root@cadcixztkv:~# vault write pki_int/intermediate/set-signed certificate=@pki_int.pem
Success! Data written to: pki_int/intermediate/set-signed
root@cadcixztkv:~# vault write pki_int/roles/coberg-ru \
>           allow_any_name=true \
>           max_ttl="720h" \
>           generate_lease=true
Success! Data written to: pki_int/roles/coberg-ru
root@cadcixztkv:~# nano pki_int.hcl
root@cadcixztkv:~# vault policy write pki_int pki_int.hcl
Success! Uploaded policy: pki_int
root@cadcixztkv:~# vault write pki_int/config/urls \
>      issuing_certificates="http://127.0.0.1:8200/v1/pki_int/ca" \
>      crl_distribution_points="http://127.0.0.1:8200/v1/pki_int/crl"
Success! Data written to: pki_int/config/urls
root@cadcixztkv:~# nano expiration.json
root@cadcixztkv:~# curl \
>      --header "X-Vault-Token: root" \
>      --request POST \
>      --data @expiration.json \
>      http://127.0.0.1:8200/v1/pki_int/config/crl
root@cadcixztkv:~# vault token create -policy=pki_int -ttl=720h
Key                  Value
---                  -----
token                s.LzacbPIBjAIob5ar31ogiOar
token_accessor       HicfDZe72522NOVRRZ9K3Ln2
token_duration       720h
token_renewable      true
token_policies       ["default" "pki_int"]
identity_policies    []
policies             ["default" "pki_int"]
root@cadcixztkv:~# vault login s.LzacbPIBjAIob5ar31ogiOar
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.LzacbPIBjAIob5ar31ogiOar
token_accessor       HicfDZe72522NOVRRZ9K3Ln2
token_duration       719h58m47s
token_renewable      true
token_policies       ["default" "pki_int"]
identity_policies    []
policies             ["default" "pki_int"]
root@cadcixztkv:~# vault write pki_int/issue/coberg-ru common_name=coberg.ru
Key                 Value
---                 -----
lease_id            pki_int/issue/coberg-ru/0LU4Ed5S5QV033niyi3aF8Wz
lease_duration      720h
lease_renewable     false
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDMzCCAhugAwIBAgIUT/4MfhC/qvEjnVOLXhd4xb8JgOEwDQYJKoZIhvcNAQEL
BQAwFjEUMBIGA1UEAxMLcGtpLWNhLXJvb3QwHhcNMjIwNDE1MTgxMTQzWhcNMjIw
NTE3MTgxMjEzWjAVMRMwEQYDVQQDEwpwa2ktY2EtaW50MIIBIjANBgkqhkiG9w0B
AQEFAAOCAQ8AMIIBCgKCAQEAlsVvXOgpwhdt0nZZuhvRibtsy2KRIn+TbFdKdMEz
98CLVIbzkA29eWxOISdIQMj+u9gIIjB98XjU3QNyi60enjcMns2IWZ7ubxygpLMR
9Df1nBN6kOccvGCBXVGMeo36ZgmATsr5ZCXfMSwqTntxz/QMUNaUNA9Y380qZ3+f
qSvdcdMi0MJh9i6Zc9nuZPsRBuX+ieKfgY86lNsvwLwpZehuRsnzQ8yTFnz1V1Jm
Hm7RHhdw1oyZ6sS7xx6WPwB1QNm2yz8epQ0cvqh11s+fyNN7IM86/XkRPHM4EPNp
FvR78jqi/4iP/FVBqUiP/4ZfWz1GIX3araw+P7P2PisgCwIDAQABo3oweDAOBgNV
HQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4EFgQUKfxnhlxe2xBo
eAEQ2VHPvdlLEcAwHwYDVR0jBBgwFoAUFH0AjnqvQ2AQL3J/udaRpG1jAQ8wFQYD
VR0RBA4wDIIKcGtpLWNhLWludDANBgkqhkiG9w0BAQsFAAOCAQEApztwZuhBeSej
PBV2rqaY0gpSu4hbZ3YDKoeiowpGziq26YidtCb6+rzZA8aHi09lZBJFPUDD2FRc
geKmHPCRNNDpgd45JZyraE7kkdx93rWO4+wR1ZBDauimyKjTtzMC2b/gNpIxZ2la
kNGM39QjbMg/VnOuArW+oGHDQw6wYaZY9zYfHcyHT0+E9mDRdDO/i2cw7AwLJI2h
5pWEcYt9bLo4GaBldpFYUq1jCnA4cYfw3QXi5J8ZMjTzatn+v7epb2Vj6aSbSc/Q
YmTdMNWU6G9/KI3nh7PHDEB0f/UriepXPSFGD6O7VBjopXJ2P7Xf2AjozuAxIJha
+PADmYUcgg==
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDuTCCAqGgAwIBAgIUee1dAc6ZuK6Rfl5dbVG2uDjmv9YwDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKcGtpLWNhLWludDAeFw0yMjA0MTUxODMxMTFaFw0yMjA1
MTUxODMxNDFaMBQxEjAQBgNVBAMTCWNvYmVyZy5ydTCCASIwDQYJKoZIhvcNAQEB
BQADggEPADCCAQoCggEBAOP3BPQPH099EOJIVCyuCKpfRY++knJB16TdTbwd1xxw
q9gm0d94hDmS/WffHX+2BYm9qioxGGMvmRDikKyhojVjMsNAMGPabRX2vTVH/v+P
1E3aarwtwpBmeTlBnIxg4cKNI+kBZycGn2uHj8u56TYqoBs/sUpk+W8F4UTNZrZF
4DBLBz5TslOscfZFvMZLcqf/oAPilsAL/2FA8KbhYS0B3Wr0xzsAo9rqcr1eX4TG
Xw0/lJUSvDsjpDfTPAGSjSzJCGyHxUhYQOw5IWewh5JwKWLweZl2GnruN9Pr4jYA
cPc8E4MSKNxXQhha7ItiAxoM8XV94XlfRl5SltDzFMMCAwEAAaOCAQAwgf0wDgYD
VR0PAQH/BAQDAgOoMB0GA1UdJQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAdBgNV
HQ4EFgQUHZX+VhbbPd25br4BDaEh8tFFbl8wHwYDVR0jBBgwFoAUKfxnhlxe2xBo
eAEQ2VHPvdlLEcAwPwYIKwYBBQUHAQEEMzAxMC8GCCsGAQUFBzAChiNodHRwOi8v
MTI3LjAuMC4xOjgyMDAvdjEvcGtpX2ludC9jYTAUBgNVHREEDTALggljb2Jlcmcu
cnUwNQYDVR0fBC4wLDAqoCigJoYkaHR0cDovLzEyNy4wLjAuMTo4MjAwL3YxL3Br
aV9pbnQvY3JsMA0GCSqGSIb3DQEBCwUAA4IBAQBGPl5wql4HWOQRzDjjisO/jq/C
w8C4rvwWR0t0EDIHX67Ujy5biudGQHkU+p+2aqTprlEHhw+hi1H7i5Jhw9FKnknS
rcfkWJ46ZZfWslU2N5dnnPk7lLRvhBnpF2JPfJWHEcijlIZ8FaLdfzGw/SdRzXec
FDwyTmJ0i2aguXnVVhU4++tNJuiDi9kInd2Wjz0TlTduI4skSqZtLUrKSaprwuta
in1ovuUKXBMUfgd/NsWT85shFLgzf5sKuMvWU97M4tT5IicdE2nMqRIDMHihZg5s
tEbzVI6RsWBmm4fkeZ6qKK/bJUJNe/Q8OgcEyOPDTYO6JirMM/oLM5F+J7zc
-----END CERTIFICATE-----
expiration          1652639501
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDMzCCAhugAwIBAgIUT/4MfhC/qvEjnVOLXhd4xb8JgOEwDQYJKoZIhvcNAQEL
BQAwFjEUMBIGA1UEAxMLcGtpLWNhLXJvb3QwHhcNMjIwNDE1MTgxMTQzWhcNMjIw
NTE3MTgxMjEzWjAVMRMwEQYDVQQDEwpwa2ktY2EtaW50MIIBIjANBgkqhkiG9w0B
AQEFAAOCAQ8AMIIBCgKCAQEAlsVvXOgpwhdt0nZZuhvRibtsy2KRIn+TbFdKdMEz
98CLVIbzkA29eWxOISdIQMj+u9gIIjB98XjU3QNyi60enjcMns2IWZ7ubxygpLMR
9Df1nBN6kOccvGCBXVGMeo36ZgmATsr5ZCXfMSwqTntxz/QMUNaUNA9Y380qZ3+f
qSvdcdMi0MJh9i6Zc9nuZPsRBuX+ieKfgY86lNsvwLwpZehuRsnzQ8yTFnz1V1Jm
Hm7RHhdw1oyZ6sS7xx6WPwB1QNm2yz8epQ0cvqh11s+fyNN7IM86/XkRPHM4EPNp
FvR78jqi/4iP/FVBqUiP/4ZfWz1GIX3araw+P7P2PisgCwIDAQABo3oweDAOBgNV
HQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUwAwEB/zAdBgNVHQ4EFgQUKfxnhlxe2xBo
eAEQ2VHPvdlLEcAwHwYDVR0jBBgwFoAUFH0AjnqvQ2AQL3J/udaRpG1jAQ8wFQYD
VR0RBA4wDIIKcGtpLWNhLWludDANBgkqhkiG9w0BAQsFAAOCAQEApztwZuhBeSej
PBV2rqaY0gpSu4hbZ3YDKoeiowpGziq26YidtCb6+rzZA8aHi09lZBJFPUDD2FRc
geKmHPCRNNDpgd45JZyraE7kkdx93rWO4+wR1ZBDauimyKjTtzMC2b/gNpIxZ2la
kNGM39QjbMg/VnOuArW+oGHDQw6wYaZY9zYfHcyHT0+E9mDRdDO/i2cw7AwLJI2h
5pWEcYt9bLo4GaBldpFYUq1jCnA4cYfw3QXi5J8ZMjTzatn+v7epb2Vj6aSbSc/Q
YmTdMNWU6G9/KI3nh7PHDEB0f/UriepXPSFGD6O7VBjopXJ2P7Xf2AjozuAxIJha
+PADmYUcgg==
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA4/cE9A8fT30Q4khULK4Iql9Fj76SckHXpN1NvB3XHHCr2CbR
33iEOZL9Z98df7YFib2qKjEYYy+ZEOKQrKGiNWMyw0AwY9ptFfa9NUf+/4/UTdpq
vC3CkGZ5OUGcjGDhwo0j6QFnJwafa4ePy7npNiqgGz+xSmT5bwXhRM1mtkXgMEsH
PlOyU6xx9kW8xktyp/+gA+KWwAv/YUDwpuFhLQHdavTHOwCj2upyvV5fhMZfDT+U
lRK8OyOkN9M8AZKNLMkIbIfFSFhA7DkhZ7CHknApYvB5mXYaeu430+viNgBw9zwT
gxIo3FdCGFrsi2IDGgzxdX3heV9GXlKW0PMUwwIDAQABAoIBAQCQ8vU0VhU7jrNY
nlLfYf1ij1eEsdEeU11Rl+Gglny7uayasPxLkq7IiP0Vudlg5hUZYv66+yKdlptG
EDmbet7QAXY6jir2ouEv0Qa3xUKPrx14L0QkyRfohy/XRy/KVqPV97Z4poq3Vs+x
KK43BMwTxACvss0BF7k+bcex3sTLaemOdCUED/3Xe19UBjZkSD5PbeFBHMvG+LQM
m/jQYjrGJYXKKxrgLbynAtey/o5zP6CAx3woCM8ac9MQNJLoRM6ShVrCogTbPzUA
lIPEm+OohMQ+buCMNakd7dDC9qSfsLZZ4ejiRD8Fs2Jc38ODsWdogL70AsiM2tJB
o0dvPyhpAoGBAPA6pm3Wa5pXT94eHPia9ubfwQb3wQ4cM/ZWWLHjRwG0cuFk1VFd
vGrZDvHeBiiVx/ZiR8jxOVSRd89+IZhj8ZvY5Jn2wt2BcOlMob5Yvu2w9vpP93Z1
hZxpJf94q7fSkYTlbITo8s0PKLjV0pIRENeHYoem0lrQe4UdgzphcjVlAoGBAPLu
QRxbQ3bo+/GQp0FqxNMYqoUsdQEYJaTvQw9UOT7UNaLE2BxzvPvYcrlZb6k7ZGPi
xKcCl6VCujQIhYdzC27EWNP5F0wQ0flhIO2zc+mVDB9L9sOP7RfaQ9UmDxa71yAm
Os2Z42Q33WmmlSdf4GVaD08fPegVV5/IBBU8prMHAoGBAJkEsthJ+XZxYTVZtxiA
FP9QzXJzw+wJ+OtTauG8pJEocPizMtHGRGtG4accxo15gsqlJZaQWPVOR6PKcvnP
x6l+yXZs4QkZF2Pz5R8LBmr/CwK8hyNUjovnykVlhvPrU7f0L2ClxWrq2gSKZruV
1UWEPjWsBFfGbreG6fz47zelAoGAU5SNakgsCflW1WHDacpmNYrDAFavWtUH/psj
I18C4w739Sxsfj2Dw9JQhlyzAjKg84zNw/gsXHt8vPJBnryCLnX3adbFoTIYTAiS
37ViJ/dT2GC2Ycpe9FnwBKG3h0bRWQpOKPbNOWnPD850eruN5ND7e2lwTy0onJyg
av4XDHkCgYB6x6sFXsPJqK90LzlCkZ64xpkhs0JMRc0Fw8Y5X12oMzz0+IOk7Q5G
nRh+m34wlNax3+NKs+Dzzwq/iT+8w9RAldPhqG/GVkSd6gbOsV/SORXAwxY4NsC+
MItSQokoA+YF4i7/sf9wxL8eALL1eJD/3h46RV8lrk7lsEvmU10Kvw==
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       79:ed:5d:01:ce:99:b8:ae:91:7e:5e:5d:6d:51:b6:b8:38:e6:bf:d6
root@cadcixztkv:~# cd /tmp/
root@cadcixztkv:/tmp# ls
consul-template_0.28.1_linux_amd64.zip
systemd-private-5632aaacf5de4131a9a0f72c4a67510e-ModemManager.service-pwtwcf
systemd-private-5632aaacf5de4131a9a0f72c4a67510e-systemd-logind.service-XHT46g
systemd-private-5632aaacf5de4131a9a0f72c4a67510e-systemd-resolved.service-hutBPf
systemd-private-5632aaacf5de4131a9a0f72c4a67510e-systemd-timesyncd.service-JqkFCg
vault_1.9.3_linux_amd64.zip
root@cadcixztkv:/tmp# unzip consul-template_0.28.1_linux_amd64.zip
Archive:  consul-template_0.28.1_linux_amd64.zip
  inflating: consul-template
root@cadcixztkv:/tmp# mv consul-template /user/local/bin
mv: cannot move 'consul-template' to '/user/local/bin': No such file or directory
root@cadcixztkv:/tmp# mv consul-template /usr/local/bin/
root@cadcixztkv:/tmp# cd
root@cadcixztkv:~# mkdir /etc/consul-template.d/; cd /etc/consul-template.d/
root@cadcixztkv:/etc/consul-template.d# nano pki-demo.hcl
root@cadcixztkv:/etc/consul-template.d# nano pki-demo.hcl
root@cadcixztkv:/etc/consul-template.d# cd
root@cadcixztkv:~# mkdir /etc/nginx/certs
root@cadcixztkv:~# nano pki-demo.hcl
root@cadcixztkv:~# nano /etc/consul-template.d/pki-demo.hcl
root@cadcixztkv:~# nano /etc/consul-template.d/coberg-cert.tpl
root@cadcixztkv:~# nano /etc/consul-template.d/coberg-key.tpl
root@cadcixztkv:~# nano /etc/consul-template.d/coberg-cert.tpl
root@cadcixztkv:~# nano /etc/systemd/system/consul-template.service
root@cadcixztkv:~# systemctl daemon-reload
root@cadcixztkv:~# systemctl enable consul-template.service
Created symlink /etc/systemd/system/multi-user.target.wants/consul-template.service → /etc/systemd/system/consul-template.service.
root@cadcixztkv:~# systemctl start consul-template.service
root@cadcixztkv:~# systemctl status consul-template.service
● consul-template.service - consul-template
     Loaded: loaded (/etc/systemd/system/consul-template.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-04-15 19:30:45 UTC; 10s ago
   Main PID: 23741 (consul-template)
      Tasks: 8 (limit: 1131)
     Memory: 4.8M
     CGroup: /system.slice/consul-template.service
             └─23741 /usr/local/bin/consul-template -config=/etc/consul-template.d/pki-demo.hcl

Apr 15 19:30:45 cadcixztkv systemd[1]: Started consul-template.


```

(5) Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.

![4.jpg](./assets/1650186472241-4.jpg)

(6) Установите nginx.

```
root@mszncyzucb:~# ls /usr/local/src/*.deb
/usr/local/src/nginx_1.20.2-1~focal_amd64.deb
/usr/local/src/nginx-dbg_1.20.2-1~focal_amd64.deb
root@mszncyzucb:~# cd /usr/local/src/nginx-*/
root@mszncyzucb:/usr/local/src/nginx-1.20.2# dpkg -i /usr/local/src/*.deb
Selecting previously unselected package nginx.
(Reading database ... 106785 files and directories currently installed.)
Preparing to unpack .../nginx_1.20.2-1~focal_amd64.deb ...
----------------------------------------------------------------------

Thanks for using nginx!

Please find the official documentation for nginx here:
* https://nginx.org/en/docs/

Please subscribe to nginx-announce mailing list to get
the most important news about nginx:
* https://nginx.org/en/support.html

Commercial subscriptions for nginx are available on:
* https://nginx.com/products/

----------------------------------------------------------------------
Unpacking nginx (1.20.2-1~focal) ...
Selecting previously unselected package nginx-dbg.
Preparing to unpack .../nginx-dbg_1.20.2-1~focal_amd64.deb ...
Unpacking nginx-dbg (1.20.2-1~focal) ...
Setting up nginx (1.20.2-1~focal) ...
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /lib/systemd/syst                 em/nginx.service.
Setting up nginx-dbg (1.20.2-1~focal) ...
Processing triggers for systemd (245.4-4ubuntu3.15) ...
Processing triggers for man-db (2.9.1-1) ...
root@mszncyzucb:/usr/local/src/nginx-1.20.2# nano /etc/nginx/nginx.conf
root@mszncyzucb:/usr/local/src/nginx-1.20.2# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@mszncyzucb:/usr/local/src/nginx-1.20.2# systemctl start nginx
root@mszncyzucb:/usr/local/src/nginx-1.20.2# systemctl status nginx
● nginx.service - nginx - high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-04-11 15:21:11 UTC; 8s ago
       Docs: https://nginx.org/en/docs/
    Process: 27918 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0>
   Main PID: 27926 (nginx)
      Tasks: 2 (limit: 1131)
     Memory: 1.9M
     CGroup: /system.slice/nginx.service
             ├─27926 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
             └─27927 nginx: worker process

Apr 11 15:21:11 mszncyzucb systemd[1]: Starting nginx - high performance web server...
Apr 11 15:21:11 mszncyzucb systemd[1]: nginx.service: Failed to parse PID from file /run/ngi>
Apr 11 15:21:11 mszncyzucb systemd[1]: Started nginx - high performance web server.
lines 1-15/15 (END)
^C
root@mszncyzucb:/usr/local/src/nginx-1.20.2# mkdir /etc/systemd/system/nginx.service.d
root@mszncyzucb:/usr/local/src/nginx-1.20.2# printf "[Service]\nExecStartPost=/bin/sleep 0.1\                 n" > /etc/systemd/system/nginx.service.d/override.conf
root@mszncyzucb:/usr/local/src/nginx-1.20.2# systemctl daemon-reload
root@mszncyzucb:/usr/local/src/nginx-1.20.2# systemctl restart nginx
root@mszncyzucb:/usr/local/src/nginx-1.20.2# systemctl status nginx                                           ● nginx.service - nginx - high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/nginx.service.d
             └─override.conf
     Active: active (running) since Mon 2022-04-11 15:23:10 UTC; 9s ago
       Docs: https://nginx.org/en/docs/
    Process: 27972 ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf (code=exited, status=0>
    Process: 27981 ExecStartPost=/bin/sleep 0.1 (code=exited, status=0/SUCCESS)
   Main PID: 27980 (nginx)
      Tasks: 2 (limit: 1131)
     Memory: 1.9M
     CGroup: /system.slice/nginx.service
             ├─27980 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
             └─27982 nginx: worker process

Apr 11 15:23:10 mszncyzucb systemd[1]: Starting nginx - high performance web server...
Apr 11 15:23:10 mszncyzucb systemd[1]: Started nginx - high performance web server.
lines 1-17/17 (END)
^C
root@mszncyzucb:/usr/local/src/nginx-1.20.2# curl -H 'Accept-Encoding: br' -I http://localhos                 t
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 11 Apr 2022 15:24:33 GMT
Content-Type: text/html
Last-Modified: Tue, 16 Nov 2021 14:44:02 GMT
Connection: keep-alive
ETag: W/"6193c3b2-264"
Content-Encoding: br

```

(7) По инструкции (ссылка) настройте nginx на https, используя ранее подготовленный сертификат:

(-) можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;

(-) можно использовать и другой html файл, сделанный вами;

```
# redirect traffic from http to https.
server {
listen              80;
listen              [::]:80;
server_name         coberg.ru www.coberg.ru;
return 301          https://coberg.ru$request_uri;
return 301          https://www.coberg.ru$request_uri;
}

server {
    listen              443 ssl;
    server_name         coberg.ru www.coberg.ru;
    ssl_certificate     /etc/nginx/certs/coberg.crt;
    ssl_certificate_key /etc/nginx/certs/coberg.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;

    location / {
      root   /usr/share/nginx/html;
      index  index.html index.htm;
    }
}
```

(8) Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.


![3.jpg](./assets/1650191120033-3.jpg)


(9) Создайте скрипт, который будет генерировать новый сертификат в vault:

(-) генерируем новый сертификат так, чтобы не переписывать конфиг nginx;

(-) перезапускаем nginx для применения нового сертификата.


```
#!/usr/bin/bash
echo "$(date +%Y-%m-%d_%H:%M:%S%Z%z) [INFO] Запущен скрипт генерации сертификата" | sudo tee -a /var/log/vault.log
# Запускаем процесс автообновления сертификата
sudo systemctl restart consul-template.service
echo "$(date +%Y-%m-%d_%H:%M:%S%Z%z) Запустилось автообновление сертификата" | sudo tee -a /var/log/vault.log
# Перезапускаем сервер `nginx`
echo "$(date +%Y-%m-%d_%H:%M:%S%Z%z) Перезапускаем службу nginx" | sudo tee -a /var/log/vault.log
sudo systemctl restart nginx
```


(10) Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.

0 2 1 * * - в два часа ночи 1 числа каждого месяца

Для демонстрации сделал запуск скрипта каждые две минуты.

```

# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
*/2 * * * * /etc/nginx/nginx_cert_create.sh

```


```
root@cadcixztkv:~# grep nginx_cert_create /var/log/syslog
Apr 16 21:03:01 cadcixztkv CRON[47863]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:06:01 cadcixztkv CRON[47945]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:09:01 cadcixztkv CRON[48027]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:12:01 cadcixztkv CRON[48153]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:15:01 cadcixztkv CRON[48235]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:18:01 cadcixztkv CRON[48316]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:21:01 cadcixztkv CRON[48388]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:24:01 cadcixztkv CRON[48465]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:27:01 cadcixztkv CRON[48546]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:30:01 cadcixztkv CRON[48622]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:33:01 cadcixztkv CRON[48695]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:36:01 cadcixztkv CRON[48771]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:39:01 cadcixztkv CRON[48852]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:42:01 cadcixztkv CRON[48970]: (root) CMD (/etc/nginx/nginx_cert_create.sh)
Apr 16 21:45:01 cadcixztkv CRON[49048]: (root) CMD (/etc/nginx/nginx_cert_create.sh)

```


```
root@cadcixztkv:~# cat /var/log/vault.log
2022-04-17_12:55:01MSK+0300 [INFO] Запущен скрипт генерации сертификата
2022-04-17_12:55:01MSK+0300 Запустилось автообновление сертификата
2022-04-17_12:55:01MSK+0300 Перезапускаем службу nginx
2022-04-17_12:58:01MSK+0300 [INFO] Запущен скрипт генерации сертификата
2022-04-17_12:58:01MSK+0300 Запустилось автообновление сертификата
2022-04-17_12:58:01MSK+0300 Перезапускаем службу nginx
2022-04-17_13:00:01MSK+0300 [INFO] Запущен скрипт генерации сертификата
2022-04-17_13:00:01MSK+0300 Запустилось автообновление сертификата
2022-04-17_13:00:01MSK+0300 Перезапускаем службу nginx
2022-04-17_13:02:01MSK+0300 [INFO] Запущен скрипт генерации сертификата
2022-04-17_13:02:02MSK+0300 Запустилось автообновление сертификата
2022-04-17_13:02:02MSK+0300 Перезапускаем службу nginx
2022-04-17_13:04:01MSK+0300 [INFO] Запущен скрипт генерации сертификата
2022-04-17_13:04:01MSK+0300 Запустилось автообновление сертификата
2022-04-17_13:04:01MSK+0300 Перезапускаем службу nginx

```


###Результат
Результатом курсовой работы должны быть снимки экрана или текст:

Процесс установки и настройки ufw
Процесс установки и выпуска сертификата с помощью hashicorp vault
Процесс установки и настройки сервера nginx
Страница сервера nginx в браузере хоста не содержит предупреждений
Скрипт генерации нового сертификата работает (сертификат сервера ngnix должен быть "зеленым")
Crontab работает (выберите число и время так, чтобы показать что crontab запускается и делает что надо)
