---
title: GitLab 설치
author: Bisso
date: 2021-10-20 14:15:00 +0900
categories: [Install, GitLab]
tags: [writing]
---

# GitLab

## Linux 설치

```bash
# apt update
sudo apt update
sudo apt upgrade -y

# dependency 설치
sudo apt install -y ca-certificates curl openssh-server

# gitlab ce repository add
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash

# apt update 및 gitlab install
sudo apt update
sudo apt -y install gitlab-ce

# gitlab settings
sudo vi /etc/gitlab/gitlab.rb # external_url modify

# settings 변경 시 아래 명령어를 통해 반영
sudo gitlab-ctl reconfigure

# service action
sudo gitlab-ctl status
sudo gitlab-ctl stop
sudo gitlab-ctl restart
# 또는
sudo gitlab-ctl restart logrotate
```