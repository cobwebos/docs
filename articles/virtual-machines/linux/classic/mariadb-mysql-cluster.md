---
title: 在 Azure 上运行 MariaDB (MySQL) 群集 | Microsoft 文档
description: 在 Azure 虚拟机上创建 MariaDB + Galera MySQL 群集
services: virtual-machines-linux
documentationcenter: ''
author: sabbour
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
ms.openlocfilehash: 2cdc58a827f696d62e6240b90202ee04ce371d07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426847"
---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>MariaDB (MySQL) 群集：Azure 教程
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Azure 资源管理器](../../../resource-manager-deployment-model.md)和经典。 本文介绍经典部署模型。 Microsoft 建议大多数新部署使用 Azure 资源管理器模型。

> [!NOTE]
> MariaDB Enterprise 群集现已在 Azure 市场中推出。 这款新产品可在 Azure 资源管理器上自动部署 MariaDB Galera 群集。 应从 [Azure 市场](https://azure.microsoft.com/marketplace/partners/mariadb/cluster-maxscale/)使用这款新产品。
>
>

本文说明如何创建 [MariaDBs](https://mariadb.org/en/about/) 的多主机 [Galera](http://galeracluster.com/products/) 群集（MySQL 的嵌入式替代版本，具有稳健性、可伸缩性和可靠性），可在 Azure 虚拟机上的高度可用环境中使用。

## <a name="architecture-overview"></a>体系结构概述
本文介绍如何完成以下步骤：

- 创建一个三节点群集。
- 将数据磁盘与 OS 磁盘隔离开来。
- 在 RAID-0/条带化设置下创建数据磁盘，以提高 IOPS。
- 使用 Azure 负载均衡器，使 3 个节点的负载保持均衡。
- 为了最大程度地减少重复工作，可创建一个包含 MariaDB + Galera 的 VM 映像，并将其用于创建其他群集 VM。

![系统体系结构](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> 本主题使用 [Azure CLI](../../../cli-install-nodejs.md) 工具，因此请务必根据说明下载这些工具并其连接到 Azure 订阅。 如果需要有关 Azure CLI 中可用命令的参考，请参阅 [Azure CLI 命令参考](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。 还需要[创建用于身份验证的 SSH 密钥]，并记下 .pem 文件的位置。
>
>

## <a name="create-the-template"></a>创建模板
### <a name="infrastructure"></a>基础结构
1. 创建地缘组，以便将资源保存在一起。

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
1. 创建虚拟网络。

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
1. 创建存储帐户，以托管所有磁盘。 不要将超过 40 个常用磁盘放置在同一存储帐户上，以免达到存储帐户的 20,000 IOPS 上限。 在本例中，我们的磁盘数量远低于该限制，所以为了简单起见，我们将所有磁盘都存储在同一帐户上。

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
1. 查找 CentOS 7 虚拟机映像的名称。

        azure vm image list | findstr CentOS
   输出将类似于 `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`。

   在下一步中使用该名称。
1. 创建 VM 模板，并将 /path/to/key.pem 替换为生成的 .pem SSH 密钥的存储路径。

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
1. 将四个 500-GB 的数据磁盘附加到 VM，以便在 RAID 配置中使用。

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
1. 通过 SSH 登录到在 mariadbhatemplate.cloudapp.net:22 创建的模板 VM，并使用私钥进行连接。

### <a name="software"></a>软件
1. 获取根。

        sudo su

1. 安装 RAID 支持：

    a. 安装 mdadm。

              yum install mdadm

    b. 创建具有 EXT4 文件系统的 RAID0/条带化配置。

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. 创建装入点目录。

              mkdir /mnt/data
    d. 检索新创建的 RAID 设备的 UUID。

              blkid | grep /dev/md0
    e. 编辑 /etc/fstab。

              vi /etc/fstab
    f. 添加设备，以便在重新启动时自动装载，并将 UUID 替换为前面从 **blkid** 命令获取的值。

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. 装载新分区。

              mount /mnt/data

1. 安装 MariaDB。

    a. 创建 MariaDB.repo 文件。

                vi /etc/yum.repos.d/MariaDB.repo

    b. 使用以下内容填充 repo 文件：

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. 为避免冲突，请删除现有后缀和 mariadb-libs。

           yum remove postfix mariadb-libs-*
    d. 安装包含 Galera 的 MariaDB。

           yum install MariaDB-Galera-server MariaDB-client galera

1. 将 MySQL 数据目录移到 RAID 块设备。

    a. 将当前 MySQL 目录复制到其新位置，然后删除旧目录。

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. 相应地设置新目录的权限。

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. 创建一个符号链接，将旧目录指向 RAID 分区上的新位置。

           ln -s /mnt/data/mysql /var/lib/mysql

1. 由于 [SELinux 会干扰群集操作](http://galeracluster.com/documentation-webpages/configuration.html#selinux)，因此在当前会话中有必要将其禁用。 编辑 `/etc/selinux/config` 以禁止其后续重新启动。

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
1. 验证 MySQL 是否运行。

   a. 启动 MySQL。

           service mysql start
   b. 保护 MySQL 安装、设置根密码、删除匿名用户，禁用远程根登录并删除测试数据库。

           mysql_secure_installation
   c. 在数据库上创建一个用户，以便在群集操作中使用，也可以选择在应用程序中使用。

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. 停止 MySQL。

            service mysql stop
1. 创建配置占位符。

   a. 编辑 MySQL 配置，以便为群集设置创建一个占位符。 暂时不要替换 **`<Variables>`** 或取消注释。 在基于此模板创建 VM 后，才需执行此操作。

            vi /etc/my.cnf.d/server.cnf
   b. 编辑 **[galera]** 节并将其清除。

   c. 编辑 **[mariadb]** 节。

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
1. 使用 CentOS 7 上的 FirewallD 打开防火墙上的所需端口。

   * MySQL：`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA：`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST：`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC：`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * 重新加载防火墙：`firewall-cmd --reload`

1. 优化系统性能。 有关详细信息，请参阅[性能优化策略](optimize-mysql.md)。

   a. 再次编辑 MySQL 配置文件。

            vi /etc/my.cnf.d/server.cnf
   b. 编辑 **[mariadb]** 节，在其后追加以下内容：

   > [!NOTE]
   > 建议 innodb\_buffer\_pool_size 为 VM 内存量的 70%。 在此示例中，它已设置为 2.45 GB，因为中型 Azure VM 具有 3.5 GB 的 RAM。
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
1. 停止 MySQL 并禁止 MySQL 服务在启动时运行，以免在添加节点时导致群集混乱，并取消预配计算机。

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
1. 通过门户捕获 VM。 （目前，[Azure CLI 工具中的问题 #1268](https://github.com/Azure/azure-xplat-cli/issues/1268) 描述的事实是，Azure CLI 工具所捕获的映像并没有捕获所附加的数据磁盘。）

    a. 通过门户关闭计算机。

    b. 单击“捕获”，然后将映像名称指定为 **mariadb-galera-image**。 提供说明并选中“我已运行 waagent”。
      
      ![捕获虚拟机](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>创建群集
使用已创建的模板创建三个 VM，并配置并启动群集。

1. 从已创建的 mariadb-galera-image 映像创建第一个 CentOS 7 VM，并提供以下信息：

 - 虚拟网络名称：mariadbvnet
 - 子网：mariadb
 - 虚拟机大小：中型
 - 云服务名称：mariadbha（或要通过 mariadbha.cloudapp.net 访问的任何名称）
 - 虚拟机名称：mariadb1
 - 用户名：azureuser
 - SSH 访问：已启用
 - 传递 SSH 证书 .pem 文件，将 /path/to/key.pem 替换为已生成的 .pem SSH 密钥的存储路径。

   > [!NOTE]
   > 为清楚起见，以下命令已拆分为多行，但你应将每条命令作为一行输入。
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
1. 通过将虚拟机连接到 mariadbha 云服务创建两个以上的虚拟机。 更改 VM 名称并将 SSH 端口更改为唯一端口，使其不与同一云服务中的其他 VM 冲突。

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  对于 MariaDB3：

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
1. 下一步将需要获取这三个 VM 的内部 IP 地址：

    ![获取 IP 地址](./media/mariadb-mysql-cluster/IP.png)
1. 使用 SSH 登录到这三个 VM，并编辑每个 VM 的配置文件。

        sudo vi /etc/my.cnf.d/server.cnf

    通过删除行首的 **#** 取消注释 **`wsrep_cluster_name`** 和 **`wsrep_cluster_address`**。
    此外，将 **`wsrep_node_address`** 中的 **`<ServerIP>`** 和 **`wsrep_node_name`** 中的 **`<NodeName>`** 分别替换为 VM 的 IP 地址和名称，然后同样取消注释这些行。
1. 启动 MariaDB1 上的群集，并让其在启动时运行。

        sudo service mysql bootstrap
        chkconfig mysql on
1. 在 MariaDB2 和 MariaDB3 上启动 MySQL，并让其在启动时运行。

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>对群集进行负载均衡
创建群集 VM 时，已将其添加到了名为 clusteravset 的可用性集中，确保其放置在不同的容错域和更新域上，且 Azure 不会同时在所有虚拟机上执行维护。 此配置符合 Azure 服务级别协议 (SLA) 将支持的要求。

现在，使用 Azure 负载均衡器在 3 个节点之间均衡请求。

使用 Azure CLI 在计算机上运行以下命令。

命令参数结构是：`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

CLI 将负载均衡器探测间隔设置为 15 秒，这可能有点太长。 可在门户中任何 VM 的“终结点”下更改它。

![编辑终结点](./media/mariadb-mysql-cluster/Endpoint.PNG)

选择“重新配置负载均衡集”。

![重新配置负载均衡集](./media/mariadb-mysql-cluster/Endpoint2.PNG)

将“探测间隔”更改为 5 秒，并保存更改。

![更改探测间隔](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>验证群集
繁琐的工作已经完成。 现在应该可以在 `mariadbha.cloudapp.net:3306` 访问群集，这会触发负载均衡器并在三个 VM 之间顺利、高效地路由请求。

使用偏好的 MySQL 客户端进行连接，或从其中一个 VM 进行连接，以验证此群集是否正常运行。

     mysql -u cluster -h mariadbha.cloudapp.net -p

然后，创建数据库并在其中填充一些数据。

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

创建的数据库将返回以下表：

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
在本文中，在运行 CentOS 7 的 Azure 虚拟机上创建了包含三个节点的 MariaDB + Galera 高度可用群集。 这些 VM 通过 Azure 负载均衡器实现了负载均衡。

你可能需要了解[在 Linux 上对 MySQL 进行群集的其他方式](mysql-cluster.md)，并探究如何[优化和测试 Azure Linux VM 上的 MySQL 性能](optimize-mysql.md)。

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[创建用于身份验证的 SSH 密钥]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
