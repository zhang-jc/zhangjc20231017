title: HDFS 权限
tags:
  - Hadoop
  - HDFS
categories:
  - 大数据
  - Hadoop
date: 2016-11-13 21:41:34
---

### 概述

HDFS 实现文件和目录权限的模式拥有许多 POSIX 模式。每个文件和目录都关联一个属主和组。文件或者目录针对属主用户、组中的其他用户以及其他用户有单独的权限。要读取文件内容必须有 r 权限，写入或者追加文件内容必须有 w 权限。要列出目录的内容必须有 r 权限，创建或者删除文件或者目录必须有 w 权限，访问目录的子目录必须有 x 权限。

<!-- more -->

与 POSIX 模式相比，因为没有可执行文件的概念所以没有文件的 setuid 和 setgid 标识位。作为简化也没有目录的 setuid 和 setgid 标识位。在目录上设置粘滞位（Sticky bit）防止除了超级用户、目录属主或文件属主外的其他用户删除或者移动目录下的文件。给文件设置粘滞位没有任何效果。总的来说，文件和目录的权限都是这种模式。通常，使用 Unix 惯例来表现和显示模式，包括使用 8 进制数字。当创建文件或目录时，它的属主是客户端进程的用户，它的组是父目录的组（BSD 规则）。

HDFS 也提供支持 POSIX ACL（访问控制列表）来增强针对特定用户或组细粒度的权限控制。ACL 稍后在本文档详细讨论。

访问 HDFS 的每个客户端进程，包含用户名和组列表两部分身份。每当必须对一个客户端进程访问一个名字为 foo 的文件或者目录做鉴权时：

- 如果用户名与 foo 的属主匹配，那么属主的权限被测试；
- 否则如果 foo 的组匹配任意一个组列表中的组，那么组权限被测试；
- 否则 foo 的其他权限被测试。

如果权限检查失败，客户端操作也失败。

### 用户身份

从 Hadoop 0.22 版本，Hadoop 支持两种识别用户身份的方式，通过 hadoop.security.authentication 属性指定：

#### 简单模式

用这种操作模式，通过主机操作系统确定客户端进程的身份。在类 Unix 系统上，用户名与 `whoami` 是等效的。

#### kerberos

用基于 Kerberos 操作模式，通过 Kerberos 证书确定客户端进程的身份。例如，在一个 Kerberos 环境，一个用户可以使用 kinit 工具获取 Kerberos 的授予票据（TGT）并且使用 klist 确定他们当前的当事人。当映射一个 Kerberos 当事人到一个 HDFS 用户名，除了主要的组件外所有组件都被丢弃。例如，当事人 todd/foobar@CORP.COMPANY.COM 将在 HDFS 上作为简单的 todd 用户名。

不管什么操作模式，用户身份机制是 HDFS 本身外部的。HDFS 没有创建用户身份、建立组或处理用户凭据的的支持。

### 组映射

一旦一个用户像上面描述的那样被确定，组列表被通过 hadoop.security.group.mapping 属性配置的组映射服务确定。如果 Java 本地接口（JNI）是可用的，默认实现 org.apache.hadoop.security.JniBasedUnixGroupsMappingWithFallback 将确定。如果 JNI 是可用的，实现将使用 Hadoop 的 API 决定一个用户的组列表。如果 JNI 不可用，那么会使用 Shell 实现 org.apache.hadoop.security.ShellBasedUnixGroupsMapping。这个实现 Shell 使用 bash -c groups 命令（Linux/Unix 环境）或者 net group 命令（Windows 环境）来决定一个用户的组列表。一个备选的实现是通过 org.apache.hadoop.security.LdapGroupsMapping 实现，直接连接 LDAP 服务器决定组列表。但是，这种方式只能在用户组唯一的在 LDAP 上且不在 Unix 服务器上具体化时使用。关于配置组映射服务更多的信息可以在 Javadoc 中获取。

对于 HDFS，在 NameNode 上执行用户到组的映射。因此，NameNode 主机系统配置确定用户到组的映射。

注意，HDFS 用字符串存储一个文件或者目录的用户和组；不像 Unix 那样需要从用户和组的身份编号转换。

### 理解具体实现

每个文件或者目录的操作都传送全路径名称给 NameNode，NameNode 对每个操作的路径应用权限检查。客户端框架隐式的将用户身份与到 NameNode 的连接联系在一起，减少已存在的客户端 API 的修改。总是存在这样的场景，当对一个文件的操作成功后，因为文件或者在路径中的一些目录不存在了当重复这个操作时可能会失败。例如，当客户端开始读一个文件的时候，会首先发送第一个请求给 NameNode 来找出文件的第一个块所在的位置。第二个找出其他块的请求可能会失败。另一方面，正在删除文件的操作不能撤销一个已经知道文件块的客户端的访问。随着权限的添加，客户端对一个文件的访问可能在请求间被撤回。再者，变更权限不会撤销一个已经知道文件块的客户端的访问。

### 文件系统 API 的变化

如果权限检查失败，所有使用路径参数的方法都会抛出 AccessControlException 异常。

新方法：

- public FSDataOutputStream create(Path f, FsPermission permission, boolean overwrite, int bufferSize, short replication, long blockSize, Progressable progress) throws IOException;
- public boolean mkdirs(Path f, FsPermission permission) throws IOException;
- public void setPermission(Path p, FsPermission permission) throws IOException;
- public void setOwner(Path p, String username, String groupname) throws IOException;
- public FileStatus getFileStatus(Path f) throws IOException; 还将返回与路径相关联的用户、组和模式。

一个新文件或者目录的模式通过掩码组配置参数被限制。当现有的 create(path, …) 方法（不带权限参数）被使用时，新文件的模式是 0666 & ^umask。当新的 create(path, permission, …) 方法（带权限参数 P）被使用时，新文件的模式是 P & ^umask & 0666。当使用现有的 mkdirs(path) 方法（不带权限参数）创建一个新目录时，新目录的模式是 0777 & ^umask。当新的 mkdirs(path, permission) 方法（带权限参数 P）被使用时，新目录的模式是 P & ^umask & 0777。

### 应用程序 Shell 的变化

新操作：

- chmod [-R] mode file ...
  只有文件属主或者超级用户才有权限修改文件的模式。
- chgrp [-R] group file ...
  调用 chgrp 的用户必须属于特定的组并且是文件的属主或者超级用户。
- chown [-R] [owner][:[group]] file ...
  一个文件的所有者只能由超级用户修改。
- ls file ...
- lsr file ...
  输出被重新格式化来展示属主、组和模式。

### 超级用户

超级用户是与 NameNode 进程自己有相同身份的用户。简单的，如果你启动 NameNode，那么你就是超级用户。超级用户可以做任何事情，超级用户的权限检查从不会失败。谁是超级用户没有不变的概念，当 NameNode 启动后进程的身份决定了现在的超级用户是谁。HDFS 的超级用户不必是 NameNode 主机的超级用户，也没有必要，所有的集群有相同的超级用户。同时，一个试验者在个人工作站上运行 HDFS 便可以不需要任何配置很方便的成为安装的超级用户。

此外，使用配置参数区别一个管理员组。如果设置，这个组的成员都是超级用户。

### Web 服务器

默认的，Web 服务器的身份是一个配置参数。也就是，NameNode 没有真实用户的概念，但是，Web 服务器表现为管理员选择的有身份（用户和组）的用户。除非选择与超级用户匹配的身份，否则部分名称空间 Web 服务器是无法访问的。

### ACL（访问控制列表）

除了传统的 POSIX 权限模型，HDFS 也支持 POSIX ACL（访问控制列表）。对于实现不同于用户和组自然组织层次的权限需求，ACL 是很有帮助的。一个 ACL 提供了一种对指定的用户和组设置不同权限的方法，不仅仅是文件的属主和文件的组。

默认的，对 ACL 的支持是禁用的，并且 NameNode 不允许创建 ACL。要启用 ACL 支持，在 NameNode 配置中设置 dfs.namenode.acls.enabled 为 true。

一个 ACL 由一组 ACL 条目组成。每条 ACL 条目确定一个用户或组并为该用户或组授权或拒绝读、写和执行权限。例如：

    user::rw-
    user:bruce:rwx                  #effective:r--
    group::r-x                      #effective:r--
    group:sales:rwx                 #effective:r--
    mask::r--
    other::r--

ACL 条目包含一个类型、一个可选的名称和一个权限字符串。为了显示目的，‘:’用作每个域的分隔符。在这个示例 ACL 中，文件的属主有读写访问权限，文件的组有读和执行访问权限，其他用户有读访问权限。目前为止，这相当于设置文件的权限位为 654。

另外，有两个针对名称为 bruce 的用户和名称为 sales 的组的扩展 ACL 条目，两个都赋予了全部访问权限。掩码是一个特殊的 ACL 条目，用来过滤所有赋予命名的用户条目和命名的组条目的权限，以及未命名的组条目。在这个示例中，掩码只有读权限，我们可以看到几个 ACL 条目的有效权限因此被过滤掉了。

每个 ACL 都必须有掩码。如果一个用户在设置 ACL 时没有应用掩码，那么通过计算过滤所有条目权限的掩码来自动插入一个掩码。

在一个有 ACL 的文件上运行 chmod，实际是改变了掩码的权限。因为掩码作为一个过滤器，这有效的约束所有扩展 ACL 条目的权限，而不仅是改变组条目并且遗漏其他扩展的 ACL 条目。

这个模型也区分“访问 ACL”和“默认 ACL”，“访问 ACL”定义在权限检查时强制执行的规则，“默认 ACL”定义子文件或者子目录创建时自动接收到的 ACL 条目。例如：

    user::rwx
    group::r-x
    other::r-x
    default:user::rwx
    default:user:bruce:rwx          #effective:r-x
    default:group::r-x
    default:group:sales:rwx         #effective:r-x
    default:mask::r-x
    default:other::r-x

只有目录才有默认 ACL。当一个新文件或者子目录创建时，会自动拷贝它的父目录的默认 ACL 到它自己的访问 ACL 中。一个新子目录也拷贝它到自己的默认 ACL 中。用这种方式，默认 ACL 在新子目录创建时被拷贝到文件系统目录树任意级别的深度。

在新子文件的访问 ACL 中确切的权限值是服从模式参数过滤的。就默认 022 掩码而言，这是典型的新目录的 755 和 新文件的 644。模式参数对没有命名的用户（文件属主）进行过滤拷贝的权限值、掩码和其他。使用这个特定的 ACL 示例，并创建一个模式为 755 的新子目录，这个模式对最终结果没有任何过滤作用。然而，如果我们考虑创建一个模式为 644 的文件，那么模式过滤使新文件的 ACL 针对未命名用户（文件属主）接收到读写权限，mask 为读权限且其他用户为读权限。这个掩码意味着对命名为 bruce 的用户和命名为 sales 的组的有效权限只有读。

注意，拷贝发生在创建新文件或者子目录的时候。之后对父目录默认 ACL 的改变不会改变已经存在的子目录。

默认的 ACL 必须有所有最小要求的 ACL 条目，包含未命名用户（文件属主）、未命名的组（文件组）和其他用户条目。如果当设置一个默认 ACL 条目时用户没有应用这些条目中的一条，那么这么条目会通过从访问 ACL 中拷贝相应的条目被自动插入，或者没有访问 ACL 时拷贝权限位。默认 ACL 也必须要有掩码。像上面描述的，如果没有指定掩码，那么通过计算过滤所有条目权限的掩码来自动插入一个掩码。

当考虑一个有 ACL 的文件时，权限检查的算法变为：

- 如果用户名与文件属主匹配，那么属主权限将被测试；
- 否则如果用户名与命名用户条目中的一个用户名匹配，那么这些权限将被测试，并被掩码权限过滤；
- 否则如果文件组与组列表任何一个组匹配，并且如果这些权限被掩码授权访问过滤，那么这些权限将被使用；
- 否则如果有一个命名的组条目与一个组列表中的组匹配，并且如果这些权限被掩码授权访问过滤，那么这些权限将被使用；
- 否则如果文件组或者任何一个命名组条目与组列表中的一个组匹配，但是没有被那些权限赋予访问权限，那么访问被拒绝；
- 否则文件的其他权限将被测试。

最佳实践是基于传统的权限位实现大部分权限需求，定义小数量的 ACL 作为例外规则来增强权限位。一个有 ACL 的文件比一个只有权限位的文件会引起 NameNode 额外的内存消耗。

### ACL 文件系统 API

新方法：

- public void modifyAclEntries(Path path, List<AclEntry> aclSpec) throws IOException;
- public void removeAclEntries(Path path, List<AclEntry> aclSpec) throws IOException;
- public void public void removeDefaultAcl(Path path) throws IOException;
- public void removeAcl(Path path) throws IOException;
- public void setAcl(Path path, List<AclEntry> aclSpec) throws IOException;
- public AclStatus getAclStatus(Path path) throws IOException;

### ACL Shell 命令

- hdfs dfs -getfacl [-R] <path>
  展示文件和目录的访问控制列表（ACL）。如果一个目录有默认 ACL，那么 getfacl 也会展示默认 ACL。
- hdfs dfs -setfacl [-R] [-b |-k -m |-x <acl_spec> <path>] |[--set <acl_spec> <path>]
  设置文件和目录的访问控制列表（ACL）。
- hdfs dfs -ls <args>
  ls 的输出将在任何有 ACL 的文件和目录的权限字符串后追加一个 ‘+’ 字符。

查看 [File System Shell](http://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/FileSystemShell.html) 文档获取这些命令的全部内容。

### 配置参数

- dfs.permissions.enabled = true
  如果是则用这里描述的权限系统。如果否，权限检查被关闭，但所有其他行为不会改变。从一个参数值切换到另外一个参数值不会改变文件或目录的模式、属主或组。不管权限是开还是关，chmod、chgrp、chown 和 setfacl 总是检查权限。这些功能只在权限上下文中有用，因此没有向后兼容的问题。此外，这允许管理员在打开常规权限检查之前可靠的设置属主和权限。
- dfs.web.ugi = webuser,webgroup
  Web 服务器使用的用户名。设置这个为超级用户的名称允许所有 Web 客户端查看所有内容。修改这个为一个别的未用的身份允许 Web 客户端只能查看那些使用 “other” 权限可以访问的内容。另外，组可以添加到逗号分割的列表中。
- dfs.permissions.superusergroup = supergroup
  超级用户组的名称。
- fs.permissions.umask-mode = 0022
  创建文件和目录时使用的掩码。对于配置文件，可能使用十进制数值 18。
- dfs.cluster.administrators = ACL-for-admins
  为集群指定为 ACL 的管理员。控制谁可以在 HDFS 中访问默认 servlet 等。
- dfs.namenode.acls.enabled = true
  设置为 true 来启用 HDFS ACL（访问控制列表）支持。默认的，ACL 是禁用的。当 ACL 禁用时，NameNode 拒绝所有设置一个 ACL 的尝试。