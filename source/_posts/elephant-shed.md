---
title: Elephant Shed
date: 2019-05-30 16:43:08
tags: elephant
categories: elephant
thumbnail: /gallery/Elephant_Shed.png
toc: true
---
# 介绍
Elephant Shed 是一个基于网页版的postgreSQL前端管理软件。他捆绑了PostgreSQL的各项应用软件和组件。当前他能管理单点Debian/Ubuntu PostgreSQL服务起和应用。
    
<!--more-->

主要组件包括如下：
* PostgreSQL - <https://www.postgresql.org/>
* pgAdmin4 - <https://www.pgadmin.org/>
* postgresql-common - <https://anonscm.debian.org/cgit/pkg-postgresql/postgresql-common.git>
* pgBadger - <http://dalibo.github.io/pgbadger/>
* pgBackRest - <http://www.pgbackrest.org/>
* Grafana - <https://grafana.com/>
* Prometheus - <https://prometheus.io/>
* Cockpit - <http://cockpit-project.org/>
* Shell In A Box - <https://github.com/shellinabox/shellinabox>      

增加的其他工具可以通过配置来设置。     
捆绑的组件和处理任务的数量会增加当前运行的服务器。因此仅建议用于足够大小的系统。   

本文档描述了当前版本。
本文档的更新版本将随 elephant-shed packages一起提供，可以在中找到`/ usr / share / doc / elephant-shed-portal`（`/ usr / share / doc / elephant-shed *`）和网站地址<https：// your-server / doc />。

# 安装

Elephant Shed 包括如下安装包和依赖:

  * `elephant-shed`:
    元数据包.
  * `elephant-shed-prometheus`:
    Prometheus和他的输出的配置文件和帮助脚本.
  * `elephant-shed-cockpit`:
    cockpit和cockpit-ws配置文件.
  * `elephant-shed-grafana`:
    预配置的Prometheus数据源和仪表板，包括各种系统和PostgreSQL指标.
  * `elephant-shed-pgadmin4`:
    pgAdmin4的配置文件.
  * `elephant-shed-pgbackrest`:
    系统服务文件和生成器, 帮助脚本和预设配置.
  * `elephant-shed-pgbadger`:
    系统服务文件, 生成器和帮助程序脚本.
  * `elephant-shed-portal`:
    Elephant Shed web的Apache配置.
  * `elephant-shed-postgresql`:
    PostgreSQL的其他预设配置文件.
  * `elephant-shed-shellinabox`:
    Shell的配置文件.
  * `elephant-shed-tmate`:
    更容易支持的预配置tmate的安装包.


## 安装包

重构包可从该网址获得 <https://packages.credativ.com/public/postgresql/>.

存储库包括该包 `elephant-shed`. 该包包括 Grafana, Cockpit and various python 包.

### 在Debian和Ubuntu上安装

```
# 安装工具
sudo apt-get install curl ca-certificates apt-transport-https

# 使用官网 PostgreSQL存储库, apt.postgresql.org
echo "deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main" | sudo tee -a /etc/apt/sources.list.d/pgdg.list
curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

# 使用 credativ 存储库, packages.credativ.com
echo "deb https://packages.credativ.com/public/postgresql/ stretch-stable main" | sudo tee -a /etc/apt/sources.list.d/elephant-shed.list
curl https://packages.credativ.com/public/postgresql/aptly.key | sudo apt-key add -

# 安装 elephant-shed
sudo apt-get update
sudo apt-get install elephant-shed

# 选择适合的PostgreSQL版本安装
sudo apt-get install postgresql-10

# 允许群组“elephant-shed”中的每个用户在门户网站上登录
# 加入该群
sudo adduser <USERNAME> elephant-shed
```

### 在RedHat和CentOS系统上安装

Elephant Shed 可以在PostgreSQL RPM中安装有关的包
到<a href="https://yum.postgresql.org/"><b>yum.postgresql.org</b></a>网站安装适合PostgreSQL版本的安装包.
然后再安装Elephant Shed.

```bash
# 使用credativ存储库(也将下载EPEL)
sudo yum install https://packages.credativ.com/public/postgresql/yum/credativ-repo.rpm

# 在RedHat上, 激活其他存储库 (不是在CentOS上)
subscription-manager repos --enable=rhel-7-server-extras-rpms
subscription-manager repos --enable=rhel-7-server-optional-rpms

# 选择需要的 PostgreSQL 版本安装
sudo yum install postgresql11-server postgresql11-contrib postgresql-common
sudo pg_createcluster 11 main --start

# 安装 elephant-shed
sudo yum install elephant-shed

# 所有在 "elephant-shed"组里的用户都可以登陆网页
# 把所有需要的用户都加进这个组里
sudo vigr

# 不幸的是, pgAdmin4和shellinabox不支持SELinux
# 如果要使用这些组件，请禁用SELinux
sudo setenforce 0
sudo sed -i -e 's/^SELINUX=.*/SELINUX=permissive/' /etc/selinux/config
```

## 源码安装

源码可以在github上获得: <https://github.com/credativ/elephant-shed>

### 源码构建Debian包

所有 Elephant Shed Debian 包可以用该命令构建 `make deb`.

要求:

  - `dpkg-dev`
  - `devscripts`

### 构建文档

把文件构建成 HTML 格式类型 `make docs`.

要求:

  - `sphinx`

### 创建Vagrant测试

`make vagrant` 该命令构建所有组件, 用Vagrant创建一个新的虚拟机，用Ansible部署软件.
该命令也可以重新部署已经运行的机器.

Vagrant 配置文件在该目录 `vagrant/Vagrantfile`.

要求:

  - `vagrant`
  - `virtualbox` 或者 `libvirt`
  - `ansible`

### 部署到远程服务器

部署软件到任何一台机器上, 连接的相关信息在该目录 `vagrant/inventory`.
部署开始命令 `make ansible`.

要求:

  - `ansible`    

# 第一步

登陆到游览器，输入服务器地址 (e.g.<https://your-server/>). 默认配置重定向到HTTP请求到HTTPS.

Elephant Shed官网提供了运行PostgreSQL的例子和他的情况. 此外你也获得了其他已安装的组件.

服务起会向你要用户证书。每个部署程序区分每个用户不同. 在测试安装中(e.g. using Vagrant) 初始化用户是 **admin** 密码是 **admin**. 可以看下: [Users](users.html). 所有绑定的组件除了pgAdmin4都使用PAM 授权。 

pgAdmin4 还不支持 PAM 授权. 它有自己的用户管理系统与所有系统用户分离.在安装配置中你会被要求安装初始化用户配置. 如果你部署了Vagrant 虚拟机， 初始化用户就是 **admin@localhost**
，密码是 **admin**.

![Elephant Shed portal](/el-portal.png) 


在新安装中，您将找到一个运行当前PostgreSQL主要版本的集群，其名称为 `main`.

集群配置在这 `/etc/postgresql/<major version>/<name>/`.

要从外部应用程序服务器使用PostgreSQL，只需要几个步骤.

1. 使用SSH或shellinabox打开一个shell去连接服务器 <https://your-server/shellinabox>.

2. 切换用户 `postgres` 然后psql:
    * `sudo -u postgres psql`

3. 创建一个数据Create a database 和相应的应用用户, 可以选:
    * `psql: CREATE ROLE appuser1 WITH LOGIN PASSWORD 'testpass';`
    * `psql: CREATE DATABASE appdb1 OWNER appuser1;`

4. 允许应用程序服务器的外部访问，开放给网络中每个人. 配置文件: `/etc/postgresql/<major version>/<name>/pg_hba.conf`

5. (可选) 进行所需的配置更改和调整. `/etc/postgresql/<major version>/<name>/postgresql.conf`

6. 重新加载配置，选项:
    * 主页: 点击按钮 `Service` 下一步集群 ，然后在下拉菜单中选择 "Reload" 
    * `psql`: `SELECT pg_reload_conf();`

7. (可选) 配置超级用户以便能够使用pgAdmin4 <https://your-server/pgadmin4> or other management tools
    * 为用户postgres创建密码: `\password`
    * 创建个性化的超级用户: `CREATE USER "sosna" SUPERUSER;`, `\password "sosna"`

# 组件

## PostgreSQL

Elephant Shed基于`postgresql-common`，默认的PostgreSQL基于Debian的安装管理系统。创建，删除等任务或重命名PostgreSQL实例（PostgreSQL术语中的“集群”）应该是
通过`postgresql-common`的命令行实用程序`pg_createcluster`，`pg_dropcluster`和`pg_renamecluster`完成。

### 默认配置

Beside the postgresql-common default configuration the
`elephant-shed-postgresql` package adds additional parameters for the cluster
creation process. Some of these parameters are required by the
Elephant Shed components. E.g. pgBadger requires some special
`log_line_prefix`. You can find this configuration under
`/etc/postgresql-common/createcluster.d/elephant-shed.conf`. Be
careful when changing any of these values.

### 集群管理

创建新集群命令 `pg_createcluster <version> <name>`.
通过命令`pg_lsclusters`安装集群和相关情况.

```
postgres@stretch:~$ pg_lsclusters
Ver Cluster  Port Status          Owner    Data directory  Log file
9.6 main     5432 online          postgres /9.6/main       /log/9.6-main.log
9.6 standby1 5433 online,recovery postgres /9.6/standby1   /log/9.6-standby1.log
9.6 standby2 5434 online,recovery postgres /9.6/standby2   /log/9.6-standby2.log
9.6 test     5435 online          postgres /9.6/test       /log/9.6-test.log
```
删除集群命令 `pg_dropcluster <version>
<name>` (注意，会删除集群中的所有数据！).

开始, 停止, 重启 或者 重载使用的命令是 `pg_ctlcluster <action> <version>
<name>` 如下:
  - `start`
  - `stop`
  - `restart`
  - `reload`

另外,你能用 systemctl (`systemctl <action>
postgresql@<version>-<name>`) 或者 Cockpit web interface.

![Cockpit Cluster Service](/cockpit_postgresql_service.png) 

*注意:* The Elephant Shed 将来的版本将会包含集群管理 [portal](#portal).

## 门户网站

门户网站是所有其他基于Web的入口点接口。它使用HTTPS和基本身份验证。每个用户
在Unix组中，`elephant-shed`可以访问它（参见[Users]（＃users））。

门户网站还显示所有状态
PostgreSQL集群包括指向Cockpit服务的链接（为了启动或停止集群），日志文件，pgBadger报告和备份软件pgBackRest。


顶部的导航栏允许在不同的网络之间切换服务。

默认情况下，仅部署HTTPS的自签名证书。一个
大多数浏览器都会显示相应的安全警告。你可以改变
签名证书（例如来自您的公司CA，或来自[Let's Encrypt]（https://letsencrypt.org/））。
Web服务由Apache2提供。它还充当反向代理，以服务所有其他Web界面并实施身份验证。

### PostgreSQL 集群

![Figure: PostgreSQL Cluster](/portal_cluster_list.png)


本节概述了现有的PostgreSQL集群及其状态。
每个集群都能开关显示当前状态。
通过单击群集，将打开带有按钮的附加菜单。
目前，所有按钮都链接到需要确认的相应组件，因此不会直接触发任何操作，但这可能会在将来发生变化。

### systemd - Service

这链接到Cockpit中此PostgreSQL集群的配置。
这里可以配置在系统启动时启用或禁用相应的服务，还可以触发启动，停止和重新加载等操作。

### systemd - Log

如果为此群集启用了syslog（这是Elephant Shed创建的群集的默认设置），则链接到Cockpit中的相应日志条目。

### Report - Run

默认情况下，每晚生成一次所有群集的pgBadger报告。
使用此服务，可以根据需要为特定群集生成报告。

### Report - Show

链接到相应的pgBadger报告概述。
查看 [pgBadger](#reporting-pgbadger)获得更多信息.

### Backup

本节提供了几个使用pgBackRest进行备份的功能。
了解更多备份工具pgBackRest，可访问网站[pgBackRest](#backup-pgbackrest).

#### Full

链接到Cockpit以启动临时完整备份。
#### Incremental

链接到Cockpit以查看临时增量备份。

#### Info

显示备份的状态。此按钮仅在第一次备份运行后显示。
此处显示可用备份和WAL归档的内容。

获得更多信息: <http://www.pgbackrest.org/user-guide.html#quickstart/backup-info>

### Switches

#### Archiving

此开关显示是否设置了使用pgBackRest的存档命令。
可以使用'/ bin / true'设置一个或停用该功能。
时间点恢复需要存档，但对于pgBackRest备份更为重要。
如果通过门户或计时器触发备份，则会自动激活存档。

手动更改服务 `pgbackrest-toggle-archiving@<version>-<name>.service`.
可以切换状态.

#### 全备

切换到启用或禁用定期备份。
已启用的备份作业（systemd timer）以绿色显示。
启动或停止计时器 `pgbackrest@<version>-<name>.timer`.
启用/禁用用于在下次重启后启用/禁用计时器。

#### 增量备份


切换到启用或禁用定期备份。
已启用的备份作业（systemd timer）以绿色显示。
要启动/停止计时器`pgbackrest-incr @ <version>  -  <name> .timer`。
启用/禁用用于在下次重启后启用/禁用计时器。

## 网页服务接口 - Cockpit

Cockpit 允许通过HTTPS远程管理所有系统的服务。
就像按下按钮使得启动、停止、重启服务如此简单. 还实时显示系统日志.

![Figure: Cockpit Package Updates](/cockpit_package_updates.png)

## 监控 - Prometheus

Prometheus是一种基于度量的服务器和服务监控系统。
它以给定的时间间隔从配置的目标收集指标，评估规则表达式，显示结果，并且如果观察到某些条件为真，则可以触发警报。

![Prometheus graphing the load](/prometheus-load.png) 

![Prometheus targets](/prometheus-targets.png) 

### 服务

在此设置中，Prometheus堆栈由systemd控制的不同组件组成。
部署了以下功能。

#### prometheus.service

监控系统和时间序列数据库 - 这是监控服务本身。它主动从不同来源提取指标。
它还提供内部指标和可通过门户访问的Web界面。

配置文件:

* `/etc/prometheus/elephant-shed-prometheus.yml`
* `/etc/default/elephant-shed-prometheus`

#### prometheus-node-exporter.service

机器指标的Prometheus导出器 - 此服务导出系统指标并侦听端口9100。
默认情况下，这些指标每30秒收集一次。

配置文件:

* `/etc/default/elephant-shed-prometheus-node-exporter`

#### prometheus-sql-exporter.service

用于SQL指标的Prometheus导出程序 - 此服务收集PostgreSQL特定指标并侦听端口9237。
通过查询数据库来检索度量标准。
为了不产生额外的负载，仅每60秒收集度量。

**警告: 不建议将`prometheus-sql-exporter`的监视间隔设置为低于60秒。
这可能会干扰正常的应用程序，并对PostgreSQL集群产生很大影响.**

`prometheus-sql-exporter.service`在启动时启动与每个数据库的一个连接，并保持此连接打开。
在每个连接的开头，`prometheus-sql-exporter.service`检查是否存在扩展名`pg_stat_statements`。
如果没有，该服务发出语句`CREATE EXTENSION pg_stat_statements`。

配置文件:

* `/etc/prometheus-sql-exporter.yml`

#### update-prometheus-sql-exporter-config.timer

此计时器定期触发`update-prometheus-sql-exporter-config.service`，每10分钟为`prometheus-sql-exporter`生成一个新配置。
这可确保每个新数据库集群和每个新数据库都自动包含在监视中。
可以手动调用update-prometheus-sql-exporter-config.service直接生成新配置。

配置模板文件:

* `prometheus-sql-exporter.yml.in`

配置 (运行):

* `prometheus-sql-exporter.yml`

### 更多资源

* <https://prometheus.io/docs/introduction/overview/>
* <https://github.com/prometheus/prometheus>
## 看板 - Grafana

Grafana是一种从各种不同数据源创建图形和仪表板的工具。
默认安装的一个PostgreSQL看报包含最需要的相关指标和调试PostgreSQL服务。

这些预先部署的仪表板通过`elephant-shed-graphana` Debian软件包，并可在将来更改。
它们是只读的，如果您进行任何更改，则需要以新名称保存。

### PostgreSQL Server 概貌

![Grafana - PostgreSQL Server Overview](/grafana-overview.png) 

此仪表板以带有简单仪表的摘要部分开始，以提供整个系统的概述。
这些仪表可能表明当前存在的问题，或暗示未来可能出现的问题。

仪表深度指标显示为图形。

### 服务起指标

服务器指标包括：CPU使用率（按类型/核心），磁盘使用情况，磁盘利用率，网络吞吐量等等。
配置以下模板筛选器：

  - `Disk`: 过滤一个或多个磁盘
  - `Interface`: 过滤一个或多个接口
  - `Filesystem`: 过滤或更多文件系统/挂载点

### 集群指标

下一节包含PostgreSQL集群范围的指标，如连接（按类型/按数据库），事务数量，数据库增长等。
一次只显示一个群集。要切换当前显示的集群，请使用模板过滤器`PostgreSQL Cluster`。

### 数据库指标

数据库级别度量标准显示在* PostgreSQL Overview *仪表板的末尾。
默认情况下，会显示当前所选PostgreSQL群集的所有数据库的度量标准。
要过滤一个或多个数据库，可以使用模板过滤器“Database”。

### 更多资源

* <http://docs.grafana.org/>
* <https://github.com/grafana/grafana>

## DBA 工具 - pgAdmin4

![pgAdmin4](/pgadmin.png) 

pgAdmin4是PostgreSQL的一个管理工具，可以帮助DBA执行许多不同的任务。
它提供用户管理，DDL功能，交互式SQL shell等。

### 更多资源

* <https://www.pgadmin.org/docs/pgadmin4/1.x/>

## 备份 - pgBackRest
Elephant Shed附带预装的备份解决方案* pgBackRest *。
每个PostgreSQL实例都可以通过发出命令`systemctl start pgbackrest @ <version>  -  <name>`或通过Web界面中的Cockpit启动备份来单独备份。
为每个实例列出了一个快捷方式。

使用第一个创建每个群集的配置条目
备份运行。默认情况下，只设置`db-path`和`db-port`。

单击在门户网站上的图标pgBackRest可以获得所有备份的列表

![Backup via Cockpit](/el-backrest-start.png) 

pgBackRest知道3种类型的备份完整，增量和差异。
我们默认使用完整和差异。
默认情况下不安装差异备份的服务文件。

### 全量备份

全量备份表示在给定时间点对数据库的全量备份。
备份由两部分组成，备份本身存储在`backup`中，WAL文件在备份期间写入，存储在`archive`中。

为了确保这些WAL文件在归档中，我们在创建第一个备份之前自动启用WAL归档。

**警告**:如果启用了归档，则保留所有比最早存储的备份更新的WAL文件。
如果备份保留很长时间并且未禁用存档，则会占用备份位置中的大量空间。
通过删除备份，不再需要的WAL文件也被删除。

### 增量备份
增量备份表示先前全量备份与给定点上的当前数据之间的已更改数据。
增量备份可能比全量备份小很多，但依赖于特定的先前全量备份。
如果没有此全量份，则无法恢复。


### 保留

要清理空间，需要删除旧备份。
pgBackRest需要知道要保留多少全量备份。
如果达到该数量，将从最旧的备份开始删除所有其他备份。
如果删除全量备份，则还将删除所有依赖于它的增量备份。
这是必要的，因为如果没有匹配的全量备份，则无法还原增量备份。


### 配置

配置文件目录 `/etc/pgbackrest.conf`.

```
[global]
repo-path=/var/lib/pgbackrest

[9.6-main]
db-path=/var/lib/postgresql/9.6/main
db-port=5432

[9.6-test]
db-port=5433
db-path=/var/lib/postgresql/9.6/test
```

全局部分为每个现有和未来的数据库集群设置默认配置。
对于每个单个群集，可以更改这些默认值。
这里将解释一些基本选项。
有关完整概述，请参阅文档。

如果服务是用ansible设置, 另外还设置了以下`[global]`参数：

```
[global]
retention-full=4
compress-level=6
spool-path=/mnt/backup/pgbackrest_spool
archive-async=y
archive-queue-max=1099511627776
repo-path=/mnt/backup/pgbackrest
...
```

#### retention-full
此选项定义应保留多少完整备份。

**危险: 如果存储的完整备份比`retention-full`更多，pgBackRest将删除最旧的备份以保持完全备份！保留完整备份！**

#### compress-level
要使用的gzip压缩级别（6是默认值）.

#### archive-async
启用WAL文件的异步归档，从而实现更高的归档吞吐量。

#### spool-path
在哪里保留异步归档的附加信息（状态目录）。

#### archive-queue-max
丢弃段之前要保留多少个WAL段。
*注意：我们配置1TB的值以确保pgbackrest默认情况下永远不会抛出WAL段*

#### repo-path
这将设置存储备份和WAL文件的主目录。
它可以设置为任何所需的安装点，因此可以轻松地备份到远程服务器。

### Backup

对于每个群集，都有一个systemd服务，它执行完整或增量备份。

* pgbackrest@\<version\>-\<name\>
* pgbackrest-incr@\<version\>-\<name\>

要创建临时备份，可以启动相应的服务。
`systemctl start pgbackrest @ 9.6-main`将创建集群`9.6-main`的完整备份。

如果没有以前的完整备份可用，`pgbackrest-incr @`也将创建一个完整备份。

### Automation

要自动创建备份和执行保留策略，每个群集有两个systemd计时器。

* pgbackrest@<version>-<name>.timer
* pgbackrest-incr@<version>-<name>.timer

`pgbackrest @ <version>  -  <name> .timer`触发完整备份，`pgbackrest-incr @ <version>  -  <name> .timer`触发增量备份。

这些计时器是为每个集群创建的，并使用默认时序进行初始化。
可以通过systemd或Web门户为每个数据库集群独立启用计时器。
要完全启用定时备份， `timer` 必须 *started* **和** *enabled*.
如果 `timer` 是 *started* 但是没有 *enabled* ，systemd 在重启后不启动timer.

请记住，仅启用增量备份仅适用于较短的时间段，特殊情况（如不更改数据库）或完整备份是以其他方式触发的。
为了将存储和恢复时间保持在合理的水平，需要定期进行完全备份。

#### pgbackrest@.timer

```
# /lib/systemd/system/pgbackrest@.timer
[Unit]
Description=Automated pgBackRest full backup of PostgreSQL cluster %i

[Timer]
OnCalendar=Sun *-*-* 01:00:00
RandomizedDelaySec=2h

[Install]
WantedBy=multi-user.target
```

此计时器每周日凌晨01:00或随机最多2小时后触发完整备份。
由RandomizedDelaySec = 2h设置的随机延迟被设置，因此systemd可以在给定的时间范围内调度许多定时器。
这么做是为了所有集群的备份不是在同一个时间启动，阻塞了I/O

#### pgbackrest-incr@.timer

```
# /lib/systemd/system/pgbackrest-incr@.timer
[Unit]
Description=Automated pgBackRest incremental backup of PostgreSQL cluster %i

[Timer]
OnCalendar=Tue,Thu *-*-* 01:00:00
RandomizedDelaySec=2h

[Install]
WantedBy=multi-user.target
```

此计时器每周二和周四凌晨01:00或随机最多2小时后触发增量备份。

#### WAL 归档

默认情况下，对于新的PostgreSQL集群，将禁用WAL归档。
它可以使用门户网站（参见[portal]（＃portal））或启动`pgbackrest-toggle-archving.service`来激活。
该服务将归档模式切换为打开或关闭，具体取决于之前的状态。

*注意：*如果禁用归档并启动完整或增量备份（手动或通过计时器），则会自动启用归档。
需要执行此步骤以确保还原所需的所有WAL文件都存档在basebackup。
**Archiving is _not_ disabled after the backup run.**

### 还原

**还原是一种侵入性操作，如果未正确执行，可能会破坏数据!**

要还原备份，有两个主要方法full和delta。

#### Full Restore

根据给定的备份全部还原（默认情况下为最新）还原到给定（默认）目标。
还原命令要求目标目录为空。.
此方法可用于新的机器，小型集群，或者是大部分数据不正确的情况下.

步骤如下.

  1. Stop the cluster (if still running)
  2. Delete or move all remaining data
  3. Restore full content from backup

*使用用户 `postgres`操作*.

```
# 1. 停止集群
pg_ctlcluster <major version> <name> stop

# 2. 删除或移除所有存在数据
mv /var/lib/postgresql/<major version>/<name> /var/somewhere-save
mkdir /var/lib/postgresql/<major version>/<name>

# 3. 从备份中还原所有数据
pgbackrest --stanza=<major version>-<name> restore
```

然后再次重启集群.
如果有足够的可用存储空间，则最好将数据移动到保存位置而不是删除。

#### Delta 还原

增量恢复不需要干净的目标，只能复制与备份不同的文件。
这种方法可以快得多，特别是如果大多数底层文件自上次备份以来没有改变。

**这有可能破坏数据!**
因为这应用于集群数据，可能会造成损害。
数据不在备份/ WAL存档中而在当前群集中的将会丢失！

操作一个 delta 还原.

  1. 停止集群 (如果在运行中)
  2. 从备份中还原所有内容

```
# 1. 停止集群
pg_ctlcluster <major version> <name> stop

# 2. 从备份中还原所有内容
pgbackrest --stanza=<major version>-<name> --delta restore
```

操作完后重启

#### 时间点恢复

该方法是全部还原.
这意味着所有basebackup文件并从存档中复制回来并应用所有WAL文件.

如果要恢复另一个恢复目标，则必须指定`--type`和`--target`。
大多数时候，人们希望将数据库恢复到给定的时间点（例如'2017-08-24 12:00:00'）。
这将需要切换`--type = time`和`--target ='2017-08-24 12：00：00'`。

```
pgbackrest --stanza=<major version>-<name> --type=time --target="<ISO timestamp>" restore
```

### 更多资源

* <http://www.pgbackrest.org/user-guide.html>
* <http://www.pgbackrest.org/command.html>
* <http://www.pgbackrest.org/configuration.html>

## 报告 - pgBadger

为每个PostgreSQL创建一个pgBadger服务实例。每次新的时候都会自动生成和更新这些服务
创建或删除集群（systemd-generators）。

pgBadger systemd计时器可确保定期更新报告。
默认情况下，这是每天23:00。

每个pgBadger服务解析的PostgreSQL相应PostgreSQL实例的日志文件. 生成的报告保存在其中
`/var/lib/pgbadger/<version>-<name>` (e.g. `/var/lib/pgbadger/9.6-main/`).

可以在Web界面中访问所有报告。
日历提供对每日和每周报告的访问。

可以使用相应的服务（例如`pgbadger @ 9.6-main.service`）或使用[portal]（＃portal）触发这些报告的手动更新。
*所有*报告的更新可以使用该服务 `pgbadger.service`触发.

*注意:* 更改postgresql.conf设置，如`log_line_prefix`或
`lc_messages`可能导致pgBadger报告不再被更新。

![pgBadger overview](/pgbadger-overview.png) 

## Web 终端 - Shell In A Box

Shell In A Box是一个方便的基于Web的终端。它可以像普通的控制台连接一样使用。
需要显式登录和身份验证。
要更改设置（例如颜色主题），只需右键单击终端窗口上的任意位置即可。

### 更多资源

* <https://github.com/shellinabox/shellinabox>

## 防火墙 - ferm

默认情况下，ferm作为前端安装，用于创建iptables规则。
默认配置部署在以下位置：

* `/etc/ferm.conf`
* `/etc/ferm.d/elephant-shed.conf`

传入流量的默认策略设置为DROP。
允许本地流量，我们明确将以下传入流量列入白名单：

* state `RELATED`,`ESTABLISHED`
* ICMP (ping)
* SSH
* HTTP / HTTPS
* PostgreSQL: Ports 5432-5439 (first 8 clusters)

网络访问的每个附加服务也需要列入白名单。
要配置自己的防火墙规则，只需在`/ etc / ferm.d /`中创建一个扩展名为`.conf`的ferm配置。
这些配置文件在重启时由ferm自动加载。

*注意:* Prometheus和Grafana等服务默认只能通过
管理身份验证的反向代理。如果提供这些服务
通过网络可以到达，必须采取预防措施。

### 更多资源

* <http://ferm.foo-projects.org/>
* <https://wiki.debian.org/ferm>

## 远程控制 - tmate

tmate是流行的终端多路复用器tmux的分支。
如果需要，它用于提供远程支持。

它预先配置为连接到中继服务器（`tmate.credativ.com`），并允许用户通过发送包含秘密令牌的SSH命令与第三方共享当前终端。

有两种操作模式，读写和只读。
这使用户能够向第三方临时访问当前终端。
用户可以随时观看终端并审核第三方采取的行动。

* tmate 默认不运行，需要时再运行
* 当启动shell关闭时，连接也会关闭
* 使用的后端是完全可配置的（在`/etc/tmate.conf`中）并预设为`tmate.credativ.com`
* tmate包含在技术预览中以评估潜力

### Usage

开始 tmate (打开一个终端)

```
tmate
```

显示需要提供给第三方的凭证（安全）

```
tmate show-messages
```

![tmate with multiple panes](/tmate.png) 

有关进一步的用法，请参阅以下有关tmux的其他资源。

### 更多资源

* <https://tmate.io/>
* <https://man.openbsd.org/OpenBSD-current/man1/tmux.1>

## 配置修订 - etckeeper

etckeeper是一组工具和钩子，用于将所有配置保存在git存储库中的`/ etc`中。
提交可以手动完成，也可以通过时间或包管理器挂钩自动完成。

可以查看配置更改并与以前的版本进行比较。
如有必要，可以恢复以前的设置。

#### 更多资源

* <https://etckeeper.branchable.com/>
* <https://www.thomas-krenn.com/de/wiki/Etc-Verzeichnis_mit_etckeeper_versionieren>

# 用户

网页受密码保护(HTTP 基本授权) ，通过PAM使用系统用户. 通过Ansible部署时, 初始化用户 **admin** 密码 **admin**. 该用户可以用户网页可以用户SSH和PostgreSQL。

创建新用户, 使用`adduser`, 增加该用户到 **elephant-shed** 群.

```
adduser myon
adduser myon elephant-shed
```

在 RedHat/CentOS, 使用 `vigr` 增加新用户到 **elephant-shed** 群.
# Limitations

以下支持的 PostgreSQL 版本:

* 11
* 10
* 9.6
* 9.5
* 9.4

其他版本可用但未完全集成在所有组件中。
这可能需要额外的手动调整。

所有PostgreSQL版本的软件包都可以通过Debian / Ubuntu上的* apt.postgresql.org *存储库安装，
和RedHat / CentOS上的* yum.postgresql.org *。
更多信息查看网站 <https://apt.postgresql.org/>.

# 已知的错误和问题

## PostgreSQL

  * `prometheus-sql-exporter`监视代理程序永久保留
    连接对所有数据库开放，这会阻止`DROP DATABASE`
    工作。要删除数据库，请先停止`prometheus-sql-exporter`。
    这可以通过Web界面Cockpit实现：[services＃/ prometheus-sql-exporter.service]（/ system / services＃/ prometheus-sql-exporter.service）。

## pgadmin4

  * pgadmin4 不使用PAM授权。
  * 默认情况下，pgadmin4 不显示本地数据库.

## Portal

  * 注销后直接重新登录不起作用。重新加载页面是必要的。

## RedHat / CentOS

  * 当S​​ELinux启用时，pgAdmin4不起作用.
  * 当SELinux启用时，shellinabox不起作用.
# 证书

Elephant Shed itself 证书在 GPLv3 (<https://www.gnu.org/licenses/gpl-3.0.de.html>).

所有捆绑组件都是免费/开源软件，具有已知且经过批准的开源许可证。

# 支持和更多
## 你有问题吗？或想要知道的更多？

* **Project page** [elephant-shed.io](https://elephant-shed.io)
* **Git** [github.com/credativ/elephant-shed](https://github.com/credativ/elephant-shed/)
* **Web-Chat** [#elephant-shed](https://webchat.oftc.net/?nick=web-user-.&channels=elephant-shed&uio=MT11bmRlZmluZWQmMj10cnVlJjk9dHJ1ZSYxMT0yMzY31)
* **IRC** [#elephant-shed](https://webchat.oftc.net/?channels=elephant-shed&uio=MT11bmRlZmluZWQmMj10cnVlJjk9dHJ1ZSYxMT0yMzY31
) on [irc.oftc.net](https://www.oftc.net/)

## 你需要专业支持或者更多服务吗？

Elephant Shed 是一个开源项目, 开发维护是由 cre<span style="color: red;">d</span>ativ.

对于Elephant Shed PostgreSQL应用, cre<span style="color: red;">d</span>ativ 提供全面的技术支持, 一年365天，一天24小时提供服务.

安装和集成支持，以及介绍也是Elephant Shed PostgreSQL credativ其中一部分服务。如果您有兴趣，请随时与我们联系。

![logo_credativ_96.png](/logo_credativ_96.png) 

* **Web** [credativ.de](https://credativ.de)
* **E-Mail:** [info@credativ.de](mailto:info@credativ.de)
* **Phone:** [+49 2166 9901-0](tel:+49216699010)

