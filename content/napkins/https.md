---
title: "Https"
date: 2025-09-30T23:49:23-04:00
draft: true
---

to stop all docker containers at once (not include removing)

```shell
sudo docker stop $(sudo docker ps -aq)
```

to remove all docker containers `docker rm $(docker ps -aq)`


use Cloud DNS to update the DNS records for `umasscybersec.org`

The process: 
```text
Add standard 
Domain name: minuteman.umasscybersec.org
Type: A 
IP: [ctfd VM static external IP]
TTL (time to live): 1
unit: second
```




