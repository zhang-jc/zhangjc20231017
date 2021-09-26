---
title: 借Trino安装介绍Ansible常用操作
date: 2021-09-26 16:44:03
tags:
- Trino
- Ansible
categories:
- 大数据
- Trino
---

Ansible是一款功能非常强大的企业级IT自动化工具。官方有配套的UI工具Ansible Tower，因为是付费工具所以没有尝试，本文基于命令行。

Trino其实就是Presto，只是改了名称。其中的故事自行搜索...

下文以我在部署Trino的过程介绍用到的Ansible功能，详细介绍请查看官网手册：[Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)。

# 功能列表

- Inventory：服务器管理，非常灵活。
- User模块：管理服务器用户，例如创建新用户trino。
- Copy模块：文件同步，例如批量分发。
- Shell模块：执行Shell命令。
- File模块：管理服务器文件，例如新建、删除、创建软连接等。

# 安装过程
## Inventory

服务器清单，如下：

```INI
[coordinator]
10.0.0.51  ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.65  ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********

[worker]
10.0.0.133 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.134 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.135 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.136 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.137 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.138 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.139 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.140 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.141 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.142 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.143 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.144 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.145 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.146 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.147 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.148 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.149 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.150 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.151 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.152 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.153 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.154 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.155 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
10.0.0.156 ansible_user=ops ansible_password=******** ansible_become=yes ansible_become_password=********
```

## OS

```Shell
ansible -i hosts all -m user -a "name=trino"
ansible -i hosts all -m copy -a "src=etc/security/limits.d/trino.conf dest=/etc/security/limits.d/ mode=644"
```

## JDK

```Shell
ansible -i hosts all -m copy -a "src=zulu11.50.19-ca-jdk11.0.12-linux_x64.tar.gz dest=/opt/"
ansible -i hosts all -m shell -a "cd /opt/ && tar xzvf zulu11.50.19-ca-jdk11.0.12-linux_x64.tar.gz"
ansible -i hosts all -m file -a "path=/opt/java-zulu src=/opt/zulu11.50.19-ca-jdk11.0.12-linux_x64 state=link"
ansible -i hosts all -m shell -a "cd /opt/java-zulu/bin && ./java --version"
ansible -i hosts all -m copy -a "src=bin/launcher dest=/opt/trino/bin/ owner=trino group=trino"
```

## Trino
### 安装

```Shell
ansible -i hosts all -m copy -a "src=trino-server-362.tar.gz dest=/opt/"
ansible -i hosts all -m shell -a "cd /opt/ && tar xzvf trino-server-362.tar.gz
ansible -i hosts all -m file -a "path=/opt/trino src=/opt/trino-server-362 state=link"
ansible -i hosts all -m file -a "path=/opt/trino-server-362 owner=trino group=trino recurse=yes"
```

### 配置
#### node.properties

```Shell
ansible -i hosts all -m file -a "path=/data/trino state=directory owner=trino group=trino"
ansible -i hosts all -m copy -a "src=etc/node.properties dest=/opt/trino/etc/ owner=trino group=trino"
ansible -i hosts all -m shell -a "ifconfig|grep 'inet 10.0.0'|awk '{gsub(\"\\\\.\", \"-\"); line=\"\nnode.id=\"\$2; print line}' >> /opt/trino/etc/node.properties"
```

#### jvm.config

```Shell
ansible -i hosts all -m copy -a "src=etc/jvm.config dest=/opt/trino/etc/ owner=trino group=trino"
```

#### config.properties
##### coordinator

```Shell
ansible -i hosts coordinator -m copy -a "src=etc/coordinator.config.properties dest=/opt/trino/etc/ owner=trino group=trino"
ansible -i hosts coordinator -m shell -a "mv /opt/trino/etc/coordinator.config.properties /opt/trino/etc/config.properties"
```

##### worker

```Shell
ansible -i hosts worker -m copy -a "src=etc/worker.config.properties dest=/opt/trino/etc/ owner=trino group=trino"
ansible -i hosts worker -m shell -a "mv /opt/trino/etc/worker.config.properties /opt/trino/etc/config.properties"
```

#### log.properties

```Shell
ansible -i hosts all -m copy -a "src=etc/log.properties dest=/opt/trino/etc/ owner=trino group=trino"
```

#### catalog
##### hive.properties

```Shell
ansible -i hosts all -m copy -a "src=etc/catalog/hive.properties dest=/opt/trino/etc/catalog/ owner=trino group=trino"
```