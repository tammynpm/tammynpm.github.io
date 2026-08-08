---
title: "Details on Self-hosting Rocket-chat on an Alpine LXC"
date: 2026-04-26T17:32:56-04:00
draft: false
---

Hosting rocket-chat
![](/posts/rocket-chat/rocket-chat-main.png)


## Installing gitea on an Alpine LXC
install Git, wget

### Prepare Database
Gitea has its own internal database client using an ORM library called XORM. Through this layer, Gitea can natively communicate with several database [backends](https://docs.gitea.com/installation/database-prep). 

Here I'm going to install MariaDB server on another Alpine LXC. 

create a system user on Alpine is a tad different than Debian or RHEL 
```
adduser -S --shell /bin/bash --gecos 'Git Version Control' --disabled-password --home /home
/git git
```

Run gitea as a service. 
A service on Alpine is created using Alpine's init system called OpenRC instead of systemd. OpenRC services' scripts locate in `/etc/init.d/` directory. An OpenRC script looks like this ![](/posts/rocket-chat/openrc-script-example.png)

The script that is provided in the official repo is for systemd which cannot be applied in this case. I found a nice [website](http://openrc.run/) that helps you convert systemd to OpenRC service. The result is: 
```
#!/sbin/openrc-run

name=$RC_SVCNAME
description="Gitea (Git with a cup of tea)"
supervisor="supervise-daemon"
command="/usr/local/bin/gitea"
command_args="web --config /etc/gitea/app.ini"
supervise_daemon_args=" -d /var/lib/gitea/ -e USER=\"git\" -e HOME=\"/home/git\" -e GITEA_WORK_DIR=\"/var/lib/gitea\""
command_user="git:git"

depend() {
	after net 
}
```
Make the script executable, enable, and start the service. 
Add gitea to the default services: `rc-update add gitea default`
Start gitea service: `rc-service gitea start`
Check service status: `rc-service gitea status`

Access the LXC at port 3000 ![](/posts/rocket-chat/gitea-main.png)
