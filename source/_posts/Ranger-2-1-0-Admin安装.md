title: Ranger 2.1.0 Admin安装
date: 2021-07-02 10:24:38
tags:
- 大数据
- Ranger
categories:
- 大数据
- Ranger
---

# Creating MySQL user ranger2 failed

```Text
2021-07-01 18:44:53,480  [E] Creating MySQL user ranger2 failed..
2021-07-01 18:44:53,496  [E] DB schema setup failed! Please contact Administrator.
```

原因是Ranger安装程序默认会使用MySQL的root账号创建所需要的账号，也就是DBA过程。如果不需要安装程序自动创建，则将install.properties中的以下配置前的#号去掉：

```Text
setup_mode=SeparateDBA
```

# Specified key was too long; max key length is 767 bytes

- 第一种报错：

```Text
Error executing: CREATE TABLE `x_portal_user` (   `id` bigint(20) NOT NULL AUTO_INCREMENT,   `create_time` datetime DEFAULT NULL,   `update_time` datetime DEFAULT NULL,   `added_by_id` bigint(20) DEFAULT NULL,   `upd_by_id` bigint(20) DEFAULT NULL,   `first_name` varchar(1022) DEFAULT NULL,   `last_name` varchar(1022) DEFAULT NULL,   `pub_scr_name` varchar(2048) DEFAULT NULL,   `login_id` varchar(767) DEFAULT NULL,   `password` varchar(512) NOT NULL,   `email` varchar(512) DEFAULT NULL,   `status` int(11) NOT NULL DEFAULT '0',   `user_src` int(11) NOT NULL DEFAULT '0',   `notes` varchar(4000) DEFAULT NULL,   `other_attributes` varchar(4000) DEFAULT NULL,   PRIMARY KEY (`id`),   UNIQUE KEY `x_portal_user_UK_login_id` (`login_id`),   UNIQUE KEY `x_portal_user_UK_email` (`email`),   KEY `x_portal_user_FK_added_by_id` (`added_by_id`),   KEY `x_portal_user_FK_upd_by_id` (`upd_by_id`),   KEY `x_portal_user_cr_time` (`create_time`),   KEY `x_portal_user_up_time` (`update_time`),   KEY `x_portal_user_name` (`first_name`(767)),   KEY `x_portal_user_email` (`email`),   CONSTRAINT `x_portal_user_FK_added_by_id` FOREIGN KEY (`added_by_id`) REFERENCES `x_portal_user` (`id`),   CONSTRAINT `x_portal_user_FK_upd_by_id` FOREIGN KEY (`upd_by_id`) REFERENCES`x_portal_user` (`id`) ) ROW_FORMAT=DYNAMIC;
java.sql.SQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes
SQLException : SQL state: 42000 java.sql.SQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes ErrorCode: 1071
2021-07-02 10:44:54,411  [E] ranger_core_db_mysql.sql file import failed!
```

修改对应字段的长度可以解决。注意UTF8每个字符4个字节。

- 第二种报错：

```Text
Error executing: CREATE TABLE `x_portal_user` (   `id` bigint(20) NOT NULL AUTO_INCREMENT,   `create_time` datetime DEFAULT NULL,   `update_time` datetime DEFAULT NULL,   `added_by_id` bigint(20) DEFAULT NULL,   `upd_by_id` bigint(20) DEFAULT NULL,   `first_name` varchar(128) DEFAULT NULL,   `last_name` varchar(1022) DEFAULT NULL,   `pub_scr_name` varchar(2048) DEFAULT NULL,  `login_id` varchar(128) DEFAULT NULL,   `password` varchar(512) NOT NULL,   `email` varchar(128) DEFAULT NULL,   `status` int(11) NOT NULL DEFAULT '0',   `user_src` int(11) NOT NULL DEFAULT '0',   `notes` varchar(4000) DEFAULT NULL,   `other_attributes` varchar(4000) DEFAULT NULL,   PRIMARY KEY (`id`),   UNIQUE KEY `x_portal_user_UK_login_id` (`login_id`),   UNIQUE KEY `x_portal_user_UK_email` (`email`),   KEY `x_portal_user_FK_added_by_id` (`added_by_id`),   KEY `x_portal_user_FK_upd_by_id` (`upd_by_id`),   KEY `x_portal_user_cr_time` (`create_time`),   KEY `x_portal_user_up_time` (`update_time`),   KEY `x_portal_user_name` (`first_name`(767)),   KEY `x_portal_user_email` (`email`),   CONSTRAINT `x_portal_user_FK_added_by_id` FOREIGN KEY (`added_by_id`) REFERENCES `x_portal_user` (`id`),   CONSTRAINT `x_portal_user_FK_upd_by_id` FOREIGN KEY (`upd_by_id`) REFERENCES `x_portal_user` (`id`) ) ROW_FORMAT=DYNAMIC;
java.sql.SQLException: Incorrect prefix key; the used key part isn't a string, the used length is longer than the key part, or the storage engine doesn't support unique prefix keys
SQLException : SQL state: HY000 java.sql.SQLException: Incorrect prefix key; the used key part isn't a string, the used length is longer than the key part, or the storage engine doesn't support unique prefix keys ErrorCode: 1089
2021-07-02 11:51:23,763  [E] ranger_core_db_mysql.sql file import failed!
```

去掉prefix key的定义即可。

修改后的建表语句如下：

```SQL
CREATE TABLE `x_portal_user` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  `added_by_id` bigint(20) DEFAULT NULL,
  `upd_by_id` bigint(20) DEFAULT NULL,
  `first_name` varchar(128) DEFAULT NULL,
  `last_name` varchar(1022) DEFAULT NULL,
  `pub_scr_name` varchar(2048) DEFAULT NULL,
  `login_id` varchar(128) DEFAULT NULL,
  `password` varchar(512) NOT NULL,
  `email` varchar(128) DEFAULT NULL,
  `status` int(11) NOT NULL DEFAULT '0',
  `user_src` int(11) NOT NULL DEFAULT '0',
  `notes` varchar(4000) DEFAULT NULL,
  `other_attributes` varchar(4000) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `x_portal_user_UK_login_id` (`login_id`),
  UNIQUE KEY `x_portal_user_UK_email` (`email`),
  KEY `x_portal_user_FK_added_by_id` (`added_by_id`),
  KEY `x_portal_user_FK_upd_by_id` (`upd_by_id`),
  KEY `x_portal_user_cr_time` (`create_time`),
  KEY `x_portal_user_up_time` (`update_time`),
  KEY `x_portal_user_name` (`first_name`),
  KEY `x_portal_user_email` (`email`),
  CONSTRAINT `x_portal_user_FK_added_by_id` FOREIGN KEY (`added_by_id`) REFERENCES `x_portal_user` (`id`),
  CONSTRAINT `x_portal_user_FK_upd_by_id` FOREIGN KEY (`upd_by_id`) REFERENCES `x_portal_user` (`id`)
) ROW_FORMAT=DYNAMIC;
```

> 对整个建表脚本中的SQL都需要对应的修改。