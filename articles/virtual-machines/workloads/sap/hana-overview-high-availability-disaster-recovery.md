---
title: "Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复 | Microsoft 文档"
description: "为 Azure 上的 SAP HANA（大型实例）建立高可用性并规划灾难恢复。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 4356e35609fa7a9727d8ad4f20fd18df01a05e84
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复 

高可用性和灾难恢复是运行任务关键型 Azure 上的 SAP HANA（大型实例）服务器的重要方面。 必须咨询 SAP、系统集成商和/或 Microsoft，以便正确构建和实施适当的高可用性/灾难恢复策略。 此外，必须考虑到与环境相关的恢复点目标和恢复时间目标。

Microsoft 通过 HANA 大型实例支持一些“现成”的 SAP HANA 高可用性方法。 其中包括：

- **存储复制：**存储系统能够将所有数据复制到另一个 Azure 区域中的另一个 HANA 大型实例戳。 SAP HANA 的工作独立于此方法。
- **HANA 系统复制：**将 SAP HANA 中的所有数据复制到独立的 SAP HANA 系统。 通过定期复制数据最大限度地减小恢复时间目标。 SAP HANA 支持异步模式、内存中同步模式和同步模式（建议仅在同一数据中心或者距离不超过 100 公里的范围内使用这些模式）。 在 HANA 大型实例戳记的当前设计中，HANA 系统复制仅可用于实现高可用性。 根据当前的网络设计，如果不具有恢复到另一 Azure 区域时所需的灾难恢复配置的第三方反向代理组件，则无法使用 HANA 系统复制。 
- **主机自动故障转移：**用于替代 HANA 系统复制的 SAP HANA 本地故障恢复解决方案。 当主节点不可用时，以扩展模式配置一个或多个备用 SAP HANA 节点，SAP HANA 会自动故障转移到备用节点。

Azure 上的 SAP HANA（大型实例）同时面向三个不同地缘政治区域（美国、澳大利亚和欧洲）中的两个 Azure 区域提供。 两个托管 HANA 大型实例戳的不同区域与用于复制存储快照的单独专用网络线路连接，以提供灾难恢复方法。 默认情况下不建立复制。 该功能是为订购灾难恢复功能的客户设置。 存储复制取决于 HANA 大型实例存储快照的使用情况。 也无法选择 Azure 区域作为 DR 区域，因为它位于另一个地缘政治区域。 

下表列出了当前支持的高可用性和灾难恢复方法和组合：

| HANA 大型实例支持的方案 | 高可用性选项 | 灾难恢复选项 | 注释 |
| --- | --- | --- | --- |
| 单节点 | 不可用 | 专用 DR 设置<br /> 多用途 DR 设置 | |
| 主机自动故障转移：N+m<br /> 包括 1+1 | 在“备用”充当活动角色的情况下为可能<br /> HANA 控制角色切换 | 专用 DR 设置<br /> 多用途 DR 设置<br /> 使用存储复制实现 DR 同步 | 附加到所有节点 (n+m) 的 HANA 卷集<br /> DR 站点必须拥有相同的节点数 |
| HANA 系统复制 | 在使用“主”/“辅助”设置的情况下为可能<br /> 在故障转移事例中，“辅助”变为主要角色<br /> HANA 系统复制和 OS 控制故障转移 | 专用 DR 设置<br /> 多用途 DR 设置<br /> 使用存储复制实现 DR 同步<br /> 在没有第三方组件的情况下，无法使用 HANA 系统复制实现 DR | 附加到每个节点的单独一组磁盘卷<br /> 仅生产站点中的辅助副本磁盘卷被复制到 DR 位置<br /> DR 站点处所需的一组卷 | 

对于专用 DR 设置，我们会让设置具有一定特征，在这种设置中，DR 站点中的 HANA 大型实例单元不用于运行任何其他工作负荷或非生产系统。 此单元是被动单元，在执行灾难故障转移后要立即采取行动时进行部署。 到目前为止，我们没有一个具有此类配置的客户。

对于多用途 DR 设置，我们会让 DR 站点上的设置具有一定特征，在这种设置中，HANA 大型实例单元会运行一个非生产工作负荷。 在要关闭非生产系统的灾难事例中，会装载复制存储的（其他）卷集并开始生产 HANA 实例。 到目前为止，所有使用 HANA 大型实例灾难恢复功能的客户都使用此配置方案。 


可在以下 SAP 文章中查找有关 SAP HANA 高可用性的详细信息： 

- [SAP HANA 高可用性白皮书](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理指南](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [有关 SAP HANA 系统复制的 SAP 学院视频](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 支持说明 #1999880 - 有关 SAP HANA 系统复制的常见问题](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 支持说明 #2165547 - SAP HANA 系统复制环境中的 SAP HANA 备份和还原](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 支持说明 #1984882 - 使用 SAP HANA 系统复制在最大限度地减少停机时间/不停机的情况下实现硬件更换](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>使用 HANA 大型实例实现灾难恢复的网络注意事项

若要利用 HANA 大型实例的灾难恢复功能，需要规划好与两个不同 Azure 区域之间的网络连接。 为此，需要使用一条 Azure ExpressRoute 线路从本地连接到主要 Azure 区域，使用另一个线路从本地连接到灾难恢复区域。 这种措施可以应对整个 Azure 区域（包括 Microsoft 企业边缘路由器 (MSEE) 位置）出现问题。

还可以采取第二种措施，就是将连接到某个区域中 Azure 上的 SAP HANA（大型实例）的所有 Azure VNet 都连接到 ExpressRoute 线路，此线路将连接其他区域的 HANA 大型实例。 通过此“跨连接”，1 号区域的 Azure VNet 中运行的服务可以连接到 2 号区域中的 HANA 大型实例单元，也可以从后者连接到前者。 这种措施可以避免当 Azure 中断服务时，只有一个 MSEE 位置连接到本地位置。

下图展示了这样一个具有弹性的灾难恢复配置：

![灾难恢复的最佳配置](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-for-using-hana-large-instances-storage-replication-for-disaster-recovery"></a>使用 HANA 大型实例存储复制实现灾难恢复的其他要求

使用 HANA 大型实例实现灾难恢复设置的其他要求包括：

- 订购的 Azure 上的 SAP HANA（大型实例）的 SKU 大小必须与生产 SKU 大小相同，并且应部署在灾难恢复区域中。 到目前为止，在客户部署中，这些实例用于运行非生产 HANA 实例。 我们将这种设置称为多用途 DR 设置。   
- 必须在 DR 站点为想要在灾难恢复站点中恢复的每个 Azure 上的 SAP HANA（大型实例）SKU 订购其他存储。 这种措施可以分配存储卷，而存储卷是从 Azure 生产区域复制到 Azure 灾难恢复区域的存储复制目标。

本文档的最后几章将详细介绍有关灾难恢复的更多信息。
 

## <a name="backup-and-restore"></a>备份和还原

在操作数据库方面，一个要点是确保能够在发生各种灾难事件时保护数据库。 这些事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。

备份数据库并具备还原到任意时间点（例如，在某人删除关键数据之前的时间点）的能力，可以尽可能地还原到发生中断之前所处的状态。

为获得最佳效果，必须执行两种类型的备份：

- 数据库备份 - 完整备份、渐进式备份或差异备份
- 事务日志备份

除了在应用程序级别执行完整数据库备份以外，还可以使用存储快照执行更全面的备份。 但存储快照不会替代事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 但存储快照可以通过快速提供数据库的前滚映像来提升恢复速度。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- 自制 (DIY)。 经过计算确保拥有足够的磁盘空间后，通过使用磁盘备份方法直接对附加在 HANA 大型实例单元的卷或对 Azure VM 中的 NFS 共享设置执行完整的数据库和日志备份操作。 在后一种情况中，客户在 Azure 中设置 Linux VM、将 Azure 存储附加到 VM 并通过该 VM 中配置的 NFS 服务器共享此存储。 如果对直接附加到 HANA 大型实例单元的卷执行备份操作，需要将备份复制到 Azure 存储帐户（在设置用于基于 Azure 存储导出 NFS 共享的 Azure VM 后），或使用 Azure 备份保管库或 Azure 冷存储。 另一个选项是将备份复制到 Azure 存储帐户后，使用第三方数据保护工具来存储备份。 对于出于合规性和审核目的而需要长期存储的数据，也有必要使用 DIY 备份选项。 在所有情况下，备份均复制到通过 VM 和 Azure 存储表示的 NFS 共享中。
- 使用 Azure 上的 SAP HANA（大型实例）底层基础结构提供的备份和还原功能。 此选项满足备份和快速还原的需要。 本部分的余下内容介绍 HANA 大型实例提供的此备份和还原功能。 同时介绍该功能与 HANA 大型实例提供的灾难恢复功能之间的关系。

> [!NOTE]
> HANA 大型实例底层基础结构使用的快照技术依赖于 SAP HANA 快照。 SAP HANA 快照目前无法与 SAP HANA 多租户数据库容器的多个租户配合工作。 因此，在 SAP HANA 多租户数据库容器中部署多个租户时，不能使用此备份方法。 不过，仅部署一个租户时可使用此备份方法。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 Azure 上的 SAP HANA（大型实例）的存储快照

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照的概念。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 当触发对 /hana/data、'hana/log 和 /hana/shared（包括 /usr/sap）卷的快照时，存储快照功能将在执行存储快照前启动 SAP HANA 快照。 在恢复存储快照后，此 SAP HANA 快照是最终日志还原的设置点。
- 成功执行存储快照后，将删除 SAP HANA 快照。
- 事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来执行快照。 在此情况下，不需要执行 HANA 快照。
- 如果必须将数据库还原到某个特定的时间点，可请求 Microsoft Azure 支持（生产中断）或 Azure 上的 SAP HANA 服务管理部门还原到特定的存储快照（例如，按计划将沙盒系统还原到其原始状态）。
- 存储快照中包含的 SAP HANA 快照是创建存储快照后，应用执行并存储的事务日志备份偏移点。
- 创建这些事务日志备份是为了将数据库还原到某个时间点。

作为客户，可针对三种不同类型的卷执行存储快照：

- 将 /hana/data、/hana/log 和 /hana/shared（包括 /usr/sap）的快照组合起来。 此快照需要执行 SAP HANA 快照）
- /hana/logbackups 的单独快照
- OS 分区（仅适用于 HANA 大型实例类型 I）


### <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照使用分配到 HANA 大型实例单元的存储空间。 因此，需要从计划存储快照和保存大量存储快照的角度考虑以下方面。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 特定的存储快照（在最初创建时）几乎不消耗存储空间。
- 随着数据内容以及存储卷上 SAP HANA 数据文件内容的不断更改，快照需要存储原始块内容。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 执行这些卷的快照会消耗卷空间。 因此，用户需负责计划存储快照、监视存储卷空间消耗并管理存储的快照数。 因为是由用户将大量数据导入 HANA 数据库或对其执行其他重大更改，所以只有用户可以决定最终禁用存储快照。 


以下部分提供了有关执行这些快照的信息，提供了一般性的建议：

- 尽管硬件可以保留每个卷的 255 个快照，但我们强烈建议保留的快照数远低于此数字。
- 执行存储快照之前，请监视并跟踪可用空间。
- 根据可用空间减少存储快照数量。 可能需要减少保留的快照数量，或者需要扩展卷。 （可以 1 TB 为单位订购额外的存储。）
- 在使用 SAP 平台迁移工具 (R3load) 将数据移入 SAP HANA，或者从备份还原 SAP HANA 数据库或执行其他活动期间，需要禁用 /hana/data 卷上的存储快照。 
- 在对 SAP HANA 表进行重大重组期间，应尽可能地避免执行存储快照。
- 存储快照是使用 Azure 上的 SAP HANA（大型实例）灾难恢复功能的先决条件。

### <a name="setting-up-storage-snapshots"></a>设置存储快照

设置 HANA 大型实例存储快照的大致步骤如下所示：
1. 确保在 HANA（大型实例）服务器上的 Linux 操作系统中安装 Perl。
2. 修改 /etc/ssh/ssh\_config，添加代码行 _MACs hmac-sha1_。
3. 在运行的每个 SAP HANA 实例的主节点上创建一个 SAP HANA 备份用户帐户（如果适用）。
4. 必须在所有 SAP HANA（大型实例）服务器上安装 SAP HANA HDB 客户端。
5. 在每个区域的第一台 SAP HANA（大型实例）服务器上，必须创建一个公钥用于访问对快照创建操作进行控制的底层存储基础结构。
6. 将脚本和配置文件从[此 github 位置](https://github.com/Azure/hana-large-instances-self-service-scripts)复制到 SAP HANA 安装的 hdbsql 位置。
7. 必要时，根据相应的客户规范修改 HANABackupDetails.txt 文件。

### <a name="step-1-install-sap-hana-hdbclient"></a>步骤 1：安装 SAP HANA HDBClient

Azure 上的 SAP HANA（大型实例）上安装的 Linux 操作系统包含所需的文件夹和脚本用于针对备份和灾难恢复目的执行 SAP HANA 存储快照。 在 [github](https://github.com/Azure/hana-large-instances-self-service-scripts) 中查找最新版本。 最新发布的脚本版本为 2.0。
但是，用户需负责在安装 SAP HANA 时在 HANA 大型实例单元上安装 SAP HANA HDBclient。 （Microsoft 不会安装 HDBclient 或 SAP HANA。）

### <a name="step-2-change-etcsshsshconfig"></a>步骤 2：更改 /etc/ssh/ssh\_config

按如下所示更改 /etc/ssh/ssh\_config，添加 _MACs hmac-sha1_ 代码行。
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

为能够访问 HANA 大型实例租户的存储快照接口，需要通过公钥建立登录。 在租户中的第一台 Azure 上的 SAP HANA（大型实例）服务器上，创建一个公钥用于访问存储基础结构，以便能够创建快照。 公钥可以确保无需使用密码登录到存储快照接口，并且不会保留密码凭据。 在 SAP HANA（大型实例）服务器上的 Linux 中，执行以下命令生成公钥：
```
  ssh-keygen –t dsa –b 1024
```
新位置为 _/root/.ssh/id\_dsa.pub。 请不要输入实际密码，否则，每次登录时都需要输入密码。 应该按“Enter”两次，从而避免登录时出现“输入密码”请求。

确保公钥按预期方式正常工作，方法是将文件夹切换到 /root/.ssh/，然后执行 **ls** 命令。 如果密钥存在，请运行以下命令复制公钥：

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

此时，请联系 Azure 的 SAP HANA 服务管理部门并提供公钥。 服务代表会使用该公钥将 SAP HANA 注册到为 HANA 大型实例租户划分的底层存储基础结构中。

### <a name="step-4-create-an-sap-hana-user-account"></a>步骤 4：创建 SAP HANA 用户帐户

为了能够启动 SAP HANA 快照的创建，需要在 SAP HANA 中创建一个可供存储快照脚本使用的用户。 在 SAP HANA Studio 中创建用于这些用途的 SAP HANA 用户帐户。 此帐户必须拥有以下权限：“备份管理”和“目录读取”。 本示例创建了用户帐户 SCADMIN。 在 HANA Studio 中创建的用户帐户名称需要区分大小写。  当遇到要求用户下一次登录时更改密码的提示时，确保点击“否”。

![在 HANA Studio 中创建用户](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>步骤 5：为 SAP HANA 用户帐户授权

在此步骤中，需要向创建的 SAP HANA 用户帐户授权，以便脚本无需在运行时提交密码。 使用 SAP HANA 命令 `hdbuserstore` 可以创建存储在一个或多个 SAP HANA 节点上的 SAP HANA 用户密钥。 用户可以使用用户密钥从下面所述的脚本流程内部访问 SAP HANA，而无需管理密码。

>[!IMPORTANT]
>以 `root` 身份运行以下命令。 否则脚本无法正常工作。

按如下所示输入 `hdbuserstore` 命令：

![输入 hdbuserstore 命令](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

在以下示例中，用户为 SCADMIN01，主机名为 lhanad01，实例数目为 01，命令为：
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
如果具有 SAP HANA 横向扩展配置，则应通过单个服务器管理所有脚本。 在本示例中，必须在每台主机上更改 SCADMIN01 的 SAP HANA 密钥，反映与该密钥相关的主机。 即，使用 HANA DB 的实例数目修正 SAP HANA 备份帐户。 该密钥必须在其分配到的主机上拥有管理权限，用于横向扩展的备份用户必须有权访问所有 SAP HANA 实例。 假定三个横向扩展节点的名称分别为 lhanad01、lhanad02 和 lhanad03，命令序列如下所示：

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad02:30215 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad03:30315 SCADMIN <password>
```

### <a name="step-6-get-snapshot-scripts-configure-snapshots-and-test-configuration-and-connectivity"></a>步骤 6：获取快照脚本、配置快照及测试配置和连接

请从[此处](https://github.com/Azure/hana-large-instances-self-service-scripts)下载最新版本的脚本。 将下载的脚本和文本文件复制到 hdbsql 的工作目录。 对于当前 HANA 安装，此目录为 /hana/shared/D01/exe/linuxx86\_64/hdb。
```
azure\_hana\_backup.pl
azure\_hana\_replication\_status.pl
azure\_hana\_snapshot\_details.pl
azure\_hana\_snapshot\_delelte.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```

不同脚本和文件的用途如下所示：

- **azure\_hana\_backup.pl**：需要使用 cron 计划此脚本以在 HANA data/log/shared 卷、/hana/logbackups 卷或 OS（针对 HANA 大型实例 I 类型 SKU 启用）上执行存储快照
- **azure\_hana\_replication\_status.pl**：此脚本的作用是提供有关生产站点到灾难恢复站点的复制状态的基本信息。  此脚本用于监视确认复制正在进行并显示复制项的大小。  此脚本还针对复制时间过长或链接可能关闭的情况提供指南。
- **azure\_hana\_snapshot\_details.pl**：此脚本用于向客户提供有关存在于环境中每个卷的所有快照的基本详细信息的列表。 此脚本可在主服务器或灾难恢复位置中的服务器单元上运行。 此脚本提供按每个包含快照的卷进行了细分的以下信息：一个卷中全部快照的大小和该卷中每个快照的详细信息（快照名称、创建时间、快照大小、快照频率以及与该快照关联的 HANA 备份 ID（如果相关）。
- **azure\_hana\_snapshot\_delelte.pl**：此脚本使用 HANA Studio 中找到的 SAP HANA 备份 ID 或根据存储快照名称删除一个或一组存储快照。  目前，该备份 ID 仅绑定到为 HANA data/log/shared 卷创建的快照。  否则，如果输入快照 ID，将查找所有与输入的快照 ID 匹配的快照。  
- **testHANAConnection.pl**：此脚本测试与 SAP HANA 实例的连接，设置存储快照时需要使用此脚本。
- **testStorageSnapshotConnection.pl**：此脚本有两个用途。 首先，确保运行脚本的 HANA 大型实例单元有权访问已分配的存储虚拟机，并有权访问 HANA 大型实例的存储快照接口。 第二个用途是为要测试的 HANA 实例创建临时快照。 应为服务器上的每个 HANA 实例运行此脚本，从而确保备份脚本按预期正常运行。
- **removeTestStorageSnapshot.pl**：此脚本用于删除通过 testStorageSnapshotConnection.pl 脚本创建的测试快照。 
- **HANABackupCustomerDetails.txt**：此脚本是可修改的配置文件，需要将其进行修改以适应 SAP HANA 配置

 
HANABackupCustomerDetails.txt 文件是脚本的控制与配置文件，用于运行存储快照。 需根据用途和设置调整该文件。 在部署实例后，应会收到 Azure 上的 SAP HANA 服务管理部门提供的存储备份名称和存储 IP 地址。 不能修改此文件中任何变量的顺序、排序或空格。 否则脚本无法正常运行。 此外，还会收到 Azure 上的 SAP HANA 服务管理部门提供的纵向扩展节点或主节点（如果是横向扩展）的 IP 地址。 安装 SAP HANA 后，还会获得 HANA 实例编号，然后需要将备份名称添加到配置文件

对于纵向扩展或横向扩展部署，在填写存储备份名称和存储 IP 地址后，将看到如下所示的配置文件。 需要填入配置文件的其他详细数据包括单一节点或主节点 IP 地址、HANA 实例编号以及可选择的备份名称：
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA Backup Name:
```

>[!NOTE]
>实际的 HANA 存储快照脚本中目前仅使用节点 1 的详细信息。 建议测试是否能够与所有 HANA 节点相互访问，以便在主备份节点必须经常变动的情况下，可以通过修改节点 1 中的详细信息，确保其他任何节点可以取代主节点。

将所有配置数据填入 HANABackupCustomerDetails.txt 文件后，需要使用 testHANAConnection.pl 脚本检查 HANA 实例数据的相关配置是否正确。 此脚本独立于 SAP HANA 纵向扩展或横向扩展配置

```
testHANAConnection.pl
```

如果具有 SAP HANA 横向扩展配置，请确保 HANA 主实例有权访问全部所需的 HANA 服务器和实例。 测试脚本中不带参数，但必须将数据添加到 HANABackupCustomerDetails.txt 配置文件的才能使脚本正常运行。 由于只会返回 shell 命令错误代码，因此脚本无法检查每个特定实例中的错误。 但尽管如此，脚本确实会提供一些有用的注释来帮助用户做仔细检查。

若要运行该脚本，请执行以下操作：
```
 ./testHANAConnection.pl
```
如果脚本成功获取 HANA 实例的状态，会显示 HANA 连接成功的消息。


下一测试步骤是基于放入 HANABackupCustomerDetails.txt 配置文件的数据检查与存储的连接，并执行测试快照。  在执行 azure\_hana\_backup.pl 脚本之前，必须先执行此测试。 如果某个卷不包含任何快照，就无法确定该卷是否为空，或者在获取快照详细信息时会发生 ssh 失败。 因此，脚本将执行两个步骤：

- 它验证要执行快照的脚本有权访问租户的存储虚拟机和接口。
- 根据 HANA 实例为每个卷创建一个测试或虚构快照。

因此，HANA 实例包含为参数。 如果执行失败，无法提供与存储连接相关的错误检查。 但该脚本在此类情况中可提供有用提示。

脚本的运行方式：
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
在下一步中，脚本将尝试使用之前设置步骤中提供的公钥和在 HANABackupCustomerDetails.txt 中配置的数据登录到存储。 如果成功，将显示以下内容：

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

如果出现问题，连接存储控制台时输出将如下所示：

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

成功登录到存储虚拟机接口后，脚本将继续执行阶段 #2 并为 SAP HANA 的三节点横向扩展配置创建测试快照，如下所示：

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

如果已使用脚本成功执行测试快照，则可以继续配置实际的存储快照。 如果不成功，请先调查问题，然后再继续操作。 创建的测试快照应保留至第一批实际快照完成为止。



### <a name="step-7-perform-snapshots"></a>步骤 7：执行快照

在完成所有准备步骤后，可以开始配置实际存储快照的配置。 要计划的脚本会使用 SAP HANA 纵向扩展和横向扩展配置。 建议通过 cron 来计划脚本的执行。 

可实现三种类型的快照备份：
- HANA：组合快照备份，该备份中包含 /hana/data、/hana/log 以及 /hana/shared（包含 /usr/sap）的卷的快照通过协调式快照实现。 可从此快照还原单个文件。
- 日志：/hana/logbackups 卷的快照备份。 执行此存储快照时不触发任何 HANA 快照。 此存储卷是要包含 SAP HANA 事务日志备份的卷，其执行频率更高，以此限制日志增长并防止潜在的数据丢失。 可从此快照还原单个文件。 其执行频率不应小于五分钟。
- 启动：包含 HANA 大型实例启动 LUN 的卷的快照。 此快照备份仅适用于 HANA 大型实例 I 类型 SKU。 无法从包含启动 LUN 的卷的快照执行单个文件还原。  


这三种不同类型的快照的调用语法如下所示：
```
HANA backup covering /hana/data, /hana/log, /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot manual 30

```

需要指定以下参数：

- 指定的第一个参数确定相应快照备份类型的特征。 参数值允许使用“HANA”、“日志”和“启动”
- 第二个值是 HANA SID（如 HM3）。 执行启动卷的备份时不需要此参数。
- 第三个参数是一个快照，是快照类型的备份标签。 对于类型或频率特定的计划快照，该参数应保持不变。
- 第四个参数通过定义要保留的快照数（拥有相同快照前缀/标签）间接定义快照的保留期。 对于通过 cron 制定的执行计划，此参数十分重要。 

在横向扩展事例中，脚本会执行一些附加检查，确保在继续创建 SAP HANA 快照（随后执行存储快照）之前，可以访问所有 HANA 服务器，并且所有 HANA 实例返回实例的相应状态。

执行 azure\_hana\_backup.pl 脚本时会在以下三个不同的阶段创建存储快照：

- 执行 SAP HANA 快照。
- 执行存储快照。
- 删除已创建的 SAP HANA 快照，然后再执行存储快照。

从脚本已复制到的 HDB 可执行文件文件夹调用该脚本，将它执行。 

保留期会根据执行脚本时作为参数提供的快照数（例如 30，如上所示）管理。 因此存储快照消耗的时间量是脚本执行时间和作为参数提供的快照数的函数。 如果保留的快照数超过了脚本调用中指定为参数的数字，则执行新快照之前，将删除同一标签中最旧的存储快照（在上述示例中为 manual）。 这意味着，作为最后一个调用参数指定的数字是可用于控制保留的快照数的数字。 通过该数字还可间接控制用于快照的磁盘空间。 

>更改标签后，将立即重新开始计数。 这意味着对标签的处理必须严格。

### <a name="snapshot-strategies"></a>快照策略
不同类型快照的频率取决于是否使用 HANA 大型实例灾难恢复功能。 由于 HANA 大型实例灾难恢复功能依赖于存储快照，所以可能会应用某些与存储快照频率和执行时间相关的特殊建议。 下方的注意事项和建议假设用户不使用 HANA 大型实例提供的灾难恢复功能。 假设用户使用存储快照来获取备份并提供过去 30 天的时点恢复。 鉴于快照数和空间的限制，部署的客户需考虑这类要求：

- 时间点恢复的恢复时间目标要求。
- 空间使用量。
- 潜在灾难恢复的恢复点目标和恢复时间目标要求
- 针对磁盘最终执行的 HANA 完整数据库备份。 每当针对磁盘执行完整数据库备份或执行 _backint_ 接口时，存储快照的执行会失败。 如果打算基于存储快照执行完整数据库备份，请确保在此期间已禁用存储快照执行。
- 每个卷的快照数限制为 255。


对于不使用 HANA 大型实例灾难恢复功能的客户，快照频率会低一些。 在这类情况下，客户以 12 小时或 24 小时的周期对 /hana/data、/hana/log 和 /hana/shared（包括 /usr/sap）执行组合快照，并将这些快照保留一个月。 对日志备份卷的快照也执行相同操作。 但日志备份卷的 SAP HANA 事务日志备份按 5 - 15 分钟的周期执行。

建议使用 cron 执行计划的存储快照，使用同一个脚本来满足所有备份和灾难恢复需求（修改脚本输入，使之与请求的不同备份时间匹配）。 可以根据这些快照的执行时间，在 cron 中做好上述一切计划：每小时、12 小时、每天或每周。 

以下是 /etc/crontab 中的一个 cron 计划的示例：
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
上述示例中有一个每小时执行一次的组合快照，该快照的实施对象包括含 /hana/data、/hana/log 和 /hana/shared（包括 /usr/sap）位置的卷。 此类型的快照将用于快速实现两天内的时点恢复。 此外，将对这些卷执行每日快照。 因此，将执行两天的每时快照和四周的每日快照。 此外，事务日志备份卷每天备份一次。 这些备份将保留四周。 从 crontab 第三行可知，计划每五分钟执行一次 HANA 事务日志备份。 还会注意到执行存储快照的不同 cron 作业的启动时间是错开的，因此不会在某个时间点同时执行所有这些快照。 

在下方示例中，将每小时执行一次组合快照，该快照涵盖包含 /hana/data、/hana/log 和 /hana/shared（包括 /usr/sap）位置的卷。 这些快照将保留两天。 事务日志备份卷的快照每五分钟执行一次，并保留四个小时。 与之前一样，HANA 事务日志文件的备份计划为每五分钟执行一次。 启动事务日志备份后，执行事务日志备份卷的快照时会存在两分钟的延迟。 一般情况下，SAP HANA 事务日志备份应在这两分钟内完成。 与之前一样，包含启动 LUN 的卷通过存储快照每天备份一次，且将保留四周。

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

如果以一个序列图表示（不包括启动 LUN）上述示例，该序列图应如下所示：

![备份和快照之间的关系](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot.PNG)

SAP HANA 对 /hana/log 卷执行常规写入，将提交的更改记录到数据库。 SAP HANA 将保存点定期写入 /hana/data 卷。 根据 crontab 中所指定，SAP HANA 事务日志备份每五分钟执行一次。 由于对 /hana/data、/hana/log 和 /hana/shared 卷触发组合存储快照，所以还会每小时执行一次 SAP HANA 快照。 成功执行 HANA 快照后，将执行组合存储快照。 如 crontab 中所指示，在 HANA 事务日志备份后两分钟左右，/hana/logbackup 卷上的存储快照开始每五分钟执行一次。


>[!IMPORTANT]
> 仅当与 SAP HANA 事务日志备份配合执行存储快照时，才能针对 SAP HANA 备份使用存储快照。 这些事务日志备份需要能够涵盖执行存储快照的间隔时间段。 

如果向用户承诺时间点恢复目标为 30 天，则需要满足以下条件：

- 能够访问针对 /hana/data、/hana/log 和 /hana/shared 且保留期限已达 30 天（一种极端情况）的组合存储快照。
- 保留时长长于任何两个组合存储快照间隔时间的连续事务日志备份。 因此事务日志备份卷快照的最长保留时长需为 30 天。 除非同时将事务日志备份复制到位于 Azure 存储的另一个 NFS 共享。 在此情况下，可以从该 NFS 共享拉取旧事务日志备份。

为了能够从存储快照和事务日志备份的最终存储复制中获益，需要更改 SAP HANA 写入事务日志备份的位置。 可在 HANA Studio 中以下方所示的方式完成此更改。 虽然 SAP HANA 会自动备份完整日志段，但仍应指定确定的日志备份间隔时间。 尤其在使用灾难恢复选项时，通常需要以确定的周期执行日志备份。 在以下事例中，日志备份间隔定为 15 分钟。

![在 SAP HANA Studio 中计划 SAP HANA 备份日志](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

可以选择频率高于 15 分钟的备份。 尤其是同时实施了灾难恢复时。 某些客户每五分钟执行一次事务日志备份。  

如果之前从未备份过数据库，则最后一步是执行基于文件的数据库备份，以创建必须在备份目录中存在的单个备份项。 否则，SAP HANA 无法启动指定的日志备份。

![创建基于文件的备份以创建单个备份项](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


成功执行第一个存储快照后，还可删除在步骤 6 中执行的测试快照。 为实现此操作，需要运行 removeTestStorageSnapshot.pl 脚本，如下所示：
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>监视磁盘卷上的快照数量和大小

在特定的存储卷上，可以监视快照的数量以及这些快照的存储消耗量。 `ls` 命令不会显示快照目录或文件。 然而，Linux OS `du` 命令显示有关这些存储快照的详细信息（因为这些快照存储在相同的卷上）。 此命令适用以下选项：

- `du –sh .snapshot` 提供快照目录中所有快照的总数。
- `du –sh --max-depth=1` 列出 .snapshot 文件夹中保存的所有快照，以及每个快照的大小。
- `du –hc` 提供所有快照占用的总大小。

使用以下命令可确保创建和存储的快照不会耗尽卷上的所有存储。

>[!NOTE]
>使用上述命令时，不显示启动 LUN 的快照。

### <a name="getting-details-of-snapshots"></a>获取有关快照的详细信息
若要获取有关快照的更多详细信息，也可以使用 azure\_hana\_snapshot\_details.pl 脚本。 如果灾难恢复位置处存在活动服务器，则可以在任一位置运行此脚本。 此脚本提供由每个包含快照的卷划分的以下信息：一个卷中全部快照的大小和该卷中每个快照的详细信息（快照名称、创建时间、快照大小、快照频率以及与该快照关联的 HANA 备份 ID（如果两者相关）。 此脚本的执行语法如下所示：

```
./azure_hana_snapshot_details.pl 
```

由于脚本会尝试检索 HANA 备份 ID，需要连接到 SAP HANA 实例，因此需要正确设置 HANABackupCustomerDetails.txt 配置文件。 卷中两个快照的输出如下所示：

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


### <a name="file-level-restore-from-storage-snapshot"></a>从存储快照实现文件级还原
对于“hana”和“日志”快照类型，可以直接在目录“.snapshot”中的卷上访问快照。 可查找每个快照的子目录。 应能够将执行了快照的每个文件以文件在快照瞬间的状态从该子目录复制到实际目录结构。

>[!NOTE]
>单个文件还原不适用于启动 LUN 的快照。 “.snapshot”目录不在启动 LUN 中公开。 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>减少服务器上的快照数

如前所述，可以减少存储的带有特定标签的快照数。 用于启动快照的命令的最后两个参数是要保留的快照的标签与数量。

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

在上面的示例中，快照标签为“客户”，要保留的带有此标签的快照数为 30。 对磁盘空间消耗量做出响应时，可能想要减少存储的快照数。 例如，如果希望将快照数减少至 15，实现此操作的简单方法是将脚本的最后一个参数设置为 15 并运行该脚本：

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

使用此设置运行脚本后，快照数（包括新存储快照）为 15。 会保存最新的 15 个快照，同时删除 15 个较旧的快照。

 >[!NOTE]
 > 仅当存在超过一小时的快照时，此脚本才能减少快照数。 该脚本不会删除不超过一小时的快照。 这些限制与提供的可选灾难恢复功能相关。

如果不再想要保留一组包含特定备份标签（如语法示例中的 hanadaily）的快照，可以使用 0 作为保留数量来执行该脚本，这样即可删除所有与该标签匹配的快照。 但是，删除所有快照可能会影响灾难恢复的功能。

第二种删除特定快照的方法是使用 azure\_hana\_snapshot\_delelte.pl 脚本。 此脚本旨在使用在 HANA Studio 中找到的 HANA 备份 ID 或通过快照名称本身删除快照或一组快照。  目前，备份 ID 仅与为 HANA 快照类型创建的快照绑定。 “日志”和“启动”类型的快照备份不执行 SAP HANA 快照。 因此，这些快照没有可供查找的备份 ID。 如果输入快照名称，将在不同卷上查找所有与输入的快照名称匹配的快照。 该脚本的调用语法是：

```
./azure_hana_snapshot_delete.pl 

```

需要将脚本作为用户“根”执行

如果选中一个快照，可单独删除一个快照。 首先会要求用户输入包含快照的卷，然后输入实际的快照名。 如果快照存在于该卷且存在时间超过一小时，将删除该快照。 可以通过执行 azure_hana_snapshot_details 脚本查找卷名和快照名。 

>[!IMPORTANT]
>如果有数据仅存在于要删除的快照上，执行删除操作则意味着将永久丢失该数据。

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>恢复到最近的 HANA 快照

在遇到生产中断的情况时，可以向 Microsoft Azure 支持提出客户事件，启动从存储快照恢复的过程。 这种意外的情景是由于在生产系统中删除了数据造成的高度紧急问题，这时，检索数据的唯一办法就是还原生产数据库。

在不同情况下，时间点恢复可能不太紧急，因此可以提前几天规划。 可与 Azure 上的 SAP HANA 服务管理部门合作规划这种恢复，而不要提出高优先级问题。 例如，如果尝试通过应用新的增强包升级了 SAP 软件，然后又需要还原到升级 EHP 之前的快照状态，则可以进行这种规划。

提出请求之前，需要做好一些准备工作。 然后，Azure 上的 SAP HANA 服务管理团队便可以处理请求，提供还原的卷。 接下来，需要负责基于快照还原 HANA 数据库。 下面说明了如何准好请求准备：

>[!NOTE]
>根据所用的 SAP HANA 版本，用户界面可能与下面所示的屏幕截图不同。

1. 确定要还原的快照。 除非另有说明，否则只能还原 HANA/数据卷。 

2. 关闭 HANA 实例。

 ![关闭 HANA 实例](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 卸载每个 HANA 数据库节点上的数据卷。 如果仍将数据卷载入操作系统，快照还原将失败。
 ![卸载每个 HANA 数据库节点上的数据卷](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. 提出 Azure 支持请求，指示要还原特定的快照。

 - 还原期间：Azure 上的 SAP HANA 服务管理部门可能要求用户参加电话会议以进行协调、验证和确认工作，由此确保还原的是正确的存储快照。 

 - 还原后：Azure 上的 SAP HANA 服务管理部门会告知存储快照已还原。

5. 完成还原过程后，请重新装载所有数据卷。

 ![重新装载所有数据卷](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. 在 SAP HANA Studio 中选择恢复选项（如果在通过 SAP HANA Studio 重新连接到 HANA DB 时未自动显示）。 以下示例演示如何还原到最后一个 HANA 快照。 存储快照中嵌入了一个 HANA 快照，如果要还原到最近的存储快照，该快照应是最近的 HANA 快照。 （如果要还原到较旧的存储快照，需要根据存储快照的创建时间找到 HANA 快照。）

 ![在 SAP HANA Studio 中选择恢复选项](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. 选择“将数据库恢复到特定的数据备份或存储快照”。

 ![“指定恢复类型”窗口](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. 选择“指定不包含目录的备份”。

 ![“指定备份位置”窗口](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. 在“目标类型”列表中，选择“快照”。

 ![“指定要恢复的备份”窗口](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. 单击“完成”启动恢复过程。

 ![单击“完成”启动恢复过程](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 数据库已还原并恢复到存储快照包含的 HANA 快照。

 ![HANA 数据库已还原并恢复到 HANA 快照](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>恢复到最新状态

以下过程将还原存储快照中包含的 HANA 快照。 然后，将事务日志备份还原到还原存储快照之前的最新数据库状态。

>[!IMPORTANT]
>在继续下一步之前，请确保具备完整且连续的事务日志备份链。 如果没有这些备份，则无法还原数据库的当前状态。

1. 执行上述“恢复到最近的 HANA 快照”过程的步骤 1-6。

2. 选择“将数据库恢复到其最近状态”。

 ![选择“将数据库恢复到其最近状态”](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 指定最新 HANA 日志备份的位置。 该位置需要包含从创建 HANA 快照开始，到处于最新状态为止的所有 HANA 事务日志备份。

 ![指定最新 HANA 日志备份的位置](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 选择一个备份用作恢复数据库的基础。 在本示例中，屏幕截图中的 HANA 快照为存储快照中包含的 HANA 快照。 

 ![选择一个备份用作恢复数据库的基础](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 如果增量备份在创建 HANA 快照之后、处于最近状态之前不存在，请清除“使用增量备份”复选框。

 ![如果不存在增量备份，则清除“使用增量备份”复选框](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 在摘要屏幕上单击“完成”启动还原过程。

 ![在摘要屏幕上单击“完成”](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>恢复到另一个时间点
若要恢复到 HANA 快照（包含在存储快照中）与晚于 HANA 快照时间点恢复之间的时间点，请执行以下操作：

1. 确保提供从创建 HANA 快照开始，到要恢复到的时间为止的所有事务日志备份。
2. 在“恢复到最新状态”下面开始恢复过程。
3. 执行步骤 2 时，请在“指定恢复类型”窗口中选择“将数据库恢复到以下时间点”，指定时间点，然后完成步骤 3-6。

### <a name="monitoring-the-execution-of-snapshots"></a>监视快照执行

使用 HANA 大型实例的存储快照时，还需要监视这些存储快照的执行。 执行存储快照的脚本会将输出写入某个文件，并将其保存在 Perl 脚本所在的同一位置。 会针对每个存储快照单独写入一个文件。 每个文件的输出明确显示了快照脚本执行的各个阶段：

- 查找需要创建快照的卷
- 查找基于这些卷创建的快照
- 根据指定的快照数量删除最终的现有快照
- 创建 SAP HANA 快照
- 基于卷创建存储快照
- 删除 SAP HANA 快照
- 将最新的快照重命名为“.0”

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
从此示例中可以看到脚本如何记录 HANA 快照的创建过程。 在横向扩展部署中，此过程在主节点上启动。 主节点在每个辅助角色节点上启动 SAP HANA 快照的同步创建。 然后创建存储快照。 成功执行存储快照后，HANA 快照会被删除。 从主节点重新启动。


## <a name="disaster-recovery-principles"></a>灾难恢复原则
对于 HANA 大型实例，我们提供不同 Azure 区域中 HANA 大型实例戳之间的灾难恢复功能。 这意味着如果在 Azure 美国西部区域部署 HANA 大型实例单元，则可以在美国东部区域将 HANA 大型实例单元作为灾难恢复单元利用。 如前所述，不会自动配置灾难恢复，因为用户也需要为 DR 区域中的另一个 HANA 大型实例单元付费。 灾难恢复设置适用于纵向扩展和横向扩展设置。 

在目前已部署的方案中，客户使用 DR 区域中的单元运行非生产系统，这些系统使用安装的一个 HANA 实例。 HANA 大型实例单元的 SKU 应与用于生产目的的 SKU 相同。 然后，需要订购更多磁盘卷，目的是将这些磁盘卷用作从生产系统到灾难恢复站点的存储复制操作的目标。 实际将被复制到 DR 站点的卷包含：

- /hana/data
- /hana/log
- /hana/shared
- /hana/log/backup（包括 /usr/sap）


灾难恢复功能的提供基于 HANA 大型实例基础结构提供的存储复制功能。 存储发生变更时，存储端上使用的功能并不是以异步方式复制的恒定更改流。 它是一种机制，并依赖于一个事实：快照基于这些卷定期创建。 然后，已复制的快照和尚未复制的新快照之间的差量会转移到灾难恢复站点中的目标磁盘卷（与生产卷大小相同）。 首次传输时，需要在快照之间的差量导致卷的数据量变小之前，将卷中的数据进行完整转移。 因此，DR 站点中的卷包含在生产站点中执行的每个卷的快照。 这样便可以最终使用该 DR 系统回到较早的状态，从而复原丢失的数据而无需回滚生产系统。

在生产站点中将 HANA 系统复制作为高可用性功能使用时，将仅复制第 2 层（或副本）实例的卷。 此配置可产生后述效果：如果保留或移除次要副本（第 2 层）服务器单元或此单元中的 SAP HANA 实例，将存储复制到 DR 站点时可能出现延迟。 

>[!IMPORTANT]
>对于多层 HANA 系统复制，当使用 HANA 大型实例灾难恢复功能时，如果关闭第 2 层 HANA 实例或服务器单元，将中断到灾难恢复站点的复制。


>[!NOTE]
>HANA 大型实例存储复制功能会复制存储快照或对其实施镜像操作。 因此，如果不按照本文档备份部分中介绍的方式执行存储快照，则无法复制到灾难恢复站点。 执行存储快照是将存储复制到灾难恢复站点的先决条件。

为了将恢复点目标最小化，HANA 大型实例服务中设置了以下复制间隔时间：
- 组合快照（快照类型为“hana”）涵盖的卷每 15 分钟复制到灾难恢复站点中的等效存储卷目标。
- 事务日志备份卷（日志类型快照）每 3 分钟复制到灾难恢复站点中的等效存储卷目标。

为了将恢复点目标最小化，用户需要进行以下设置
- 每三十分钟至一小时执行一次“hana”类型存储快照（请参阅步骤 7 - 执行快照）。
- 每五分钟执行一次 SAP HANA 事务日志备份。
- 每 5 - 15 分钟执行一次“日志”类型存储快照。 使用此周期，RPO 应能达到 15 - 25 分钟。

如果使用此设置，事务日志备份序列、存储快照、HANA 事务日志备份卷的复制和 /hana/data、/hana/log、/hana/shared（包括 /usr/sap）将如下图所示。

 ![事务日志备份快照和时间轴上的快照镜像之间的关系](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

在灾难恢复方案中，要实现更好的恢复点目标，可以将 Azure 上的 SAP HANA（大型实例）中的 HANA 事务日志备份复制到其他 Azure 区域。 若要进一步降低恢复点目标，请执行以下大致步骤：

1. 以尽可能高的频率将 HANA 事务日志备份到 /hana/logbackups。
2. 完成事务日志备份后，使用 rsync 将其复制到托管 NFS 共享的 Azure 虚拟机上（位于 Azure 生产区域和 DR 区域中的 Azure 虚拟网络）。 需要将两个 Azure VNet 都连接到 ExpressRoute 线路，该线路将 HANA 生产大型实例连接到 Azure（请参阅本文档“灾难恢复要求”部分中的图）。 
3. 在该区域中附加有 NFS 导出存储的 VM 上保留事务日志备份。
4. 在灾难故障转移方案中，使用在灾难恢复站点中 NFS 共享上较新创建的事务日志备份去补充在 /hana/logbackups 卷上找到的事务日志备份。 
5. 现在，可以对可保存到 DR 区域的最新备份执行事务日志备份还原。

## <a name="disaster-recovery-fail-over-procedure"></a>灾难恢复故障转移过程
如果希望或需要故障转移到 DR 站点，则在此过程中需要与 Azure 上的 SAP HANA 操作部门互动。 目前此过程的大致步骤如下所示：

- 由于在 HANA 大型实例的灾难恢复单元上运行了一个 HANA 非生产实例，所以需要关闭此实例。 假定已预安装 HANA 休眠生产实例。
- 需要确保不再运行任何 SAP HANA 进程。 可使用以下命令执行此检查：/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList。 输出应显示 hdbdaemon 进程处于停止状态且没有其他 HANA 进程处于运行或已开始状态。
- 现在需要检查希望将灾难恢复站点恢复到的快照名称或 SAP HANA 备份 ID。 在真实的灾难恢复事例中，此快照通常是最新的快照。 然而用户有时候可能需要恢复丢失的数据。 这时就需要选择更早的快照。
- 需要通过高优先级支持请求联系 Azure 上的 SAP HANA 服务管理部门，并请求在 DR 站点上恢复该快照/HANA 备份 ID。 在操作端，将执行以下步骤：
    - 停止生产卷到灾难恢复卷的快照复制。 前提是尚未因生产站点服务中断而引发此结果。
    - 将在灾难恢复卷上还原所选的存储快照/备份 ID。
    - 还原后，灾难恢复卷可载入灾难恢复区域中的 HANA 大型实例单元。
- 客户需执行的下一步操作是将灾难恢复卷载入灾难恢复站点中的 HANA 大型实例单元。 
- 然后便可以启动目前的 SAP HANA 休眠生产实例。
- 如果此外还选择复制事务日志备份以缩短 RPO 时间，则需要将这些事务日志备份合并到新装载的 DR /hana/logbackups 目录。 请不要覆盖现有备份，而是仅复制尚未被存储复制的较新备份。

接下来的一系列步骤包括基于还原的存储快照和可用的事务日志备份恢复 SAP HANA 生产实例。 步骤如下所示：

使用 SAP HANA Studio 将备份位置更改为 /hana/logbackups，如![更改 DR 恢复的备份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)下方所示

SAP HANA 会逐个扫描备份文件位置并建议要还原到的最新事务日志备份。 扫描需要花费几分钟时间，扫描完成后会显示如下所示的屏幕：![DR 恢复的事务日志备份列表](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

下一步需要执行以下操作来调整某些默认设置：

- 取消选择“使用增量备份”
- 选择“初始化日志区域”，如下所示：

 ![设置初始化日志区域](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

在接下来显示的屏幕中，按“完成”，如下所示：

 ![完成 DR 还原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

应显示如此处所示的进度窗口。 请谨记，此为 SAP HANA 三节点横向扩展配置的灾难恢复还原示例。

 ![还原进度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果还原过程一直停留在“完成”屏幕且不显示进度屏幕，请检查辅助角色节点上的所有 SAP HANA 实例是否正常运行。 如有必要，请手动启动 SAP HANA 实例。


### <a name="fail-back-from-dr-to-production-site"></a>从 DR 到生产站点的故障回复
假定到灾难恢复站点的故障转移是由 Azure 生产区域中的问题引起，而非因用户请求找回丢失数据而引起。 这意味着用户现在处于生产状态或在灾难恢复站点停留了一段时间。 随着生产站点中问题的解决，可能需要在生产站点上进行故障回复。 由于不能丢失数据，所以回到生产站点这一过程需包含几个步骤且需要与 Azure 上的 SAP HANA 操作部门进行紧密协作。 问题解决后，用户需立即触发相关操作以同步到生产站点。

步骤顺序如下所示：

- Azure 上的 SAP HANA 操作部门获取从灾难恢复存储卷同步生产存储卷（现表示生产状态）的触发指令。 在此状态中，生产站点中的 HANA 大型实例单元处于关闭状态。
- Azure 上的 SAP HANA 操作部门监视复制并确保在通知用户前实现跟进
- 需要关闭在灾难恢复站点使用 HANA 生产实例的应用程序。 接着执行 HANA 事务日志备份，然后停止灾难恢复站点中 HANA 大型实例单元上运行的 HANA 实例
- 关闭灾难恢复站点中 HANA 大型实例单元上运行的 HANA 实例后，需要再次手动同步磁盘卷。
- Azure 上的 SAP HANA 操作部门将再次启动生产站点中的 HANA 大型实例单元，然后由用户进行操作。 请确保在 HANA 大型实例单元启动时，SAP HANA 实例处于关闭状态。
- 现在需要执行相同的数据库还原步骤，与之前故障转移到灾难恢复站点时一样。

### <a name="monitoring-disaster-recovery-replication"></a>监视灾难恢复复制

可通过执行脚本 azure\_hana\_replication\_status.pl 监视存储复制过程的状态。 必须从灾难恢复位置中运行的单元运行此脚本，否则它将不会按预期正常运行。 无论复制是否处于活动状态，此脚本都将正常运行。 可为灾难恢复位置中租户的每个 HANA 大型实例单元运行此脚本。 不能使用此脚本获取有关启动卷的详细信息。

调用如下所示的脚本：
```
./replication_status.pl <HANA SID>
```

卷将输出细分为以下部分：  

- 链接状态
- 当前复制活动
- 最新复制的快照 
- 最新快照的大小
- 上一个完成的快照复制与当前复制之间的迟延时间（当前）。  

链接状态显示为“活动”，除非位置之间的链接关闭或当前正在进行故障转移事件。 复制活动处理的问题：当前是否正在复制任何数据、数据是否处于空闲状态或该链接是否发生其他活动。 上一个复制的快照应仅显示为“snapmirror…”。 然后将显示上一个快照的大小。 最后显示迟延时间。 迟延时间表示从计划的复制时间到复制结束之间的时间。 对于数据复制，迟延时间可能超过一个小时，尤其是首次复制（尽管复制已开始）。 迟延时间将继续延长，直到完成正在进行的复制。

以下是一个输出示例：

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












