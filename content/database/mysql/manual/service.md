---
title: "服务功能"
description: test
draft: false
---



### 添加账号

要创建新账号时，需要指定账号密码和授权访问的数据库，创建成功后可查看 [账号列表](#账号列表)。

![添加账号](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/add_user.png)

**注解**：

1. 系统保留 root 、qc_check 、 qc_repl 账号来进行自动化运维和数据同步，请勿删除这三个账号，以免破坏系统的运行。
2. 如果加密认证选项为 `YES` 则需要开启 [SSL传输加密](#ssl传输加密) 后该用户才可以用于连接数据库。
3. 支持添加`高级权限`账户，但是，仅能创建一个。
4. 添加用户成功后，如使用 Proxy 实例进行读写链接，需要重启 Proxy 实例来同步主实例用户信息。

### 删除账号

这里填写要删除的账号名。

![删除账号](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/del_user.png)

### SSL传输加密

可选择是否开启 SSL传输加密，默认关闭。更多详细信息参考 [MySQL SSL](https://dev.mysql.com/doc/refman/5.7/en/creating-ssl-rsa-files.html)

![SSL 传输加密](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/SSL_cert.png)

**注解**：开启该服务后，获取 SSL 证书参考 [启动日志服务端](#启动日志服务端) 中 wget `下载单个目录`（mysql-cert）的方法下载。

### 增删节点

可以根据需要增加集群节点。仅可以修改 只读实例 和 Proxy 实例 节点数量。添加只读实例节点的任务执行时间跟集群主实例的数据量有关系，数据量大时，任务执行时间会久一些，添加节点不影响集群的读写。

![添加节点](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/add_nodes.png)

**注解**：

1. 不能修改主实例节点数量。
2. 基础版集群不支持增删节点服务。

### 扩容集群

可以对一个运行中的数据库服务进行在线扩容，调整CPU/内存/磁盘空间大小。

![扩容集群](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/scale.png)

**注解**：

1. 点选需要扩容的角色以及扩容后的规格，点击提交即可完成扩容。扩容需要在开机状态下进行，扩容时链接会有短暂中断，请在业务低峰时进行。
2. 不支持资源降配。

支持自动扩容,通过点击控制台的`运维与管理`下的`自动伸缩`点击创建。

![扩容集群](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/auto_scale.png)


### 启动日志服务端

**启动服务**

MySQL Plus 支持通过 HTTP 服务预览和下载日志，HTTP 服务端口为 `18801` 。

日志服务支持下载 mysql 错误日志 `mysql-error` 和 mysql 慢日志 `mysql-slow`（二者都保留六个日志文件），同时支持下载 mysql 审计日志 `mysql-audit` ， mysql binlog 文件 `mysql-bin` 和 SSL 证书文件 `mysql-cert` 。

![启动日志服务端](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/start_caddy_server.png)

**注解**：点选运行服务的角色，并勾选需要预览和下载的 MySQL 日志，输入 HTTP 用户名和密码点击提交即可启动日志服务。

**预览日志**

![登陆web](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/preview_logs_log-in.png)

**注解**：通过浏览器输入需要下载日志节点的 IP 和 HTTP 服务端口 18801，如 `http://192.168.0.18:18801`，输入 HTTP 用户名和密码即可登录预览日志。（需要在同一 VPC 下主机上的浏览器来访问，或者通过青云 VPN 服务来访问。不要通过端口转发的方式将服务暴露到公网，避免对数据库服务造成重大影响！）

![预览日志](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/preview_logs.png)

![下载日志](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/caddy_log_download.png)

**注解**：可以通过鼠标点击下载日志。

**wget 下载日志**

1.下载所有目录

```
wget -r http://192.168.0.18:18801 --http-user=admin --http-password=Admin123@ --reject="index.html*"
```

2.下载单个目录

```
wget -r http://192.168.0.18:18801/mysql-bin/ --http-user=admin --http-password=Admin123@ --reject="index.html*" -np
```

3.下载单个文件

```
wget -r http://192.168.0.18:18801/mysql-bin/mysql-bin.000003 --http-user=admin --http-password=Admin123@ --reject="index.html*"
```

### 关闭日志服务端

![关闭日志服务端](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/stop_caddy_server.png)

**注解**：关闭日志服务可以勾选是否清理日志，如果选则 `YES`，会将 `mysql-error` ，`mysql-slow` 和 `mysql-audit` 的归档日志清理掉，如 `mysql-error.log.*` ， `mysql-slow.log.*`　，`mysql-audit.log.*`　。

### 在线迁移

迁移服务可以将远端 MySQL 数据库的数据平滑迁移到 QingCloud MySQL Plus 集群中。目前支持迁移的 MySQL 版本为 5.6 & 5.7 & 8.0。

支持 `xtrabackup` 方式和 `mysqldump` 两种迁移方式，数据量大时，xtrabackup 迁移速率会快很多。

**迁移说明：**

**mysqldump 方式迁移**

> 1. `在线迁移可将远端集群的数据迁移到当前集群。`
> 2. 在线迁移时，会去远端 MySQL 通过 mysqldump 方式复制全量数据。
> 3. 需要提供远端 MySQL 具有 super 权限 和 复制权限 的账户，并且要求远端 MySQL 开启 GTID 模式。
> 4. 远端 MySQL 版本在 5.6 以下，可以参考[数据迁移方案](#数据迁移)进行迁移。
> 5. 迁移期间，可通过 高可用写 IP 查看同步状态。
> 6. 为了保护集群运维账户，该服务不会同步远端 MySQL 的用户，需要使用者迁移完成后添加远端 MySQL 用户信息到本集群中。
> 7. 迁移前在远端集群设置 `connect_timeout=30`；远端集群和本集群均设置 `max_allowed_packets=1G`、`slave_pending_jobs_size_max=1G`、`interactive_timeout=3600`、`wait_timeout=3600`、`net_read_timeout=1800`、`net_write_timeout=1800`。
> 8. 迁移期间远端集群不要执行 DDL 语句。
> 9. 迁移期间不要在本集群控制台执行任何操作。
> 10. 不支持从高版本 MySQL 迁移到 低版本 MySQL。
> 11. 该服务仅会在主实例运行，当集群有只读实例时，服务运行失败，有 Proxy 实例不影响服务运行。
> 12. 开启在线迁移服务后，自动备份功能将会失效，建议暂时关闭自动备份。

**xtrabackup 方式迁移**

> 1. 仅支持同一 VPC 下的集群迁移。
> 2. 在线迁移时，会去远端 MySQL 通过 xtrabackup 方式复制全量数据。
> 3. 需要提供远端 MySQL 具有 super 权限 和 复制权限 的账户，并且要求远端 MySQL 开启 GTID 模式。
> 4. MySQL 内核大版本要一致，不支持跨大版本迁移，比如从 MySQL-5.6 迁移到 MySQL-5.7。 不支持 MySQL-5.7.18-14 及以前的版本迁移。
> 5. 迁移期间，可通过 高可用写 IP 查看同步状态。
> 6. 该服务会同步远端 MySQL 的用户信息到本集群中。
> 7. 迁移期间远端集群不要执行 DDL 语句。
> 8. 迁移期间不要在本集群控制台执行任何操作。
> 9. 该服务仅会在主实例运行，当集群有只读实例时，服务运行失败，有 Proxy 实例不影响服务运行。
> 10. 开启在线迁移服务后，自动备份功能将会失效，建议暂时关闭自动备份。

**迁移步骤：**

**mysqldump 方式迁移**

> 1. 若原库与当前集群不在同一 VPC 下，使用 边界路由器（vpc board） 或 VPN ，打通原库与当前集群的网络。
> 2. 点击「 在线迁移 」，将原库 MySQL 账户名（要有 super 权限 和 复制权限）、密码、端口、IP 地址、远端集群ID 填入下图所示文本框，并选择 xtrabackup 为 `NO`， 点击「 提交 」即开始迁移。
> 3. 等待「 在线迁移 」服务完成。服务执行完成后，当前集群就成功获取了原库的全量数据并与原库配置了主从关系，如此则可以持续同步原库增量数据到当前集群。
> 4. 若当前集群与原库同版本且同私有网络，可执行[交换预留IP](#交换预留IP)来快速切换业务。否则，按第5步切换业务。
> 5. 选择合适时段，切换业务到当前集群（切换时，远端 MySQL 必须要停写以保证数据一致性）：
>
> ```
>   a) 停止原库的业务。
> 
>   b) 校验当前集群与原库数据是否一致，若一致，则继续下一步。
> 
>   c) 点击 「 结束迁移 」，会自动重启 MySQL 集群。
> 
>   d) 将业务所连IP改为当前集群的VIP，恢复业务。
> 
> ```


**xtrabackup 方式迁移**

> 1. 点击「 在线迁移 」，将原库 MySQL 账户名（要有 super 权限 和 复制权限）、密码、端口、IP 地址、远端集群ID 填入下图所示文本框，并选择 xtrabackup 为 `YES`，点击「 提交 」即开始迁移。
> 2. 等待「 在线迁移 」服务完成。服务执行完成后，当前集群就成功获取了原库的全量数据并与原库配置了主从关系，如此则可以持续同步原库增量数据到当前集群。
> 3. 若当前集群与原库同版本且同私有网络，可执行[交换预留IP](#交换预留IP)来快速切换业务。否则，按第4步切换业务。
> 4. 选择合适时段，切换业务到当前集群（切换时，远端 MySQL 必须要停写以保证数据一致性）：
>
> ```
>   a) 停止原库的业务。
> 
>   b) 校验当前集群与原库数据是否一致，若一致，则继续下一步。
> 
>   c) 点击 「 结束迁移 」，会自动重启 MySQL 集群。
> 
>   d) 将业务所连IP改为当前集群的VIP，恢复业务。
> 
> ```

![在线迁移](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/migrate_data.png)


### 交换预留IP

交换预留IP用于同版本且同私有网络中的当前集群与远端源集群之间在「在线迁移 」后交换VIP，省去人工停业务、检查数据一致性、执行结束迁移、更换业务所连VIP等步骤，实现业务快速切换。

**注意事项：**

> 1、为使操作步骤中的「 在线迁移 」尽可能执行成功，须参考[在线迁移](#在线迁移)中的迁移说明。
>
> 2、为确保数据一致性，「 交换预留IP 」时会设置远端源集群只读。

**操作步骤：**

> 1、在当前新创建的集群提前添加与远端源集群中相同的业务账号，便于后续「 交换预留IP 」完成后立即恢复业务。
>
> 2、在当前新创建的集群点击「 在线迁移 」，将远端源集群的MySQL账号(具备Super和复制权限)、密码、高可用写IP、端口信息填入文本框，点击「 提交 」即开始迁移。
>
> 3、等待「 在线迁移 」任务执行完成，在当前新创建的集群点击「 交换预留IP 」，如下图所示选择需要交换的集群ID后提交，执行完成后业务便切换到当前集群。

![交换预留IP](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/exchange_reserved_ips.png)


### 监控

这里提供了每台主机的资源监控和服务监控。服务监控统计了SHOW GLOBAL STATUS中的信息，可用于定位分析数据库的性能。资源监控统计了主机的资源信息，如CPU使用率、硬盘IOPS情况等，可用于查看系统性能是否到达瓶颈。

![提交事务数](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/commit_monitor.png)

![写入查询](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/write_monitor.png)

![行锁定](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/lock_monitor.png)

![CPU利用率](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/cpu_monitor.png)

![硬盘 IOPS](C:/Original_qingcloud_doc/qingcloud-iaas-docs-master/database_cache/app_mysql_plus/_images/iops_monitor.png)