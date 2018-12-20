---
title: Gitlab Docker 安装、配置邮件、开启SSH
top: false
date: 2018-08-17 17:34:26
tags: gitlab
categories: devOps
thumbnail:
---

# Gitlab Setup

## 1 社区版本

### 1.1 docker run 官方 ce 版本

```bash
sudo docker run --detach \
    --hostname localhost \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume ./config:/etc/gitlab \
    --volume ./logs:/var/log/gitlab \
    --volume ./data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```

### 1.2 docker run 汉化 ce 版本

```bash
sudo docker run --detach \
    --hostname localhost \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name cn-gitlab \
    --restart always \
    --volume ./config:/etc/gitlab \
    --volume ./logs:/var/log/gitlab \
    --volume ./data:/var/opt/gitlab \
    twang2218/gitlab-ce-zh:latest
```

### 1.3 docker compose 方式

```docker-compose.yml
web:
  image: 'gitlab/gitlab-ce:latest'
  restart: always
  hostname: 'gitlab.example.com'
  environment:
    GITLAB_OMNIBUS_CONFIG: |
      external_url 'https://gitlab.example.com'
  ports:
    - '80:80'
    - '443:443'
    - '22:22'
  volumes:
    - '/srv/gitlab/config:/etc/gitlab'
    - '/srv/gitlab/logs:/var/log/gitlab'
    - '/srv/gitlab/data:/var/opt/gitlab'
```

## 2 开启 ssh

- 修改宿主机 ssh 默认端口 22 => 1022

```sh
vi /etc/ssh/sshd_config

Port 22
Port 1022
```

- 防火墙 `firewall`

```sh
firewall-cmd --zone=public --add-port=10022/tcp --permanent
firewall-cmd --reload
firewall-cmd --zone=public --query-port=10022/tcp
```

- `SELinux`

```sh
yum provides semanage
yum install policycoreutils-python

semanage port -l | grep ssh
semanage port -a -t ssh_port_t -p tcp 10022
semanage port -l | grep ssh
systemctl restart sshd.service
```

## 2 配置邮件

- 修改 `/etc/gitlab/gitlab.rb`

```js
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "邮箱地址"
gitlab_rails['smtp_password'] = "password"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
gitlab_rails['smtp_domain'] = "exmail.qq.com"
gitlab_rails['gitlab_email_from'] = '邮箱地址'
```

- 更新配置 `gitlab-ctl reconfigure`

- 重启服务 `gitlab-ctl restart`

## 3 centos7 中 docker 文件挂载，容器中没有执行权限

```bash
1，在运行容器的时候，给容器加特权：
示例：docker run -i -t --privileged=true -v /home/docs:/src waterchestnut/nodejs:0.12.0

2，临时关闭selinux：
示例：su -c "setenforce 0"
注意：之后要记得重新开启selinux，命令：su -c "setenforce 1"

3. 添加selinux规则，将要挂载的目录添加到白名单：
chcon -Rt svirt_sandbox_file_t /home/docs
```

## 参考

- [gitlab-ce](https://hub.docker.com/r/gitlab/gitlab-ce/)
- [docker/docker-compose.yml](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/docker/docker-compose.yml)
- [Run the image](https://docs.gitlab.com/omnibus/docker/)

---

© 会煮咖啡的猫咪
微信 ducafecat
[镜像](ducafecat.github.io) [Github](https://github.com/ducafecat)
