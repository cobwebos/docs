---
title: 适用于 Linux 的 Azure 上的 SAP BusinessObjects BI 平台部署 |Microsoft Docs
description: 在适用于 Linux 的 Azure 上部署和配置 SAP BusinessObjects BI 平台
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 7253e257f9d721c09f2e041c1473a9d81d09a321
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094260"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>适用于 Azure 上的 linux 的 SAP BusinessObjects BI 平台部署指南

本文介绍在适用于 Linux 的 Azure 上部署 SAP BOBI 平台的策略。 在此示例中，配置了两个高级 SSD 托管磁盘作为其安装目录的虚拟机。 Azure Database for MySQL 用于 CMS 数据库，文件存储库服务器的 Azure NetApp 文件在两个服务器之间共享。 默认 Tomcat Java web 应用程序和 BI 平台应用程序一起安装在这两个虚拟机上。 若要对用户请求进行负载平衡，将使用应用程序网关，该网关具有本机 TLS/SSL 卸载功能。

这种体系结构适用于小型部署或非生产环境。 对于生产或大规模部署，你可以为 Web 应用程序提供单独的主机，并且还可以有多个 BOBI 应用程序托管，使服务器能够处理详细信息。

![适用于 Linux 的 Azure 上的 SAP BOBI 部署](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

在此示例中，使用了 "产品版本" 和 "文件系统布局"

- SAP BusinessObjects Platform 4。3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (版本： 8.0.15) 
- MySQL C API 连接器-libmysqlclient (版本： 6.1.11) 

| 文件系统        | 说明                                                                                                               | 大小(GB)             | 所有者  | 组  | 存储                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | 用于安装 SAP BOBI 实例、默认 Tomcat Web 应用程序和数据库驱动程序的文件系统 (如有必要)  | SAP 大小调整准则 | bl1adm | sapsys | 托管高级磁盘-SSD |
| /usr/sap/frsinput  | 装载目录适用于所有 BOBI 主机上用于共享文件的共享文件，这些主机将用作输入文件存储库目录  | 业务需求         | bl1adm | sapsys | Azure NetApp 文件         |
| /usr/sap/frsoutput | 装载目录适用于所有 BOBI 主机上用于共享文件的共享文件，这些主机将用作输出文件存储库目录 | 业务需求         | bl1adm | sapsys | Azure NetApp 文件         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>通过 Azure 门户部署 linux 虚拟机

在本部分中，我们将创建两个虚拟机， (Vm) Linux 操作系统 (OS) 映像用于 SAP BOBI 平台。 用于创建虚拟机的高级步骤如下所示：

1. 创建 [资源组](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. 创建 [虚拟网络](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)。

   - 请勿在 SAP BI 平台部署中为所有 Azure 服务使用单个子网。 基于 SAP BI 平台体系结构，需要创建多个子网。 在此部署中，我们将创建三个子网-应用程序子网、文件存储库存储子网和应用程序网关子网。
   - 在 Azure 中，应用程序网关和 Azure NetApp 文件始终需要位于单独的子网中。 有关更多详细信息，请查看[Azure NetApp 文件的](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) [Azure 应用程序网关](../../../application-gateway/configuration-overview.md)和准则网络规划文章。

3. 创建可用性集。

   - 若要为多实例部署中的每个层实现冗余，请将每个层的虚拟机放置在可用性集中。 请确保根据你的体系结构分离每个层的可用性集。

4. 创建虚拟机 1 ** (azusbosl1) 。**

   - 你可以使用自定义映像，也可以从 Azure Marketplace 中选择一个映像。 请参阅 [从适用于 sap 的 Azure Marketplace 部署 vm](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) ，或使用基于需要的 [sap 自定义映像部署 vm](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) 。

5. 创建虚拟机 2 ** (azusbosl2) 。**
6. 添加一个高级 SSD 磁盘。 它将用作 SAP BOBI 安装目录。

## <a name="provision-azure-netapp-files"></a>预配 Azure NetApp 文件

在继续安装 Azure NetApp 文件之前，请先熟悉 Azure [Netapp 文件文档](../../../azure-netapp-files/azure-netapp-files-introduction.md)。

Azure NetApp 文件在多个 [azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中提供。 查看所选 Azure 区域是否提供 Azure NetApp 文件。

使用 azure [区域的 Azure Netapp 文件可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) 页面查看按区域的 Azure netapp 文件可用性。

在部署 Azure netapp 文件之前，请通过转到 [注册 azure](../../../azure-netapp-files/azure-netapp-files-register.md) netapp 文件说明来请求加入 Azure netapp 文件。

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源

以下说明假定你已部署 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。 Azure NetApp 文件资源和将在其中装载 Azure NetApp 文件资源的 Vm 必须部署在同一 Azure 虚拟网络或对等互连 Azure 虚拟网络中。

1. 如果尚未部署资源，请请求 [加入 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-register.md)。

2. 按照 [创建 netapp 帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的说明，在所选的 Azure 区域中创建一个 netapp 帐户。

3. 按照 [设置 Azure Netapp 文件容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明设置 Azure netapp 文件容量池。

   - 本文中介绍的 SAP BI 平台体系结构使用 *高级* 服务级别的单个 Azure NetApp 文件容量池。 对于 Azure 上的 SAP BI 文件存储库服务器，我们建议使用 Azure NetApp 文件*高级*或*超*[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。

4. 按照将 [子网委托给 Azure Netapp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的说明，将子网委托给 Azure netapp 文件。

5. 按照 [为 Azure Netapp 文件创建 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)中的说明部署 Azure netapp 文件量。

   可以将和卷部署为 NFSv3 和 NFSv 4.1，因为 SAP BOBI 平台支持这两种协议。 在各自的 Azure NetApp 文件子网中部署卷。 将自动分配 Azure NetApp 卷的 IP 地址。

请记住，Azure NetApp 文件资源和 Azure Vm 必须位于同一个 Azure 虚拟网络中，或者位于对等互连 Azure 虚拟网络中。 例如，azusbobi-frsinput，azusbobi-frsoutput 是卷名和 nfs://10.31.2.4/azusbobi-frsinput，nfs://10.31.2.4/azusbobi-frsoutput 是 Azure NetApp 文件卷的文件路径。

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput) 
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput) 

### <a name="important-considerations"></a>重要注意事项

在为 SAP BOBI Platform 文件存储库服务器创建 Azure NetApp 文件时，请注意以下事项：

1. 最小容量池为 4 tib (TiB) 。
2. 最小卷大小为 100 gb (GiB) 。
3. Azure NetApp 文件以及将在其中装入 Azure NetApp 文件的所有虚拟机必须位于同一区域中的同一 Azure 虚拟网络或 [对等互连虚拟网络](../../../virtual-network/virtual-network-peering-overview.md) 中。 现在支持通过同一区域中的 VNET 对等互连进行 Azure NetApp 文件访问。 目前尚不支持通过全局对等互连进行 Azure NetApp 访问。
4. 所选虚拟网络必须具有委托给 Azure NetApp 文件的子网。
5. 使用 Azure NetApp 文件 [导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，可以控制允许的客户端、访问类型 (读写、只读等) 。
6. Azure NetApp 文件功能尚未识别区域。 目前，该功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。
7. Azure NetApp 文件卷可以部署为 NFSv3 或 NFSv4.1 卷。 SAP BI 平台应用程序支持这两种协议。

## <a name="configure-file-systems-on-linux-servers"></a>在 linux 服务器上配置文件系统

本部分中的步骤使用以下前缀：

**[A]**：该步骤适用于所有主机

### <a name="format-and-mount-sap-file-system"></a>格式化和装载 SAP 文件系统

1. **[A]** 列出所有附加的磁盘

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** 用于/Usr/sap 的格式块设备

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** 创建装载目录

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** 获取块设备的 UUID

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** 在/Etc/fstab 中维护文件系统装载条目

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** 装载文件系统

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>装载 Azure NetApp 文件卷

1. **[A]** 创建装载目录

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** 将客户端操作系统配置为支持 Nfsv 4.1 Mount ** (仅当使用 Nfsv 4.1 时才适用) **

   如果要将 Azure NetApp 文件卷用于 NFSv 4.1 协议，请在所有 Vm 上执行以下配置，其中 Azure NetApp 文件 NFSv 4.1 卷需要装入。

   **验证 NFS 域设置**

   请确保将该域配置为默认的 Azure NetApp 文件域，即  **defaultv4iddomain.com** ，并将映射设置为无 **人**。

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > 确保在 VM 上设置/etc/idmapd.conf 中的 NFS 域，使其与 Azure NetApp 文件上的默认域配置匹配： **defaultv4iddomain.com**。 如果 NFS 客户端上的域配置不匹配 (例如，VM) 和 NFS 服务器，即 Azure NetApp 配置，则在 Vm 上装载的 Azure NetApp 卷上的文件权限将显示为无人。

   验证 `nfs4_disable_idmapping`。 它应设置为“Y”。若要创建 `nfs4_disable_idmapping` 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** 添加装载条目

   如果使用 NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   如果使用 NFSv 4。1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** 装载 NFS 卷

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>配置 CMS 数据库-Azure database for MySQL

本部分提供有关如何使用 Azure 门户预配 Azure Database for MySQL 的详细信息。 它还提供了有关如何为 SAP BOBI 平台创建 CMS 和审核数据库的说明，以及用于访问数据库的用户帐户的说明。

仅当使用 Azure DB for MySQL 时，这些准则才适用。 有关 () 的其他数据库，请参阅 SAP 或数据库特定的文档以了解相关说明。

### <a name="create-an-azure-database-for-mysql"></a>创建用于 MySQL 的 Azure 数据库

登录到 Azure 门户并按照本 [快速入门指南的 Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md#create-an-azure-database-for-mysql-server)中所述的步骤进行操作。 预配 Azure Database for MySQL 的几点注意事项

1. 为运行 SAP BI 平台应用程序服务器的 Azure Database for MySQL 选择同一区域。

2. 基于产品可用性矩阵选择受支持的 DB 版本 [ (PAM) ，适用于 SAP BI 的](https://support.sap.com/pam) 特定于 sap BOBI 版本。 遵循适用于在 SAP PAM 中为 MySQL AB 解决的兼容性准则

3. 在 "计算 + 存储" 中，选择 " **配置服务器** "，并根据调整大小的输出选择适当的定价层。

4. 默认情况下启用**存储自动增长**。 请记住， [存储](../../../mysql/concepts-pricing-tiers.md#storage) 只能向上扩展，而不是关闭。

5. 默认情况下， **备份保留期** 为七天，但你可以 [根据需要将其配置](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) 为35天。

6. 默认情况下，Azure Database for MySQL 的备份是本地冗余的，因此，如果要在异地冗余存储中备份服务器，请从**备份冗余选项**中选择 "**地域冗余**"。

> [!NOTE]
> 不支持在创建服务器后更改 [备份冗余选项](../../../mysql/concepts-backup.md#backup-redundancy-options) 。

### <a name="configure-connection-security"></a>配置连接安全性

默认情况下，使用防火墙保护创建的服务器，并且无法公开访问。 若要提供对运行 SAP BI 平台应用程序服务器的虚拟网络的访问权限，请执行以下步骤-  

1. 中转到 Azure 门户中的 "服务器资源"，然后从服务器资源的左侧菜单中选择 " **连接安全性** "。
2. 选择 **"是"** 以 **允许访问 Azure 服务**。
3. 在 "VNET 规则" 下，选择 " **添加现有虚拟网络**"。 选择 SAP BI Platform 应用程序服务器的虚拟网络和子网。 此外，还需要提供对跳转框或其他服务器的访问权限，通过这些服务器可将 [MySQL 工作台](../../../mysql/connect-workbench.md) 连接到 Azure Database for MySQL。 MySQL 工作台将用于创建 CMS 和审核数据库
4. 添加虚拟网络后，请选择 " **保存**"。

### <a name="create-cms-and-audit-database"></a>创建 CMS 和审核数据库

1. 从 [mysql 网站](https://dev.mysql.com/downloads/workbench/)下载并安装 mysql 工作台。 请确保在可访问 Azure Database for MySQL 的服务器上安装 MySQL 工作台。

2. 使用 MySQL 工作台连接到服务器。 请按照 [本文](../../../mysql/connect-workbench.md#get-connection-information)中所述的说明进行操作。 如果成功连接测试，则会收到以下消息：

   ![SQL 工作台连接](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. 在 "SQL 查询" 选项卡中，运行以下查询以创建 CMS 和审核数据库的架构。

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;

4. Create user account to connect to schema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. 检查 MySQL 用户帐户的权限和角色

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>在 linux 服务器上安装 (libmysqlclient) 的 MySQL C API 连接器

要使 SAP BOBI 应用程序服务器访问数据库，它需要数据库客户端/驱动程序。 适用于 Linux 的 MySQL C API 连接器必须用于访问 CMS 和审核数据库。 不支持与 CMS 数据库的 ODBC 连接。 本部分提供有关如何在 Linux 上设置 MySQL C API 连接器的说明。

1. 请参阅 [与 Azure Database for MySQL 文章兼容的 MySQL 驱动程序和管理工具](../../../mysql/concepts-compatibility.md) ，其中描述了与 Azure Database for MySQL 兼容的驱动程序。 请参阅文章中的 **MySQL Connector/C (libmysqlclient) ** 驱动程序。

2. 请参阅此 [链接](https://downloads.mysql.com/archives/c-c/) 下载驱动程序。

3. 选择操作系统并下载 MySQL 连接器的共享组件 rpm 包。 在此示例中，使用了 mysql-6.1.11 连接器版本。

4. 在所有 SAP BOBI 应用程序实例中安装连接器。

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. 检查 libmysqlclient.so 的路径

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. 将 LD_LIBRARY_PATH 设置为指向 `/usr/lib64` 将用于安装的用户帐户的目录。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>服务器准备

本部分中的步骤使用以下前缀：

**[A]**：该步骤适用于所有主机。

1. **[A]** 根据 LINUX (SLES 或 RHEL) 的风格，需要设置内核参数并安装所需的库。 请参阅[适用于 Unix 的商业智能平台安装指南中的](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)"**系统要求**" 部分。

2. **[A]** 确保计算机上的时区设置正确。 请参阅安装指南中的 " [其他 Unix 和 Linux 要求" 部分](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) 。

3. **[A]** 创建用户帐户 (**bl1**adm) 并组 (在其下运行软件的后台进程的 sapsys) 。 使用此帐户执行安装并运行软件。 帐户不需要根权限。

4. **[A]** 设置用户帐户 (**bl1**adm) 环境使用支持的 utf-8 区域设置，并确保控制台软件支持 utf-8 字符集。 若要确保操作系统使用正确的区域设置，请在 (**bl1**adm) 用户环境中将 LC_ALL 和 LANG 环境变量设置为首选区域设置。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** 配置用户帐户 (**bl1**adm) 。

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. 从 SAP 服务应用商店下载并提取 SAP BusinessObjects BI 平台的媒体。

## <a name="installation"></a>安装

检查服务器上的用户帐户 **bl1**adm 的区域设置

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

导航到 SAP BusinessObjects BI Platform 的媒体，并通过 **bl1**adm 用户运行以下命令-

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

遵循适用于 Unix 的 [SAP BOBI 平台](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) 安装指南（特定于你的版本）。 安装 SAP BOBI 平台时要注意几个要点。

- 在 " **配置产品注册** " 屏幕上，你可以使用 sap 说明 [1288121](https://launchpad.support.sap.com/#/notes/1288121) 中 sap BusinessObjects 解决方案的临时许可证密钥，或者可以在 sap 服务 Marketplace 中生成许可证密钥

- 在 " **选择安装类型** " 屏幕上，选择 "在第一台服务器上 **完全** 安装 (azusbosl1") 上，为其他服务器 (Azusbosl2) 选择 " **自定义/展开** "，这将展开现有 BOBI 设置。

- 在 " **选择默认值或现有数据库** " 屏幕上，选择 " **配置现有数据库**"，这将提示你选择 CMS 和审核数据库。 选择 " **MySQL** " "数据库类型" 和 "审核数据库类型"。

  如果不想在安装期间配置审核，还可以选择 "无审核数据库"。

- 在基于 SAP BOBI 体系结构的 " **选择 Java Web 应用程序服务器" 屏幕** 上选择适当的选项。 在此示例中，我们选择了选项1，它在同一 SAP BOBI 平台上安装 tomcat 服务器。

- 在 **配置 Cms 存储库数据库-MySQL**中输入 cms 数据库信息。 用于 Linux 安装的 CMS 数据库信息的示例输入。 Azure Database for MySQL 用于默认端口3306
  
  ![Linux 上的 SAP BOBI 部署-CMS 数据库](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

-  (可选) 在 " **配置审核存储库数据库-MySQL**" 中输入审核数据库信息。 用于 Linux 安装的审核数据库信息的示例输入。

  ![Linux 上的 SAP BOBI 部署-审核数据库](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 按照说明进行操作，并输入所需的输入来完成安装。

对于多实例部署，请在第二个主机上运行安装安装程序 (azusbosl2) 。 在 " **选择安装类型** " 屏幕中，选择 " **自定义/展开** "，将展开现有的 BOBI 设置。

在 Azure database for MySQL 产品中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。 因此，在中央管理控制台 (CMC) ，你会发现不同的数据库版本基本上是在网关上设置的版本。 有关更多详细信息，请查看 [支持的 Azure Database for MySQL 服务器版本](../../../mysql/concepts-supported-versions.md) 。

![Linux 上的 SAP BOBI 部署-CMC 设置](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>安装后

### <a name="tomcat-clustering---session-replication"></a>Tomcat 群集-会话复制

Tomcat 支持将两个或更多应用程序服务器用于会话复制和故障转移。 SAP BOBI 平台会话将进行序列化，即使应用程序服务器发生故障，用户会话也可以无缝地故障转移到另一个 tomcat 实例。

例如，如果用户连接到的 web 服务器在 SAP BI 应用程序中导航文件夹层次结构时失败。 使用正确配置的群集，用户可以继续导航文件夹层次结构，而无需重定向到登录页。

在 SAP 说明 [2808640](https://launchpad.support.sap.com/#/notes/2808640)中，使用多播提供配置 tomcat 群集的步骤。 但在 Azure 中，不支持多播。 因此，若要在 Azure 中运行 Tomcat 群集，必须使用 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP 说明 [2764907](https://launchpad.support.sap.com/#/notes/2764907)) 。 在 SAP 博客上 [使用 Sap BUSINESSOBJECTS BI 平台的静态成员身份检查 Tomcat 群集](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) ，在 Azure 中设置 Tomcat 群集。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI 平台负载平衡的 web 层

在 SAP BOBI 多实例部署中，) 在两个或多个主机上运行 (web 层的 Java Web 应用程序服务器。 若要在 web 服务器之间平均分配用户负载，可以在最终用户和 web 服务器之间使用负载均衡器。 在 Azure 中，可以使用 Azure 负载均衡器或 Azure 应用程序网关来管理 web 应用程序服务器的流量。 下一节将介绍有关每个产品的详细信息。

#### <a name="azure-load-balancer-network-based-load-balancer"></a>基于网络的负载均衡器 (的 Azure 负载均衡器) 

[Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md) 是高性能、低延迟的第4层 (TCP、UDP) 负载均衡器，可在正常运行的虚拟机之间分配流量。 负载均衡器运行状况探测监视每个 VM 上的给定端口，并仅将流量分配给运行的虚拟机 () 。 你可以根据是否想要从 internet 访问 SAP BI 平台，选择公共负载均衡器或内部负载均衡器。 它的区域冗余，确保了跨可用性区域的高可用性。

请参阅下图中的内部负载均衡器部分，其中的 web 应用程序服务器运行在端口8080上，默认为 Tomcat HTTP 端口，将由运行状况探测进行监视。 因此，来自最终用户的任何传入请求将被重定向到后端池中 (azusbosl1 或 azusbosl2) 的 web 应用程序服务器。 负载均衡器不支持 TLS/SSL 终止 (也称为 TLS/SSL 卸载) 。 如果使用 Azure 负载均衡器将流量分布到 web 服务器，我们建议使用标准负载均衡器。

> [!NOTE]
> 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](high-availability-guide-standard-load-balancer-outbound-connections.md)。

![跨 Web 服务器平衡流量的 Azure 负载均衡器](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure 应用程序网关 (web 应用程序负载均衡器) 

[Azure 应用程序网关 (AGW) ](../../../application-gateway/overview.md) 提供应用程序传送控制器 (ADC) 作为服务，用于帮助应用程序将用户流量定向到一个或多个 web 应用程序服务器。 它提供了各种第7层负载均衡功能，例如 TLS/SSL 卸载、Web 应用程序防火墙 (WAF) 、基于 Cookie 的会话相关性和应用程序的其他功能。

在 SAP BI 平台中，应用程序网关将应用程序 web 流量定向到后端池的指定资源-azusbosl1 或 azusbos2。 将侦听器分配给端口，创建规则，然后将资源添加到后端池。 在下图中，具有专用前端 IP 地址 (10.31.3.20 的应用程序网关) 充当用户的入口点，处理传入的 TLS/SSL (HTTPS/443) 连接，对 TLS/SSL 进行解密，并将未加密的 (请求传递到后端池中的服务器。 通过内置的 TLS/SSL 终止功能，我们只需在应用程序网关上维护一个 TLS/SSL 证书，这可以简化操作。

![应用程序网关，用于平衡跨 Web 服务器的流量](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

若要配置适用于 SAP BOBI Web 服务器的应用程序网关，可以参阅 SAP 博客上的 [使用 Azure 应用程序网关实现 SAP BOBI 的负载平衡](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) 。

> [!NOTE]
> 我们建议使用 Azure 应用程序网关对流量进行负载平衡，因为它提供 SSL 卸载之类的功能赞、集中 SSL 管理以降低服务器上的加密和解密开销、Round-Robin 算法来分发流量、算法， (WAF) 功能、高可用性等。

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI 平台-备份和还原

备份和还原是将数据和应用程序定期复制到单独位置的过程。 如果原始数据或应用程序丢失或损坏，则可以将其还原或恢复到以前的状态。 它还是任何业务灾难恢复策略的基本组成部分。

若要为 SAP BOBI 平台开发全面的备份和还原策略，请确定导致应用程序系统停机或中断的组件。 在 SAP BOBI 平台中，以下组件的备份对保护应用程序至关重要。

- SAP BOBI 安装目录 (托管高级磁盘) 
- 文件存储库服务器 (Azure NetApp 文件或 Azure 高级文件) 
- CMS 数据库 (Azure Database for MySQL 或 Azure VM 上的数据库) 

以下部分介绍如何为 SAP BOBI 平台上的每个组件实现备份和还原策略。

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>SAP BOBI 安装目录的备份 & 还原

在 Azure 中，备份应用程序服务器和所有附加磁盘的最简单方法是使用 [Azure 备份](../../../backup/backup-overview.md) 服务。 它提供独立且独立的备份来防止 Vm 上数据的意外破坏。 备份存储在提供恢复点内置管理的恢复服务保管库中。 配置和缩放很简单，备份经过优化，可在需要时轻松还原。

作为备份过程的一部分，拍摄快照，并将数据传输到恢复服务保管库，而不会影响生产工作负荷。 快照提供了不同的一致性级别，如 [快照一致性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) 一文中所述。 还可以选择使用选择性磁盘备份和还原功能来备份 VM 中的数据磁盘子集。 有关详细信息，请参阅 [AZURE Vm 备份](../../../backup/backup-azure-vms-introduction.md) 文档和 [常见问题解答-备份 azure vm](../../../backup/backup-azure-vm-backup-faq.md)。

#### <a name="backup--restore-for-file-repository-server"></a>文件存储库服务器的备份 & 还原

对于 **Azure NetApp 文件**，可以创建按需快照，并使用快照策略计划自动快照。 快照副本提供和卷的时间点副本。 有关详细信息，请参阅 [使用 Azure NetApp 文件管理快照](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)。

**Azure 文件** 备份与本机 [Azure 备份](../../../backup/backup-overview.md) 服务集成，该服务将备份和还原功能与 vm 备份一起使用，并简化操作工作。 有关详细信息，请参阅 [Azure 文件共享备份](../../../backup/azure-file-share-backup-overview.md) 和 [常见问题解答-备份 Azure 文件](../../../backup/backup-azure-files-faq.md)。

#### <a name="backup--restore-for-cms-database"></a>CMS 数据库的备份 & 还原

Azure 数据库 MySQL 是 Azure 中的 DBaaS 产品，它可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 Azure 数据库 MySQL 会备份数据文件和事务日志。 根据受支持的最大存储大小，它会将完整和差异备份 (4 TB 的最大存储服务器) 或快照备份 (最多支持 16 TB 的最大存储服务器) 。 这些备份允许你在配置的备份保留期内的任意时间点还原服务器。 默认备份保持期为七天，可以 [选择将其配置](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) 为三天。 所有备份都使用 AES 256 位加密进行加密。

这些备份文件不是用户公开的，无法导出。 这些备份只能用于 Azure Database for MySQL 中的还原操作。 可以使用 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) 复制数据库。 有关详细信息，请参阅 [Azure Database for MySQL 中的备份和还原](../../../mysql/concepts-backup.md)。

对于在虚拟机上安装的数据库，您可以对 HANA 数据库使用标准的备份工具或 [Azure 备份](../../../backup/sap-hana-db-about.md) 。 此外，如果 Azure 服务和工具无法满足你的要求，则可以使用其他备份工具或脚本来创建磁盘备份。

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI 平台可靠性

SAP BusinessObjects BI 平台包含不同的层，这些层针对特定任务和操作进行了优化。 当任何一层中的组件变为不可用时，SAP BOBI 应用程序将变得不可访问，或者应用程序的某些功能将无法正常工作。 因此，需要确保每个层的可靠性，使应用程序无需任何业务中断即可正常运行。

本部分重点介绍适用于 SAP BOBI 平台的以下选项-

- **高可用性：** 如果其中一个服务器不可用，则高可用平台上至少有两个 Azure 区域中的任何一个用于使应用程序正常运行。
- **灾难恢复：** 如果由于某些自然灾害而导致整个 Azure 区域不可用，则还原应用程序功能是一种过程。

此解决方案的实现根据 Azure 中系统设置的性质而有所不同。 因此，客户需要根据业务需求定制高可用性和灾难恢复解决方案。

### <a name="high-availability"></a>高可用性

高可用性是指一组技术，可通过同一数据中心内的冗余、容错或故障转移保护的组件提供应用程序/服务的业务连续性，从而最大程度地降低 IT 中断。 在我们的示例中，数据中心位于一个 Azure 区域内。 适用于 [sap 的高可用性体系结构和方案](sap-high-availability-architecture-scenarios.md) 为 Sap 应用程序提供了针对 Azure 应用程序的不同高可用性技术和建议的初步见解，这将补充本部分中的说明。

根据 SAP BOBI 平台的大小调整结果，你需要设计横向，并确定跨 Azure 虚拟机和子网的 BI 组件分布。 在分布式体系结构中，冗余级别取决于业务所需的恢复时间目标 (RTO) 和恢复点目标 (RPO) 。 SAP BOBI 平台在每个层上都包含不同的层和组件，目的是为了实现冗余。 这样，如果一个组件发生故障，SAP BOBI 应用程序就不会有任何中断。 例如，

- 冗余应用程序服务器，如 BI 应用程序服务器和 Web 服务器
- CMS 数据库、文件存储库服务器、负载均衡器等独特组件

以下部分介绍如何在 SAP BOBI 平台的每个组件上实现高可用性。

#### <a name="high-availability-for-application-servers"></a>应用程序服务器的高可用性

对于 BI 和 Web 应用程序服务器，无论它们是单独安装还是一起安装，都不需要特定的高可用性解决方案。 可以通过冗余实现高可用性，这是在不同的 Azure 虚拟机中配置 BI 和 Web 服务器的多个实例。

若要降低因一个或多个事件导致停机的影响，建议遵循在多个虚拟机上运行的应用程序服务器的高可用性实践。

- 使用可用性区域来保护数据中心故障。
- 在可用性集中配置多个虚拟机以实现冗余。
- 在可用性集中对 Vm 使用托管磁盘。
- 将每个应用层配置为单独的可用性集。

有关详细信息，请参阅 [管理 Linux 虚拟机的可用性](../../manage-availability.md)

#### <a name="high-availability-for-cms-database"></a>CMS 数据库的高可用性

如果使用 Azure 数据库即服务 (DBaaS) service for CMS 数据库，则默认情况下会提供高可用性框架。 只需选择区域和服务固有的高可用性、冗余和复原能力，无需配置任何其他组件。 有关 Azure 上受支持的 DBaaS 产品/服务的 SLA 的更多详细信息，请查看[AZURE SQL 数据库 Azure Database for MySQL 和高可用性](../../../azure-sql/database/high-availability-sla.md)的[高可用性](../../../mysql/concepts-high-availability.md)

对于 CMS 数据库的其他 DBMS 部署，请参阅 [适用于 SAP 工作负荷的 dbms 部署指南](dbms_guide_general.md)，其中提供了有关不同 DBMS 部署及其实现高可用性的方法的见解。

#### <a name="high-availability-for-file-repository-server"></a>文件存储库服务器的高可用性

File Repository Server (FRS) 指的是存储诸如报表、universes 和连接等内容的磁盘目录。 它在该系统的所有应用程序服务器之间共享。 因此必须确保其高度可用。

在 Azure 上，可以选择用于文件共享的 [Azure 高级文件](../../../storage/files/storage-files-introduction.md) 或 [azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md) ，这些文件共享设计为高度可用且持久持久。 有关详细信息，请参阅 Azure 文件的 [冗余](https://docs.microsoft.com/azure/storage/files/storage-files-planning#redundancy) 部分。

> [!NOTE]
> Azure 文件的 SMB 协议已公开发布，但 Azure 文件的 NFS 协议支持目前为预览版。 有关详细信息，请参阅 [Azure 文件的 NFS 4.1 支持现在为预览版](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

由于此文件共享服务在所有区域中都不可用，因此请务必参考 [区域网站提供的产品](https://azure.microsoft.com/en-us/global-infrastructure/services/) 以了解最新信息。 如果该服务在你的区域中不可用，你可以创建 NFS 服务器，从中你可以将文件系统与 SAP BOBI 应用程序共享。 但你还需要考虑它的高可用性。

#### <a name="high-availability-for-load-balancer"></a>负载均衡器的高可用性

若要在 web 服务器之间分配流量，可以使用 Azure 负载均衡器或 Azure 应用程序网关。 可根据为部署选择的 SKU 来实现任一负载均衡器的冗余。

- 对于 Azure 负载均衡器，可以通过将标准负载均衡器前端配置为区域冗余来实现冗余。 有关详细信息，请参阅 [标准负载均衡器和可用性区域](../../../load-balancer/load-balancer-standard-availability-zones.md)
- 对于应用程序网关，可以根据部署过程中选择的层类型来实现高可用性。
  - v1 SKU 在部署两个或更多实例时支持高可用性方案。 Azure 跨更新域和容错域分配这些实例，确保实例不会全部同时发生故障。 对于此 SKU，可以在区域内实现冗余
  - v2 SKU 会自动确保新的实例分散到容错域和更新域中。 如果选择“区域冗余”，则最新实例还将分布到各个可用性区域中以提供区域性故障复原能力。 有关更多详细信息，请参阅自动 [缩放和区域冗余应用程序网关 v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台的参考高可用性体系结构

以下参考体系结构介绍了如何使用可用性集设置 SAP BOBI 平台，该平台提供了区域中的 Vm 冗余和可用性。 该体系结构展示了如何使用不同的 Azure 服务，例如 Azure 应用程序网关、Azure NetApp 文件以及提供内置冗余的 SAP BOBI 平台 Azure Database for MySQL，这降低了管理不同高可用性解决方案的复杂性。

在下图中， (的 TCP/443) 的传入流量使用 Azure 应用程序网关 v1 SKU 进行负载平衡，此功能在两个或更多实例上部署时高度可用。 Web 服务器、管理服务器和处理服务器的多个实例部署在单独的虚拟机中以实现冗余，并且每个层部署在不同的可用性集中。 Azure NetApp 文件在数据中心内有内置冗余，因此文件存储库服务器的和卷将高度可用。 CMS 数据库预配于 Azure Database for MySQL (DBaaS) ，该版本具有固有的高可用性。 有关详细信息，请参阅 [Azure Database for MySQL 指南中的高可用性](../../../mysql/concepts-high-availability.md) 。

![使用可用性集的 SAP BusinessObjects BI 平台冗余](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

上述体系结构提供了有关如何在 Azure 上部署 SAP BOBI 的见解。 但它不涵盖 Azure 上的 SAP BOBI 平台的所有可能的配置选项。 客户可根据其业务需求，为负载均衡器、文件存储库服务器和 DBMS 等不同组件选择不同的产品/服务，从而定制其部署。

在多个 Azure 区域，提供可用性区域，这意味着它具有独立的电源、冷却和网络供应。 它使客户能够跨两个或三个可用性区域部署应用程序。 对于希望跨 AZs 实现高可用性的客户，可以在可用性区域之间部署 SAP BOBI 平台，从而确保应用程序中的每个组件都是区域冗余的。

### <a name="disaster-recovery"></a>灾难恢复

本部分中的说明介绍为 SAP BOBI 平台提供灾难恢复保护的策略。 它补充了 [sap 的灾难恢复](../../../site-recovery/site-recovery-sap.md) 文档，该文档代表总体 SAP 灾难恢复方法的主要资源。

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台的引用灾难恢复体系结构

此参考体系结构正在运行包含冗余应用程序服务器的 SAP BOBI 平台的多实例部署。 对于灾难恢复，应将所有层故障转移到次要区域。 每个层使用不同的策略提供灾难恢复保护。

![SAP BusinessObjects BI 平台灾难恢复](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>负载均衡器

负载均衡器用于在 SAP BOBI 平台的 Web 应用程序服务器之间分配流量。 若要为 Azure 应用程序网关实现 DR，请在次要区域中实现应用程序网关的并行安装。

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>运行 web 和 BI 应用程序服务器的虚拟机

Azure Site Recovery 服务可用于在次要区域中复制运行 Web 和 BI 应用程序服务器的虚拟机。 它复制次要区域中的服务器，以便在发生灾难和中断时，可以轻松地故障转移到复制的环境并继续工作

#### <a name="file-repository-servers"></a>文件存储库服务器

- **Azure NetApp 文件** 提供 NFS 和 SMB 卷，因此，任何基于文件的复制工具都可用于在 Azure 区域之间复制数据。 有关如何在另一个区域复制和卷的详细信息，请参阅 [关于 Azure NetApp 文件的常见问题解答](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  可以使用 Azure NetApp 文件跨区域复制，该复制当前为 [预览版](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) ，使用 NetApp SnapMirror®技术。 因此，只有更改的块以压缩、高效的格式通过网络发送。 这种专有技术可最大程度地减少跨区域复制数据所需的数据量，从而节省数据传输成本。 它还缩短了复制时间，因此你可以获得较小的还原点目标 (RPO) 。 有关详细信息，请参阅 [使用跨区域复制的要求和注意事项](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) 。

- **Azure 高级文件** 仅支持本地冗余 (LRS) 和区域冗余存储 (ZRS) 。 对于 Azure 高级文件 DR 策略，可以使用 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 或 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/) 将文件复制到不同区域中的其他存储帐户。 有关详细信息，请参阅 [灾难恢复和存储帐户故障转移](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS 数据库

Azure Database for MySQL 提供了多个选项，可在发生任何灾难时恢复数据库。 选择适用于你的业务的适当选项。

- 启用跨区域读取副本以增强业务连续性和灾难恢复计划。 可以从源服务器复制到最多5个副本。 只读副本使用 MySQL 的二进制日志复制技术进行异步更新。 副本是新的服务器，可以像管理普通的 Azure Database for MySQL 服务器一样对其进行管理。 有关详细信息，请参阅读取副本、可用区域、限制以及如何从 [读取副本概念](../../../mysql/concepts-read-replicas.md)中进行故障转移。

- 使用 Azure Database for MySQL 的异地还原功能，该功能使用异地冗余备份来还原服务器。 即使承载服务器的区域处于脱机状态，也可访问这些备份。 可以使用这些备份还原到任何其他区域并使服务器恢复联机。

  > [!NOTE]
  > 只有当为服务器预配了异地冗余备份存储时，异地还原才是可行的。 不支持在创建服务器后更改 **备份冗余选项** 。 有关详细信息，请参阅 [备份冗余](../../../mysql/concepts-backup.md#backup-redundancy-options) 一文。

下面是此示例中使用的每个层的灾难恢复建议。

| SAP BOBI 平台层   | 建议                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure 应用程序网关 | 辅助区域上应用程序网关的并行安装                                                |
| Web 应用程序服务器   | 使用 Site Recovery 进行复制                                                                         |
| BI 应用程序服务器    | 使用 Site Recovery 进行复制                                                                         |
| Azure NetApp 文件        | 基于文件的复制工具将数据复制到次要区域 **或** 和跨区域复制 (预览)  |
| Azure Database for MySQL  | 跨区域读取副本 **或** 从异地冗余备份还原备份。                             |

## <a name="next-steps"></a>后续步骤

- [为多层 SAP 应用部署设置灾难恢复](../../../site-recovery/site-recovery-sap.md)
- [适用于 SAP 的 Azure 虚拟机规划和实施](planning-guide.md)
- [适用于 SAP 的 Azure 虚拟机部署](deployment-guide.md)
- [适用于 SAP 的 Azure 虚拟机 DBMS 部署](dbms-guide.md)