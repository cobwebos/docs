---
title: 在 Azure SAP HANA（大型实例）上进行 HANA 备份和还原 | Microsoft Docs
description: 如何在 Azure SAP HANA（大型实例）上执行 HANA 备份和还原
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 69417551c1c8d410f75e74a8164c8b8a223ab835
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58805323"
---
# <a name="backup-and-restore"></a>备份和还原

>[!IMPORTANT]
>本文档不是 SAP HANA 管理文档或 SAP 说明的替代文档。 读者应深入了解 SAP HANA 管理和操作的专业技能，尤其是在备份、还原和高可用性以及灾难恢复主题方面。 本文档中提供 SAP HANA Studio 的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

重要的是使用自己的 HANA 版本或发布，在相应的环境中执行步骤和过程。 本文档中介绍的某些过程进行了简化，以便于理解，不是用作最终操作手册的详细步骤。 如果想针对配置创建操作手册，需要测试并实践相关过程，并记录与特定配置相关的过程。 

在操作数据库方面，一个要点是在发生各种灾难事件时保护数据库。 这些事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。

备份数据库并具备还原到任意时间点（例如，在某人删除关键数据之前的时间点）的能力，可以尽可能地还原到发生中断之前所处的状态。

为获得最佳效果，必须执行两种类型的备份：

- 数据库备份：完整备份、增量备份或差异备份
- 事务日志备份

除了在应用程序级别执行完整数据库备份以外，还可以使用存储快照执行备份。 存储快照不会替代事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 但是，存储快照可以通过快速提供数据库的前滚映像来提升恢复速度。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- 自制 (DIY)。 计算并确保有足够的磁盘空间后，使用以下磁盘备份方法之一执行完整的数据库和日志备份。 可以直接备份到 HANA 大型实例单元上附加的卷，或者备份到 Azure 虚拟机 (VM) 中设置的网络文件共享 (NFS)。 在后一种情况中，客户在 Azure 中设置 Linux VM、将 Azure 存储附加到 VM 并通过该 VM 中配置的 NFS 服务器共享此存储。 如果对直接附加到 HANA 大型实例单元的卷执行备份，需要将备份复制到 Azure 存储帐户（在设置用于基于 Azure 存储导出 NFS 共享的 Azure VM 之后）。 还可以使用 Azure 备份保管库或 Azure 冷存储。 

   另一个选项是将备份复制到 Azure 存储帐户后，使用第三方数据保护工具来存储备份。 对于出于符合性和审核目的而需要长期存储的数据，还有必要使用 DIY 备份选项。 在所有情况下，备份均复制到通过 VM 和 Azure 存储表示的 NFS 共享中。

- 基础结构备份和还原功能。 还可以使用 Azure 上的 SAP HANA（大型实例）底层基础结构提供的备份和还原功能。 此选项满足备份和快速还原的需要。 本部分的余下内容介绍 HANA 大型实例提供的备份和还原功能。 本部分还会介绍备份和还原功能与 HANA 大型实例提供的灾难恢复功能之间的关系。

> [!NOTE]
>   HANA 大型实例底层基础结构使用的快照技术依赖于 SAP HANA 快照。 目前，SAP HANA 快照无法与 SAP HANA 多租户数据库容器的多个租户配合工作。 如果只部署一个租户，则 SAP HANA 快照可以正常工作，并可以使用此方法。

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 Azure 上的 SAP HANA（大型实例）的存储快照

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 当触发对 /hana/data、和 /hana/shared（包括 /usr/sap）卷的快照时，快照技术将在执行存储快照前启动 SAP HANA 快照。 在恢复存储快照后，此 SAP HANA 快照是最终日志还原的设置点。 为使 HANA 快照成功，需要活跃的 HANA 实例。  在 HSR 方案中，不能执行 HANA 快照的当前辅助节点不支持存储快照。
- 成功执行存储快照后，将删除 SAP HANA 快照。
- 事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来创建快照。 在这种情况下，不需要执行 HANA 快照。
- 如果您必须将数据库还原到的某一点时间，请求的 Microsoft Azure 支持 （会造成生产中断） 或 SAP HANA Azure 还原到特定的存储快照。 一个例子就是按计划将沙盒系统还原到其原始状态。
- 存储快照中包含的 SAP HANA 快照是创建存储快照后，应用执行并存储的事务日志备份偏移点。
- 创建这些事务日志备份是为了将数据库还原到某个时间点。

可针对三种类型的卷执行存储快照：

- 基于 /hana/data 和 /hana/shared（包括 /usr/sap）创建组合快照。 此快照需要创建 SAP HANA 快照，为存储快照进行准备工作。 从存储点的角度来看，SAP HANA 快照可确保数据库处于一致状态。 这对于还原过程来说就是在一个点上进行设置。
- 基于 /hana/logbackups 创建单独的快照。
- 操作系统分区。

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 获取最新的快照脚本和文档。 从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 下载快照脚本包时，还可以获得脚本的 PDF 文档作为脚本包的一部分。 每个脚本包都有其自己的 PDF 文档。

## <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照使用分配到 HANA 大型实例单元的存储空间。 需要从计划存储快照和保存的存储快照数量角度考虑以下方面。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 特定的存储快照（在最初创建时）几乎不消耗存储空间。
- 随着数据内容以及存储卷上 SAP HANA 数据文件内容的不断更改，快照需要存储原始块内容以及数据更改。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 执行这些卷的快照会消耗卷空间。  需要确定何时计划存储快照。 此外，还需要监视存储卷的空间消耗，并管理存储的快照数。 将大量数据导入 HANA 数据库或对其执行其他重大更改时，可以禁用存储快照。 


以下部分提供了有关执行这些快照的信息，提供了一般性的建议：

- 尽管硬件可以保留每个卷的 255 个快照，但可以保留的快照数远低于此数字。 建议设为 250 个或更少。
- 执行存储快照之前，请监视并跟踪可用空间。
- 根据可用空间减少存储快照数量。 可以减少保留的快照数量，或者扩展卷。 可以 1 TB 为单位订购附加存储。
- 在使用 SAP 平台迁移工具 (R3load) 将数据移入 SAP HANA，或者从备份还原 SAP HANA 数据库或执行其他活动期间，可以禁用 /hana/data 卷上的存储快照。 
- 在对 SAP HANA 表进行重大重组期间，应尽可能地避免执行存储快照。
- 存储快照是利用 Azure 上的 SAP HANA（大型实例）灾难恢复功能的先决条件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服务存储快照的先决条件

为确保快照脚本成功执行，请务必在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。 Perl 已预安装在 HANA 大型实例单元中。 若要检查 Perl 版本，请使用以下命令：

`perl -v`

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>设置存储快照

若要设置 HANA 大型实例的存储快照，请遵循以下步骤：
1. 确保在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。
1. 修改 /etc/ssh/ssh\_config，添加代码行 _MACs hmac-sha1_。
1. 在运行的每个 SAP HANA 实例的主节点上创建一个 SAP HANA 备份用户帐户（如果适用）。
1. 在所有 SAP HANA 大型实例服务器上安装 SAP HANA HDB 客户端。
1. 在每个区域的第一台 SAP HANA 大型实例服务器上，创建一个公钥用于访问对快照创建操作进行控制的底层存储基础结构。
1. 将脚本和配置文件从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 复制到 SAP HANA 安装中的 **hdbsql** 位置。
1. 必要时，根据相应的客户规范修改 *HANABackupDetails.txt* 文件。

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 获取最新的快照脚本和文档。 从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 下载快照脚本包时，还可以获得脚本的 PDF 文档作为脚本包的一部分。 每个脚本包都有其自己的 PDF 文档。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 方案的注意事项
如果对一个 HANA 大型实例单元上的多个 SAP HANA 实例运行 [MCOD 场景](https://launchpad.support.sap.com/#/notes/1681092)，将获得为每个 SAP HANA 实例预配的单独存储卷。 在当前版本的自助服务快照自动化中，无法针对每个 HANA 实例系统 ID (SID) 启动单独的快照。 该功能在配置文件（参阅下文）中为服务器的注册 SAP HANA 实例提供检查，并对单元上注册的所有实例的卷执行同时快照。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步骤 1：安装 SAP HANA HDB 客户端

Azure 上的 SAP HANA（大型实例）上安装的 Linux 操作系统包含所需的文件夹和脚本用于针对备份和灾难恢复目的执行 SAP HANA 存储快照。 在 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 中查找最新版本。 最新发布的脚本版本为 3.x。 相同的主要版本中，不同的脚本可能具有不同的次要发行版本。

>[!IMPORTANT]
>从脚本版本 2.1 过渡到 3.x 时，请注意，配置文件的结构和脚本的某些语法发生了变化。 请查看特定部分中的标注。 

客户需要负责在安装 SAP HANA 时在 HANA 大型实例单元上安装 SAP HANA HDB 客户端。

### <a name="step-2-change-the-etcsshsshconfig"></a>步骤 2：更改 /etc/ssh/ssh\_config

按如下所示更改 `/etc/ssh/ssh_config`，添加 _MACs hmac-sha1_ 代码行：
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>步骤 3：创建公钥

为能够访问 HANA 大型实例租户的存储快照接口，需要通过公钥建立登录过程。 在租户中的第一台 Azure 上的 SAP HANA（大型实例）服务器上，创建一个公钥用于访问存储基础结构。 公钥可以确保无需使用密码登录到存储快照接口。 创建公钥也意味着不需要保留密码凭据。 在 SAP HANA 大型实例服务器上的 Linux 中，执行以下命令生成公钥：
```
  ssh-keygen -t rsa –b 5120 -C ""
```

新位置是 **_/root/.ssh/id\_rsa.pub**。 请不要输入实际密码，否则，每次登录时都需要输入密码。 应该按 **Enter** 两次，从而避免登录时出现“输入密码”请求。

确保公钥按预期方式正常工作，方法是将文件夹切换到 **/root/.ssh/**，然后执行 `ls` 命令。 如果密钥存在，请运行以下命令复制公钥：

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

此时，请联系 Azure 上的 SAP HANA，并向他们提供的公钥。 服务代表会使用该公钥将 SAP HANA 注册到为 HANA 大型实例租户划分的底层存储基础结构中。

### <a name="step-4-create-an-sap-hana-user-account"></a>步骤 4：创建 SAP HANA 用户帐户

为了启动 SAP HANA 快照的创建，需要在 SAP HANA 中创建一个可供存储快照脚本使用的用户帐户。 在 SAP HANA Studio 中创建用于此目的的 SAP HANA 用户帐户。 必须在 SYSTEMDB 下创建用户，而不是在适用于 MDC 的 SID 数据库下创建。 在单容器环境中，在租户数据库下设置用户。 此帐户必须拥有以下权限：“备份管理”和“目录读取”。 在本示例中，用户名为 **SCADMIN**。 在 HANA Studio 中创建的用户帐户名区分大小写。 出现是否要求用户下次登录时更改密码的提示时，请务必选择“否”。

![在 HANA Studio 中创建用户](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

如果在一个单元上有多个 SAP HANA 实例时采用 MCOD 部署，则需要对每个 SAP HANA 实例重复此步骤。

### <a name="step-5-authorize-the-sap-hana-user-account"></a>步骤 5：为 SAP HANA 用户帐户授权

在此步骤中，向创建的 SAP HANA 用户帐户授权，以便脚本无需在运行时提交密码。 使用 SAP HANA 命令 `hdbuserstore` 可以创建存储在一个或多个 SAP HANA 节点上的 SAP HANA 用户密钥。 用户可以使用用户密钥从脚本流程内部访问 SAP HANA，而无需管理密码。 本文稍后会介绍脚本过程。

>[!IMPORTANT]
>在计划执行脚本的用户下运行以下命令。 否则脚本无法正常工作。

按如下所示输入 `hdbuserstore` 命令：

**对于非 MDC HANA 设置**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**对于 MDC HANA 设置**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

在以下示例中，用户为 **SCADMIN01**，主机名为 **lhanad01**，实例编号为 **01**：
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
如果在一个单元上有多个 SAP HANA 实例时使用 HANA MCOD 部署，则需要对单元上的每个 SAP HANA 实例和相关联的备份用户重复此步骤。

如果具有 SAP HANA 横向扩展配置，则需要通过单个服务器管理所有脚本。 在本示例中，必须在每台主机上更改 **SCADMIN01** 的 SAP HANA 密钥，显示与该密钥相关的主机。 使用 HANA DB 的实例编号修正 SAP HANA 备份帐户。 该密钥必须在其分配到的主机上拥有管理权限，横向扩展配置的备份用户必须有权访问所有 SAP HANA 实例。 假设三个横向扩展节点的名称分别为 **lhanad01**、**lhanad02** 和 **lhanad03**，命令序列如下所示：

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步骤 6：获取快照脚本、配置快照及测试配置和连接

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 下载最新版本的脚本。 将下载的脚本和文本文件复制到 hdbsql 的工作目录。 对于当前 HANA 安装，此目录采用以下格式：/hana/shared/D01/exe/linuxx86\_64/hdb。 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_test_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

处理 Perl 脚本时： 

- 除非收到 Microsoft Operations 指示，否则不要修改脚本。
- 要求修改脚本或参数文件时，始终使用 Linux 文本编辑器（例如“vi”），而不使用 Windows 编辑器（例如记事本）。 使用 Windows 编辑器可能会损坏文件格式。
- 始终使用最新的脚本。 可从 GitHub 下载最新版本。
- 在整个环境中使用相同版本的脚本。
- 测试脚本，并在生产系统中直接使用之前，熟悉所需参数和脚本的输出。
- 不要更改由 Microsoft Operations 预配的服务器的装载点名称。 这些脚本依赖于这些标准的装载点才能成功执行。


不同脚本和文件的用途如下所述：

- **azure\_hana\_backup.pl**：使用 Linux Cron Scheduling 实用工具计划此脚本，以便在 HANA 数据和共享卷、/hana/logbackups 卷或操作系统上执行存储快照。
- **azure\_hana\_replication\_status.pl**：此脚本提供有关生产站点到灾难恢复站点的复制状态的基本详细信息。 此脚本用于监视以确保复制正在进行，并显示复制项的大小。 如果复制时间过长或链路关闭，此脚本还会提供指导。
- **azure\_hana\_snapshot\_details.pl**：此脚本提供有关存在于环境中每个卷的所有快照的基本详细信息列表。 此脚本可在主服务器或灾难恢复位置中的服务器单元上运行。 此脚本根据每个包含快照的卷提供以下细分信息：
   * 一个卷中全部快照的大小
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）
- **azure\_hana\_snapshot\_delete.pl**：此脚本删除一个存储快照或一组快照。 可以使用 HANA Studio 中的 SAP HANA 备份 ID，或使用存储快照名称。 目前，该备份 ID 仅绑定到为 HANA data/log/shared 卷创建的快照。 否则，如果输入快照 ID，会查找所有与输入的快照 ID 匹配的快照。  
- **testHANAConnection.pl**：此脚本测试与 SAP HANA 实例的连接，设置存储快照时需要使用此脚本。
- ：**testStorageSnapshotConnection.pl**：此脚本有两个用途。 首先，它确保运行脚本的 HANA 大型实例单元有权访问已分配的存储虚拟机，并有权访问 HANA 大型实例的存储快照接口。 第二个用途是为要测试的 HANA 实例创建临时快照。 应为服务器上的每个 HANA 实例运行此脚本，从而确保备份脚本按预期正常运行。
- **removeTestStorageSnapshot.pl**：此脚本删除通过 testStorageSnapshotConnection.pl 脚本创建的测试快照。
- **azure\_hana\_dr\_failover.pl**：此脚本将 DR 故障转移加入到另一个区域。 该脚本需要在 DR 区域中的 HANA 大型实例单元中执行，或者在希望故障转移到的单元上执行。 此脚本可停止从主端到辅助端的存储复制，还原 DR 卷上最新的快照，并为 DR 卷提供装载点
- **azure\_hana\_test\_dr\_failover.pl**：此脚本将测试故障转移执行到 DR 站点。 与 azure_hana_dr_failover.pl 脚本不同，此执行不会中断从主端到辅助端的存储复制。 而会在 DR 端创建复制的存储卷的克隆，并提供克隆卷的装载点。 
- **HANABackupCustomerDetails.txt**：此文件是可修改的配置文件，需要将其进行修改以适应 SAP HANA 配置。 *HANABackupCustomerDetails.txt* 文件是脚本的控制与配置文件，用于运行存储快照。 根据用途和设置调整该文件。 你收到**存储备份名称**并**存储 IP 地址**部署实例后的 Azure 上的 SAP HANA。 不能修改此文件中任何变量的顺序、排序或空格。 否则脚本无法正常运行。 此外，您收到纵向扩展节点或主节点的 IP 的地址 （如果是横向扩展） 从在 Azure 上的 SAP HANA。 还会知道在安装 SAP HANA 期间获取的 HANA 实例编号。 现在，需要将备份名称添加到配置文件。

对于纵向扩展或横向扩展部署，在填写 HANA 大型实例单元的服务器名称和服务器的 IP 地址后，配置文件如以下示例所示。 填写想要备份或恢复的每个 SAP HANA SID 的所有必填字段。

还可以标注出一段时间内不希望备份的实例行，方法是在所需的字段前添加“#”。 如果不需要备份或恢复特定实例，则无需输入服务器上包含的所有 SAP HANA 实例。 必须保留所有字段的格式，否则所有脚本都会引发错误消息，并且脚本会终止。 最后一个 SAP HANA 实例投入使用后，可以删除未使用的任何 SID 信息详情的其他所需行。 必须填写、标注或删除所有行。

>[!IMPORTANT]
>从版本 2.1 到版本 3.x，文件的结构有所更改。 如果想使用 3.x 版本的脚本，则需要调整配置文件结构。 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

对于在 HANA 大型实例单元上配置的每个实例或横向扩展配置，需按以下方式定义数据：

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
对于横向扩展和 HANA 系统复制配置，对每一个节点重复此配置。 此度量值可确保在失败的情况下，备份和最终存储复制可以继续工作。   

将所有配置数据填入 *HANABackupCustomerDetails.txt* 文件后，需要检查 HANA 实例数据的相关配置是否正确。 使用脚本独立于 SAP HANA 纵向扩展或横向扩展配置的脚本 `testHANAConnection.pl`。

```
testHANAConnection.pl
```

如果具有 SAP HANA 横向扩展配置，请确保 HANA 主实例有权访问全部所需的 HANA 服务器和实例。 测试脚本中不带参数，但必须将数据添加到 *HANABackupCustomerDetails.txt* 配置文件的才能使脚本正常运行。 只会返回 shell 命令错误代码，因此脚本无法检查每个特定实例中的错误。 但尽管如此，脚本确实会提供一些有用的注释来帮助用户做仔细检查。

若要运行该脚本，请输入以下命令：
```
 ./testHANAConnection.pl
```
如果脚本成功获取 HANA 实例的状态，会显示 HANA 连接成功的消息。


下一测试步骤是基于放入 *HANABackupCustomerDetails.txt* 配置文件的数据检查与存储的连接，并执行测试快照。 在执行 `azure_hana_backup.pl` 脚本之前，必须先运行此测试。 如果某个卷不包含任何快照，就无法确定该卷是否为空，或者在获取快照详细信息时是否发生了 SSH 失败。 因此，脚本将执行两个步骤：

- 它验证要执行快照的脚本有权访问租户的存储虚拟机和接口。
- 根据 HANA 实例为每个卷创建一个测试或虚构快照。

因此，HANA 实例包含为参数。 如果执行失败，无法提供与存储连接相关的错误检查。 即使不执行错误检查，该脚本也能提供有用的提示。

1. 执行命令序列以开展此测试：

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   移交 HANA 大型实例单元时，已提供存储用户名和存储 IP 地址。

1. 运行测试脚本：
   ```
    ./testStorageSnapshotConnection.pl
   ```

脚本会尝试使用之前设置步骤中提供的公钥和在 *HANABackupCustomerDetails.txt* 文件中配置的数据登录到存储。 如果登录成功，会显示以下内容：

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

如果连接到存储控制台时出现问题，输出如下所示：

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

成功登录到存储虚拟机接口后，脚本会继续执行阶段 2 并创建测试快照。 此处显示的输出适用于 SAP HANA 的三节点横向扩展配置：

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

如果已使用脚本成功执行测试快照，则可以继续配置实际的存储快照。 如果不成功，请先调查问题，然后继续。 测试快照应保留至第一批实际快照完成为止。


### <a name="step-7-perform-snapshots"></a>步骤 7：执行快照

在完成所有准备步骤后，可以开始配置实际存储快照的配置。 要计划的脚本会使用 SAP HANA 纵向扩展和横向扩展配置。 备份脚本的定期和常规执行应使用 cron 实用工具来计划。 

可以创建实现三种类型的快照备份：
- **HANA**：组合快照备份，其中包含 /hana/data 和 /hana/shared（包含 /usr/sap）的卷的快照通过协调式快照实现。 可从此快照还原单个文件。
- **日志**：/hana/logbackups 卷的快照备份。 执行此存储快照时不触发任何 HANA 快照。 此存储卷是要包含 SAP HANA 事务日志备份。 这些备份的执行频率更高，以限制日志增长并防止潜在的数据丢失。 可从此快照还原单个文件。 频率不应小于 3 分钟。
- **Boot**：包含 HANA 大型实例启动逻辑单元号 (LUN) 的卷的快照。 此快照备份仅适用于 HANA 大型实例 I 类型 SKU。 无法从包含启动 LUN 的卷的快照执行单个文件还原。


>[!NOTE]
> 随着 3.x 版本脚本（支持 MCOD 部署）的到来，这三种类型快照的调用语法发生了变化。 没有必要再指定实例的 HANA SID。 需确保在配置文件 *HANABackupCustomerDetails.txt* 中配置了一个单元的 SAP HANA 实例。

>[!NOTE]
> 首次执行脚本时，在多 SID 环境中，脚本可能会显示某些意外错误。 只需重新运行脚本即可解决问题。



使用脚本 *azure_hana_backup.pl* 执行存储快照的新调用语法如下：

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

参数详细信息如下： 

- 第一个参数确定相应快照备份类型的特征。 允许的值为 **hana**、**logs** 和 **boot**。 
- 将参数 **\<HANA 大型实例类型 >** 是所必需的启动卷备份。 有两个有效的值“TypeI”或“TypeII”，具体取决于 HANA 大型实例单元。 若要了解单元的类型，请参阅 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。  
- 将参数**\<快照前缀 >** 是快照发布或快照的类型的备份标签。 它有两个用途：第一个用途是指定快照的名称，以便知道这些快照的作用。 第二个用途是让脚本 *azure\_hana\_backup.pl* 确定该特定标签下保留的存储快照数。 如果计划两个具有相同类型（例如 **hana**）、不同标签的存储快照备份，并定义要对每个备份保留 30 个快照，则最终会有 60 个受影响的存储卷快照。 仅允许字母数字（“A-Z、a-z、0-9”）、下划线（“_”）和破折号（“-”）字符。 
- 将参数 **\<snapshot_frequency >** 是保留供未来发展和不产生任何影响。 执行 **log** 类型的备份时，请将其设置为“3 分钟”，并在执行其他备份类型时将其设为“15 分钟”。
- 将参数**\<的保留的快照数 >** 通过定义拥有相同快照前缀 （标签） 的快照数间接定义快照的保留期。 对于通过 cron 制定的执行计划，此参数十分重要。 如果拥有相同快照前缀的快照数将超出此参数提供的编号，在执行新的存储快照之前，将删除时间最早的快照。

对于横向扩展，该脚本会执行附加检查，确保可以访问所有 HANA 服务器。 该脚本还会在创建 SAP HANA 快照之前，检查所有 HANA 实例是否返回实例的相应状态。 执行 SAP HANA 快照之后，执行存储快照。

执行脚本 `azure_hana_backup.pl` 会在以下三个阶段创建存储快照：

1. 执行 SAP HANA 快照
1. 执行存储快照
1. 删除已创建的 SAP HANA 快照，然后再执行存储快照

若要执行该脚本，请从脚本复制到的 HDB 可执行文件文件夹调用该脚本。 

保留期根据执行脚本时作为参数提交的快照数管理。 存储快照消耗的时间量由以下因素决定：执行时间长短，以及执行脚本时作为参数提供的快照数。 如果保留的快照数超过了脚本调用中指定为参数的数字，则执行新快照之前，将删除同一标签中时间最早的存储快照。 作为最后一个调用参数指定的数字是可用于控制保留的快照数的数字。 通过该数字还可间接控制用于快照的磁盘空间。 

> [!NOTE]
>更改标签后，将立即重新开始计数。 需要严格处理标签，以免意外删除快照。

## <a name="snapshot-strategies"></a>快照策略
不同类型快照的频率取决于是否使用 HANA 大型实例灾难恢复功能。 此功能依赖于存储快照，这可能需要实施某些与存储快照频率和执行时间长短相关的特殊建议。 

以下注意事项和建议假设不使用 HANA 大型实例提供的灾难恢复功能。 假设用户使用存储快照来获取备份并提供过去 30 天的时点恢复。 鉴于快照数和空间的限制，客户考虑了以下要求：

- 时间点恢复的恢复时间。
- 使用的空间。
- 潜在灾难恢复的恢复点和恢复时间目标。
- 针对磁盘最终执行的 HANA 完整数据库备份。 每当针对磁盘执行完整数据库备份或执行 **backint** 接口时，存储快照的执行会失败。 如果打算基于存储快照执行完整数据库备份，请确保在此期间已禁用存储快照执行。
- 每个卷的快照数（限制为 250）。


对于不使用 HANA 大型实例灾难恢复功能的客户，快照频率会低一些。 在这类情况下，客户以 12 小时或 24 小时的周期对 /hana/data 和 /hana/shared（包括 /usr/sap）执行组合快照，并将这些快照保留一个月。 对日志备份卷的快照也执行相同操作。 但是，日志备份卷的 SAP HANA 事务日志备份按 5 - 15 分钟的周期执行。

最好是使用 cron 执行计划的存储快照。 使用同一个脚本来满足所有备份和灾难恢复的需求，并修改脚本输入，使之与请求的不同备份时间匹配。 可以根据这些快照的执行时间，在 cron 中做好上述一切计划：每小时、12 小时、每天或每周。 

下面是 /etc/crontab 中的 cron 计划的示例：
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
22 12 * * *  ./azure_hana_backup.pl logs dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
上述示例中有一个每小时执行一次的组合快照，该快照的实施对象包括含 /hana/data 和 /hana/shared（包括 /usr/sap）位置的卷。 使用此类型的快照快速实现过去两天内的时点恢复。 此外，将对这些卷执行每日快照。 因此，会执行两天的每时快照和四周的每日快照。 此外，事务日志备份卷每天备份一次。 这些备份将保留四周。 从 crontab 第三行可知，计划每 5 分钟执行一次 HANA 事务日志备份。 执行存储快照的不同 cron 作业的启动时间是错开的，因此不会在某个时间点同时执行所有这些快照。 

在以下示例中，将每小时执行一次组合快照，该快照涵盖包含 /hana/data 和 /hana/shared（包括 /usr/sap）位置的卷。 这些快照会保留两天。 事务日志备份卷的快照每 5 分钟执行一次，并保留 4 个小时。 与之前一样，HANA 事务日志文件的备份计划为每 5 分钟执行一次。 启动事务日志备份后，执行事务日志备份卷的快照时会存在 2 分钟的延迟。 一般情况下，SAP HANA 事务日志备份应在这 2 分钟内完成。 与之前一样，包含启动 LUN 的卷通过存储快照每天备份一次，且会保留四周。

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl logs logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

下图演示了上述示例的序列（不包括启动 LUN）：

![备份和快照之间的关系](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 对 /hana/log 卷执行常规写入，将提交的更改记录到数据库。 SAP HANA 将保存点定期写入 /hana/data 卷。 根据 crontab 中所指定，SAP HANA 事务日志备份每 5 分钟执行一次。 由于对 /hana/data 和 /hana/shared 卷触发组合存储快照，因此还会每小时执行一次 SAP HANA 快照。 成功执行 HANA 快照后，会执行组合存储快照。 如 crontab 中所指示，在 HANA 事务日志备份后 2 分钟左右，/hana/logbackup 卷上的存储快照每 5 分钟执行一次。

> 

>[!IMPORTANT]
> 仅当与 SAP HANA 事务日志备份配合执行存储快照时，才能针对 SAP HANA 备份使用存储快照。 这些事务日志备份需要能够涵盖执行存储快照的间隔时间段。 

如果向用户承诺时间点恢复目标为 30 天，则需要：

- 在极端情况下，访问针对 /hana/data 和 /hana/shared 且保留期限已达 30 天的组合存储快照。
- 创建保留时长长于任何两个组合存储快照间隔时间的连续事务日志备份。 因此事务日志备份卷快照的最长保留时长需为 30 天。 如果将事务日志备份复制到位于 Azure 存储的另一个 NFS 共享，则不存在这种情况。 在这种情况下，可以从该 NFS 共享拉取旧事务日志备份。

若要从存储快照和事务日志备份的最终存储复制中获益，需要更改 SAP HANA 写入事务日志备份的位置。 可在 HANA Studio 中进行此更改。 虽然 SAP HANA 会自动备份完整日志段，但仍应指定确定的日志备份间隔时间。 使用灾难恢复选项时尤其如此，因为通常需要以确定性的周期执行日志备份。 在以下案例中，日志备份间隔设为 15 分钟。

![在 SAP HANA Studio 中计划 SAP HANA 备份日志](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

还可以选择频率高于 15 分钟的备份。 较高的频率设置通常与 HANA 大型实例的灾难恢复功能结合使用。 某些客户每 5 分钟执行一次事务日志备份。  

如果从未备份过数据库，则最后一步是执行基于文件的数据库备份，以创建必须在备份目录中存在的单个备份项。 否则，SAP HANA 无法启动指定的日志备份。

![创建基于文件的备份以创建单个备份项](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


成功执行第一个存储快照后，可删除在步骤 6 中执行的测试快照。 为此，请运行脚本 `removeTestStorageSnapshot.pl`：
```
./removeTestStorageSnapshot.pl
```

下面是脚本输出的示例：
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>监视磁盘卷上的快照数量和大小

在特定的存储卷上，可以监视快照的数量以及这些快照的存储消耗量。 `ls` 命令不会显示快照目录或文件。 但是，Linux OS 命令 `du` 显示有关这些存储快照的详细信息（因为这些快照存储在相同的卷上）。 此命令适用以下选项：

- `du –sh .snapshot`：此选项提供快照目录中所有快照的总数。
- `du –sh --max-depth=1`：此选项列出 .snapshot 文件夹中保存的所有快照，以及每个快照的大小。
- `du –hc`：此选项提供所有快照占用的总大小。

使用以下命令可确保创建和存储的快照不会耗尽卷上的所有存储。

>[!NOTE]
>使用上述命令时，不显示启动 LUN 的快照。

### <a name="getting-details-of-snapshots"></a>获取有关快照的详细信息
若要获取有关快照的更多详细信息，也可以使用脚本 `azure_hana_snapshot_details.pl`。 如果灾难恢复位置处存在活动服务器，则可以在任一位置运行此脚本。 此脚本根据每个包含快照的卷提供以下细分输出： 
   * 一个卷中全部快照的大小
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）

下面是脚本执行语法的示例：

```
./azure_hana_snapshot_details.pl 
```

由于脚本会尝试检索 HANA 备份 ID，因此需要连接到 SAP HANA 实例。 此连接需要正确设置 *HANABackupCustomerDetails.txt* 配置文件。 卷中两个快照的输出如下所示：

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>减少服务器上的快照数

如前所述，可以减少存储的带有特定标签的快照数。 用于启动快照的命令的最后两个参数是要保留的快照的标签与数量。

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

在上面的示例中，快照标签为“dailyhana”，要保留的带有此标签的快照数为 28。 对磁盘空间消耗量做出响应时，可能想要减少存储的快照数。 例如，如果希望将快照数减少至 15，实现此操作的简单方法是将脚本的最后一个参数设置为 **15** 并运行该脚本：

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

如果使用此设置运行脚本，快照数（包括新存储快照）为 15。 会保存最新的 15 个快照，同时删除 15 个较旧的快照。

 >[!NOTE]
 > 仅当存在超过 1 小时的快照时，此脚本才能减少快照数。 该脚本不会删除不超过 1 小时的快照。 这些限制与提供的可选灾难恢复功能相关。

如果不再想要保留一组包含备份标签（如语法示例中的 **hanadaily**）的快照，可以使用 **0** 作为保留数量来执行该脚本。 然后删除所有与该标签匹配的快照。 但是，删除所有快照可能会影响 HANA 大型实例灾难恢复的功能。

第二种删除特定快照的方法是使用脚本 `azure_hana_snapshot_delete.pl`。 此脚本旨在使用在 HANA Studio 中找到的 HANA 备份 ID 或通过快照名称本身删除快照或一组快照。 目前，备份 ID 仅与为 **hana** 快照类型创建的快照绑定。 **logs** 和 **boot** 类型的快照备份不执行 SAP HANA 快照，因此，这些快照没有可供查找的备份 ID。 如果输入快照名称，将在不同卷上查找所有与输入的快照名称匹配的快照。 

要调用该脚本，需使用脚本的调用语法指定 HANA 实例的 SID：

```
./azure_hana_snapshot_delete.pl <SID>

```

需要以 **root** 用户身份执行脚本。

如果选中一个快照，可单独删除每个快照。 首先提供包含快照的卷，然后提供快照名称。 如果快照存在于该卷且存在时间超过 1 小时，则会删除该快照。 可以通过执行 `azure_hana_snapshot_details` 脚本查找卷名和快照名称。 

>[!IMPORTANT]
>如果有数据仅存在于要删除的快照上，则删除快照后，会永久丢失数据。

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>从存储快照实现文件级还原
对于 **hana** 和 **logs** 快照类型，可以直接在 **.snapshot** 目录中的卷上访问快照。 每个快照的有一个子目录。 可将执行了快照的每个文件以文件在快照瞬间的状态从该子目录复制到实际目录结构。 在当前的脚本版本中，未以自助服务的形式向快照还原提供还原脚本（虽然故障转移期间可在 DR 站点在自助服务 DR 脚本中执行快照还原）。 必须通过打开服务请求联系 Microsoft 操作团队，要求从现有可用快照还原所需快照。

>[!NOTE]
>单个文件还原不适用于独立于 HANA 大型实例单元类型的启动 LUN 的快照。 **.snapshot** 目录不在启动 LUN 中公开。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>恢复到最近的 HANA 快照

如果遇到生产中断的情况，可向 Microsoft Azure 支持发起客户事件，启动从存储快照恢复的过程。 这是由于在生产系统中删除了数据造成的高度紧急问题，这时，检索数据的唯一办法就是还原生产数据库。

在不同的情况下，时间点恢复可能不太紧急，因此可以提前几天规划。 可以在 Azure 上规划与 SAP HANA 此恢复，而不要提出高优先级标志。 例如，计划通过应用新的增强包升级 SAP 软件。 然后又需要还原到升级增强包之前的快照状态。

发送请求之前，需要做好准备。 然后，Azure 团队上的 SAP HANA 可以处理该请求，并提供已还原的卷。 接下来，需要基于快照还原 HANA 数据库。 

下面说明如何做好请求准备：

>[!NOTE]
>根据所用的 SAP HANA 版本，用户界面可能与下面所示的屏幕截图不同。

1. 确定要还原的快照。 除非另有说明，否则只能还原 hana/data 卷。 

1. 关闭 HANA 实例。

   ![关闭 HANA 实例](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 卸载每个 HANA 数据库节点上的数据卷。 如果仍将数据卷载入操作系统，快照还原会失败。
   ![卸载每个 HANA 数据库节点上的数据卷](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 提出 Azure 支持请求，并包含有关如何还原特定快照的说明。

   - 还原期间：在 Azure 上的 SAP HANA 可能会要求用户参加电话会议以协调、 验证和确认还原正确的存储快照。 

   - 还原后：Azure 服务上的 SAP HANA 会通知你已还原存储快照。

1. 完成还原过程后，请重新装载所有数据卷。

   ![重新装载所有数据卷](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

1. 在 SAP HANA Studio 中选择恢复选项（如果在通过 SAP HANA Studio 重新连接到 HANA DB 时未自动显示）。 以下示例演示如何还原到最后一个 HANA 快照。 存储快照中嵌入了一个 HANA 快照。 如果要还原到最近的存储快照，该快照应是最近的 HANA 快照。 （如果要还原到较旧的存储快照，需要根据存储快照的创建时间找到 HANA 快照。）

   ![在 SAP HANA Studio 中选择恢复选项](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

1. 选择“将数据库恢复到特定的数据备份或存储快照”。

   ![“指定恢复类型”窗口](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

1. 选择“指定不包含目录的备份”。

   ![“指定备份位置”窗口](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

1. 在“目标类型”列表中，选择“快照”。

   ![“指定要恢复的备份”窗口](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

1. 选择“完成”启动恢复过程。

    ![选择“完成”启动恢复过程](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

1. HANA 数据库已还原并恢复到存储快照包含的 HANA 快照。

    ![HANA 数据库已还原并恢复到 HANA 快照](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>恢复到最新状态

以下过程将还原存储快照中包含的 HANA 快照。 然后，将事务日志备份还原到还原存储快照之前的最新数据库状态。

>[!IMPORTANT]
>在继续下一步之前，请确保具备完整且连续的事务日志备份链。 如果没有这些备份，则无法还原数据库的当前状态。

1. 完成“恢复到最近的 HANA 快照”中的步骤 1-6。

1. 选择“将数据库恢复到其最近状态”。

   ![选择“将数据库恢复到其最近状态”](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

1. 指定最新 HANA 日志备份的位置。 该位置需要包含从创建 HANA 快照开始，到处于最新状态为止的所有 HANA 事务日志备份。

   ![指定最新 HANA 日志备份的位置](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

1. 选择一个备份用作恢复数据库的基础。 在本示例中，屏幕截图中的 HANA 快照为存储快照中包含的 HANA 快照。 

   ![选择一个备份用作恢复数据库的基础](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

1. 如果增量备份在创建 HANA 快照之后、处于最近状态之前不存在，请清除“使用增量备份”复选框。

   ![如果不存在增量备份，则清除“使用增量备份”复选框](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

1. 在摘要屏幕上选择“完成”启动还原过程。

   ![在摘要屏幕上单击“完成”](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>恢复到另一个时间点
若要恢复到 HANA 快照（包含在存储快照中）与晚于 HANA 快照时间点恢复之间的时间点，请执行以下步骤：

1. 确保提供从创建 HANA 快照开始，到要恢复到的时间为止的所有事务日志备份。
1. 开始执行“恢复到最新状态”下的过程。
1. 执行步骤 2 时，请在“指定恢复类型”窗口中选择“将数据库恢复到以下时间点”，指定时间点。 
1. 完成步骤 3-6。

## <a name="monitor-the-execution-of-snapshots"></a>监视快照执行

使用 HANA 大型实例的存储快照时，还需要监视这些快照的执行。 执行存储快照的脚本会将输出写入某个文件，并将其保存在 Perl 脚本所在的同一位置。 会针对每个存储快照单独写入一个文件。 每个文件的输出显示了快照脚本执行的各个阶段：

1. 查找需要创建快照的卷。
1. 查找基于这些卷创建的快照。
1. 根据指定的快照数量删除最终的现有快照。
1. 创建 SAP HANA 快照
1. 基于卷创建存储快照。
1. 删除 SAP HANA 快照。
1. 将最新的快照重命名为 **.0**。

脚本 cab 的最重要部分标识为此部分：
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
从此示例中可以看到脚本如何记录 HANA 快照的创建过程。 在横向扩展部署中，此过程在主节点上启动。 主节点在每个辅助角色节点上启动 SAP HANA 快照的同步创建。 然后创建存储快照。 成功执行存储快照后，HANA 快照会被删除。 HANA 快照的删除从主节点发起。


## <a name="next-steps"></a>后续步骤
- 请参阅[灾难恢复原则和准备](hana-concept-preparation.md)。
