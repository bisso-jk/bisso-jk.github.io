---
title: Jenkins Fedora 설치
author: Bisso
date: 2021-10-21 19:36:00 +0900
---

# Fedora

## 자바 설치

## 젠킨스 설치

```bash
## LTS install

sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo dnf upgrade
# Add required dependencies for the jenkins package
sudo dnf install java-11-openjdk
sudo dnf install jenkins
sudo systemctl daemon-reload
```

## 젠킨스 시작

```bash
sudo systemctl enable jenkins

sudo systemctl start jenkins

sudo systemctl status jenkins
```