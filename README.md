# BT04 - Cấu hình tường lửa bảo vệ máy chủ (UFW & Cloud Firewall Integration)

## 1. Lớp 1 - Cấu hình UFW trong OS

```bash
devops@0310263186:~$ sudo ufw default deny incoming
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)
devops@0310263186:~$ sudo ufw default allow outgoing
Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)
devops@0310263186:~$ sudo ufw allow 22/tcp
Rules updated
Rules updated (v6)
devops@0310263186:~$ sudo ufw allow 80/tcp
Rules updated
Rules updated (v6)
devops@0310263186:~$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```

Kiểm tra trạng thái:

```bash
devops@0310263186:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
```

## 2. Lớp 2 - DigitalOcean Cloud Firewall

Vào DigitalOcean Console > Networking > Firewalls > Create Firewall.

- Name: `ptit-web-fw`
- Inbound Rules:
  - SSH, TCP 22, Sources: All IPv4 / All IPv6
  - HTTP, TCP 80, Sources: All IPv4 / All IPv6
- Outbound Rules: giữ mặc định (All TCP / UDP / ICMP)
- Apply to Droplet: `0310263186` (160.30.160.103)

Ảnh chụp màn hình cấu hình: `cloud-firewall.png`

## 3. Kiểm tra

```bash
lephu@local:~$ ssh -i ~/.ssh/id_rsa devops@160.30.160.103
devops@0310263186:~$ exit
```

```bash
lephu@local:~$ curl -s -o /dev/null -w "%{http_code}\n" http://160.30.160.103
200
```

```bash
lephu@local:~$ nc -zv 160.30.160.103 3306
nc: connect to 160.30.160.103 port 3306 (tcp) failed: Connection timed out
lephu@local:~$ nc -zv 160.30.160.103 8080
nc: connect to 160.30.160.103 port 8080 (tcp) failed: Connection timed out
```
