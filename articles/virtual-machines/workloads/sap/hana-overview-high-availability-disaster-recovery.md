---
title: "Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复 | Microsoft Docs"
description: "为 Azure 上的 SAP HANA（大型实例）建立高可用性并规划灾难恢复"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9ef09e33803a976e05e555ec7ae9eb872d237137
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复 

>[!IMPORTANT]
>本文档不是 SAP HANA 管理文档或 SAP 说明的替代文档。 读者应深入了解 SAP HANA 管理和操作的专业技能。 尤其是在备份、还原和高可用性以及灾难恢复主题方面。 本文档中提供 SAP HANA Studio 的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。 因此，重要的是使用自己的 HANA 版本或发布，在相应的环境中执行步骤和过程。 本文档中介绍的某些过程进行了简化，以便于理解，不是用作最终操作手册的详细步骤。 如果想针对特定的配置创建操作手册，需要测试并实践相关过程，并记录与特定配置相关的过程。 


高可用性和灾难恢复 (DR) 是运行任务关键型 Azure 上的 SAP HANA（大型实例）服务器的重要方面。 必须咨询 SAP、系统集成商和/或 Microsoft，以便正确构建和实施适当的高可用性和灾难恢复策略。 此外，必须考虑到与环境相关的恢复点目标 (RPO) 和恢复时间目标。

Microsoft 通过 HANA 大型实例支持一些 SAP HANA 高可用性功能。 这些功能包括：

- **存储复制**：存储系统能够将所有数据复制到另一个 Azure 区域中的另一个 HANA 大型实例戳记。 SAP HANA 的工作独立于此方法。 此功能是为 HANA 大型实例提供的默认灾难恢复机制。
- **HANA 系统复制**：将 [SAP HANA 中的所有数据](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)复制到独立的 SAP HANA 系统。 通过定期复制数据最大限度地减小恢复时间目标。 SAP HANA 支持异步模式、内存中同步模式和同步模式。 建议仅在同一数据中心或者距离不超过 100 公里的范围内针对 SAP HANA 系统使用同步模式。 在 HANA 大型实例戳的当前设计中，HANA 系统复制仅可用于在一个区域内实现高可用性。 目前，HANA 系统复制要求使用第三方反向代理或路由组件将灾难恢复配置传送到另一 Azure 区域。 
- **主机自动故障转移**：用于替代 HANA 系统复制的 SAP HANA 本地故障恢复解决方案。 如果主节点不可用，可在横向扩展模式下配置一个或多个备用 SAP HANA 节点，SAP HANA 会自动故障转移到备用节点。

Azure 上的 SAP HANA（大型实例）在三个不同地缘政治区域（美国、澳大利亚和欧洲）的两个 Azure 区域中提供。 即将推出日本地缘政治区域。 位于同一地缘政治区域，托管 HANA 大型实例戳的两个不同区域与用于复制存储快照的单独专用网络线路连接，以提供灾难恢复方法。 默认情况下不建立复制。 该功能是为订购灾难恢复功能的客户设置。 存储复制取决于 HANA 大型实例存储快照的使用情况。 无法选择 Azure 区域作为 DR 区域，因为它位于另一个地缘政治区域。 

下表显示了当前支持的高可用性和灾难恢复方法与组合：

| HANA 大型实例支持的方案 | 高可用性选项 | 灾难恢复选项 | 注释 |
| --- | --- | --- | --- |
| 单节点 | 不可用。 | 专用 DR 设置。<br /> 多用途 DR 设置。 | |
| 主机自动故障转移：N+m<br /> 包括 1+1 | 在备用节点充当活动角色的情况下可行。<br /> HANA 控制角色切换。 | 专用 DR 设置。<br /> 多用途 DR 设置。<br /> 使用存储复制实现 DR 同步。 | HANA 卷集附加到所有节点 (n+m)。<br /> DR 站点必须拥有相同的节点数。 |
| HANA 系统复制。 | 在使用主要或辅助设置的情况下可行。<br /> 在故障转移案例中，辅助角色变为主要角色。<br /> HANA 系统复制和 OS 控制故障转移。 | 专用 DR 设置。<br /> 多用途 DR 设置。<br /> 使用存储复制实现 DR 同步。<br /> 在没有第三方组件的情况下，无法使用 HANA 系统复制实现 DR。 | 单独一组磁盘卷附加到每个节点。<br /> 仅生产站点中的辅助副本磁盘卷被复制到 DR 位置。<br /> DR 站点中需要一组卷。 | 

专用 DR 设置是在不用于运行任何其他工作负荷或非生产系统的 DR 站点中部署 HANA 大型实例单元。 此单元是被动单元，仅当执行灾难故障转移时才部署。 不过，对于许多客户而言，此设置不是首选项。

> [!NOTE]
> [SAP HANA MCOD 部署](https://launchpad.support.sap.com/#/notes/1681092)（一个单元上多个 HANA 实例）作为覆盖方案，与表中列出的 HA 和 DR 方法配合使用。 例外情况是将 HANA 系统复制与基于 Pacemaker 的自动故障转移群集配合使用。 这种情况仅支持一个单元一个 HANA 实例。 而对于 [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) 部署，如果部署了多个租户，仅非基于存储的 HA 和 DR 方法可用。 若仅部署了一个租户，则上面列出的所有方法均有效。  

多用途 DR 设置在运行非生产工作负荷的 DR 站点中部署 HANA 大型实例单元。 发生灾难时，可关闭非生产系统，装载存储复制的（其他）卷集，并启动生产 HANA 实例。 使用 HANA 大型实例灾难恢复功能的大多数客户都使用此配置。 


可在以下 SAP 文章中找到有关 SAP HANA 高可用性的详细信息： 

- [SAP HANA 高可用性白皮书](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理指南](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [有关 SAP HANA 系统复制的 SAP 学院视频](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 支持说明 #1999880 - 有关 SAP HANA 系统复制的常见问题](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP 支持说明 #2165547 - SAP HANA 系统复制环境中的 SAP HANA 备份和还原](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 支持说明 #1984882 - 使用 SAP HANA 系统复制在最大限度地减少停机时间/不停机的情况下实现硬件更换](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>使用 HANA 大型实例实现灾难恢复的网络注意事项

若要利用 HANA 大型实例的灾难恢复功能，需要规划好与两个不同 Azure 区域之间的网络连接。 需使用一条 Azure ExpressRoute 线路从本地连接到主要 Azure 区域，使用另一个线路从本地连接到灾难恢复区域。 这种措施可以应对 Azure 区域（包括 Microsoft 企业边缘路由器 (MSEE) 位置）中出现的问题。

还可以采取第二种措施，就是将连接到某个区域中 Azure 上的 SAP HANA（大型实例）的所有 Azure 虚拟网络都连接到 ExpressRoute 线路，此线路将连接其他区域的 HANA 大型实例。 通过此“跨连接”，1 号区域的 Azure 虚拟网络中运行的服务可以连接到 2 号区域中的 HANA 大型实例单元，也可以从后者连接到前者。 这种措施可以避免当 Azure 离线时，只有一个 MSEE 位置连接到本地位置。

下图展示了灾难恢复方案的弹性配置：

![灾难恢复的最佳配置](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-when-you-use-hana-large-instances-storage-replication-for-disaster-recovery"></a>使用 HANA 大型实例存储复制实现灾难恢复时的其他要求

使用 HANA 大型实例实现灾难恢复设置的其他要求包括：

- 订购的 Azure 上的 SAP HANA（大型实例）的 SKU 大小必须与生产 SKU 大小相同，并且应部署在灾难恢复区域中。 在当前客户部署中，这些实例用于运行非生产 HANA 实例。 这些配置被称为“多用途 DR 设置”。   
- 必须在 DR 站点上为想要在灾难恢复站点中恢复的每个 Azure 上的 SAP HANA（大型实例）SKU 订购附加存储。 购买附加存储可以分配存储卷。 可以分配用作从生产 Azure 区域复制到灾难恢复 Azure 区域的存储复制目标的存储卷。

 

## <a name="backup-and-restore"></a>备份和还原

在操作数据库方面，一个要点是在发生各种灾难事件时保护数据库。 这些事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。

备份数据库并具备还原到任意时间点（例如，在某人删除关键数据之前的时间点）的能力，可以尽可能地还原到发生中断之前所处的状态。

为获得最佳效果，必须执行两种类型的备份：

- 数据库备份：完整备份、增量备份或差异备份
- 事务日志备份

除了在应用程序级别执行完整数据库备份以外，还可以使用存储快照执行备份。 存储快照不会替代事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 但是，存储快照可以通过快速提供数据库的前滚映像来提升恢复速度。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- 自制 (DIY)。 计算并确保有足够的磁盘空间后，使用磁盘备份方法执行完整的数据库和日志备份。 可以直接备份到 HANA 大型实例单元上附加的卷，或者备份到 Azure 虚拟机 (VM) 中设置的网络文件共享 (NFS)。 在后一种情况中，客户在 Azure 中设置 Linux VM、将 Azure 存储附加到 VM 并通过该 VM 中配置的 NFS 服务器共享此存储。 如果对直接附加到 HANA 大型实例单元的卷执行备份，需要将备份复制到 Azure 存储帐户（在设置用于基于 Azure 存储导出 NFS 共享的 Azure VM 之后）。 或者，可以使用 Azure 备份保管库或 Azure 冷存储。 

   另一个选项是将备份复制到 Azure 存储帐户后，使用第三方数据保护工具来存储备份。 对于出于符合性和审核目的而需要长期存储的数据，还有必要使用 DIY 备份选项。 在所有情况下，备份均复制到通过 VM 和 Azure 存储表示的 NFS 共享中。

- 使用 Azure 上的 SAP HANA（大型实例）底层基础结构提供的备份和还原功能。 此选项满足备份和快速还原的需要。 本部分的余下内容介绍 HANA 大型实例提供的备份和还原功能。 本部分还会介绍备份和还原功能与 HANA 大型实例提供的灾难恢复功能之间的关系。

> [!NOTE]
> HANA 大型实例底层基础结构使用的快照技术依赖于 SAP HANA 快照。 目前，SAP HANA 快照无法与 SAP HANA 多租户数据库容器的多个租户配合工作。 因此，在 SAP HANA 多租户数据库容器中部署多个租户时，不能使用此备份方法。 如果只部署一个租户，则 SAP HANA 快照可以正常工作。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 Azure 上的 SAP HANA（大型实例）的存储快照

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 当触发对 /hana/data、和 /hana/shared（包括 /usr/sap）卷的快照时，存储快照功能将在执行存储快照前启动 SAP HANA 快照。 在恢复存储快照后，此 SAP HANA 快照是最终日志还原的设置点。
- 成功执行存储快照后，将删除 SAP HANA 快照。
- 事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来创建快照。 在这种情况下，不需要执行 HANA 快照。
- 如果必须将数据库还原到某个特定的时间点，可请求 Microsoft Azure 支持（会造成生产中断）或 Azure 上的 SAP HANA 服务管理部门还原到特定的存储快照。 一个例子就是按计划将沙盒系统还原到其原始状态。
- 存储快照中包含的 SAP HANA 快照是创建存储快照后，应用执行并存储的事务日志备份偏移点。
- 创建这些事务日志备份是为了将数据库还原到某个时间点。

可针对三种不同类型的卷执行存储快照：

- 基于 /hana/data 和 /hana/shared（包括 /usr/sap）创建组合快照。 此快照需要创建 SAP HANA 快照，为存储快照进行准备工作。 从存储点的角度来看，SAP HANA 快照可确保数据库处于一致状态。 这对于还原过程来说就是在一个点上进行设置。
- 基于 /hana/logbackups 创建单独的快照。
- OS 分区。


### <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照使用分配到 HANA 大型实例单元的存储空间。 因此，需要从计划存储快照和保存的存储快照数量角度考虑以下方面。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 特定的存储快照（在最初创建时）几乎不消耗存储空间。
- 随着数据内容以及存储卷上 SAP HANA 数据文件内容的不断更改，快照需要存储原始块内容以及数据更改。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 执行这些卷的快照会消耗卷空间。 需要确定何时计划存储快照。 此外，还需要监视存储卷的空间消耗，并管理存储的快照数。 将大量数据导入 HANA 数据库或对其执行其他重大更改时，可以禁用存储快照。 


以下部分提供了有关执行这些快照的信息，提供了一般性的建议：

- 尽管硬件可以保留每个卷的 255 个快照，但我们强烈建议保留的快照数远低于此数字。
- 执行存储快照之前，请监视并跟踪可用空间。
- 根据可用空间减少存储快照数量。 可以减少保留的快照数量，或者扩展卷。 可以 1 TB 为单位订购附加存储。
- 在使用 SAP 平台迁移工具 (R3load) 将数据移入 SAP HANA，或者从备份还原 SAP HANA 数据库或执行其他活动期间，可以禁用 /hana/data 卷上的存储快照。 
- 在对 SAP HANA 表进行重大重组期间，应尽可能地避免执行存储快照。
- 存储快照是利用 Azure 上的 SAP HANA（大型实例）灾难恢复功能的先决条件。

### <a name="prerequisites-for-leveraging-self-service-storage-snapshots"></a>利用自助服务存储快照的先决条件

为确保快照脚本成功执行，请务必在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。 Perl 已预安装在 HANA 大型实例单元中。 若要检查 perl 版本，请使用以下命令：

`perl -v`

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="setting-up-storage-snapshots"></a>设置存储快照

设置 HANA 大型实例存储快照的步骤如下：
1. 确保在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。
2. 修改 /etc/ssh/ssh\_config，添加代码行 _MACs hmac-sha1_。
3. 在运行的每个 SAP HANA 实例的主节点上创建一个 SAP HANA 备份用户帐户（如果适用）。
4. 在所有 SAP HANA 大型实例服务器上安装 SAP HANA HDB 客户端。
5. 在每个区域的第一台 SAP HANA 大型实例服务器上，创建一个公钥用于访问对快照创建操作进行控制的底层存储基础结构。
6. 将脚本和配置文件从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 复制到 SAP HANA 安装中的 **hdbsql** 位置。
7. 必要时，根据相应的客户规范修改 HANABackupDetails.txt 文件。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 方案的注意事项
如果对一个 HANA 大型实例单元上的多个 SAP HANA 实例运行[MCOD 方案](https://launchpad.support.sap.com/#/notes/1681092)，将获得为每个不同 SAP HANA 实例预配的单独存储卷。 在当前版本的自助服务快照自动化中，无法针对每个 SID 启动单独的快照。 该功能在配置文件（参阅下文）中为服务器的注册 SAP HANA 实例提供检查，并对单元上注册的所有实例的卷执行同时快照。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步骤 1：安装 SAP HANA HDB 客户端

Azure 上的 SAP HANA（大型实例）上安装的 Linux 操作系统包含所需的文件夹和脚本用于针对备份和灾难恢复目的执行 SAP HANA 存储快照。 在 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 中查找最新版本。 最新发布的脚本版本为 3.x。 相同的主要版本中，不同的脚本可能具有不同的次要发行版本。

>[!IMPORTANT]
>脚本版本从 2.1 到 3.0，配置文件的结构和脚本的某些语法发生了变化。 请查看特定部分中的标注。 

但是，客户需要负责在安装 SAP HANA 时在 HANA 大型实例单元上安装 SAP HANA HDB 客户端。 （Microsoft 不会安装 HDB 客户端或 SAP HANA。）

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

为能够访问 HANA 大型实例租户的存储快照接口，需要通过公钥建立登录。 在租户中的第一台 Azure 上的 SAP HANA（大型实例）服务器上，创建一个公钥用于访问存储基础结构，以便能够创建快照。 公钥可以确保无需使用密码登录到存储快照接口。 创建公钥也意味着不需要保留密码凭据。 在 SAP HANA 大型实例服务器上的 Linux 中，执行以下命令生成公钥：
```
  ssh-keygen –t dsa –b 1024
```
新位置为 **_/root/.ssh/id\_dsa.pub**。 请不要输入实际密码，否则，每次登录时都需要输入密码。 应该按 **Enter** 两次，从而避免登录时出现“输入密码”请求。

确保公钥按预期方式正常工作，方法是将文件夹切换到 **/root/.ssh/**，然后执行 `ls` 命令。 如果密钥存在，请运行以下命令复制公钥：

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

此时，请联系 Azure 的 SAP HANA 服务管理部门并向其提供公钥。 服务代表会使用该公钥将 SAP HANA 注册到为 HANA 大型实例租户划分的底层存储基础结构中。

### <a name="step-4-create-an-sap-hana-user-account"></a>步骤 4：创建 SAP HANA 用户帐户

为了启动 SAP HANA 快照的创建，需要在 SAP HANA 中创建一个可供存储快照脚本使用的用户帐户。 在 SAP HANA Studio 中创建用于此目的的 SAP HANA 用户帐户。 必须在 SYSTEMDB 下创建用户，而不是在 SID 数据库下创建。 此帐户必须拥有以下权限：“备份管理”和“目录读取”。 在本示例中，用户名为 **SCADMIN**。 在 HANA Studio 中创建的用户帐户名区分大小写。 出现是否要求用户下次登录时更改密码的提示时，请务必选择“否”。

![在 HANA Studio 中创建用户](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

如果在一个单元上有多个 SAP HANA 实例时采用 MCOD 部署，则需要对每个 SAP HANA 实例重复此步骤。

### <a name="step-5-authorize-the-sap-hana-user-account"></a>步骤 5：为 SAP HANA 用户帐户授权

在此步骤中，向创建的 SAP HANA 用户帐户授权，以便脚本无需在运行时提交密码。 使用 SAP HANA 命令 `hdbuserstore` 可以创建存储在一个或多个 SAP HANA 节点上的 SAP HANA 用户密钥。 用户可以使用用户密钥从脚本流程内部访问 SAP HANA，而无需管理密码。 稍后会介绍脚本过程。

>[!IMPORTANT]
>以 `root` 身份运行以下命令。 否则脚本无法正常工作。

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

如果具有 SAP HANA 横向扩展配置，则应通过单个服务器管理所有脚本。 在本示例中，必须在每台主机上更改 **SCADMIN01** 的 SAP HANA 密钥，反映与该密钥相关的主机。 使用 HANA DB 的实例编号修正 SAP HANA 备份帐户。 该密钥必须在其分配到的主机上拥有管理权限，横向扩展配置的备份用户必须有权访问所有 SAP HANA 实例。 假设三个横向扩展节点的名称分别为 lhanad01、lhanad02 和 lhanad03，命令序列如下所示：

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步骤 6：获取快照脚本、配置快照及测试配置和连接

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) 下载最新版本的脚本。 将下载的脚本和文本文件复制到 hdbsql 的工作目录。 对于当前 HANA 安装，此目录为 /hana/shared/D01/exe/linuxx86\_64/hdb。 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

关于处理 perl 脚本： 

- 除非收到 Microsoft Operations 指示，否则不要修改脚本。
- 要求修改脚本或参数文件时，始终使用 Linux 文本编辑器（例如“vi”），而不使用 Windows 编辑器（例如记事本）。 使用 Windows 编辑器可能会损坏文件格式。
- 始终使用最新的脚本。 可从 GitHub 下载最新版本。
- 在整个环境中使用相同版本的脚本。
- 测试脚本，并在生产系统中直接使用之前，熟悉所需参数和脚本的输出。
- 不要更改由 Microsoft Operations 预配的服务器的装载点名称。 这些脚本依赖于这些标准的装载点才能成功执行。


不同脚本和文件的用途如下所述：

- **azure\_hana\_backup.pl**：使用 cron 计划此脚本，以便在 HANA 数据和共享卷、/hana/logbackups 卷或 OS 上执行存储快照。
- **azure\_hana\_replication\_status.pl**：此脚本提供有关生产站点到灾难恢复站点的复制状态的基本详细信息。 此脚本用于监视以确保复制正在进行，并显示复制项的大小。 如果复制时间过长或链路关闭，此脚本还会提供指导。
- **azure\_hana\_snapshot\_details.pl**：此脚本提供有关存在于环境中每个卷的所有快照的基本详细信息列表。 此脚本可在主服务器或灾难恢复位置中的服务器单元上运行。 此脚本根据每个包含快照的卷提供以下细分信息：
   * 一个卷中全部快照的大小。
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）
- **azure\_hana\_snapshot\_delete.pl**：此脚本删除一个存储快照或一组快照。 可以使用 HANA Studio 中的 SAP HANA 备份 ID，或使用存储快照名称。 目前，该备份 ID 仅绑定到为 HANA data/log/shared 卷创建的快照。 否则，如果输入快照 ID，会查找所有与输入的快照 ID 匹配的快照。  
- **testHANAConnection.pl**：此脚本测试与 SAP HANA 实例的连接，设置存储快照时需要使用此脚本。
- **testStorageSnapshotConnection.pl**：此脚本有两个用途。 首先，它确保运行脚本的 HANA 大型实例单元有权访问已分配的存储虚拟机，并有权访问 HANA 大型实例的存储快照接口。 第二个用途是为要测试的 HANA 实例创建临时快照。 应为服务器上的每个 HANA 实例运行此脚本，从而确保备份脚本按预期正常运行。
- **removeTestStorageSnapshot.pl**：此脚本删除通过 **testStorageSnapshotConnection.pl** 脚本创建的测试快照。
- **azure\_hana\_dr\_failover.pl**：此脚本将 DR 故障转移加入到另一个区域。 该脚本需要在 DR 区域中的 HANA 大型实例单元中执行。 或者在希望故障转移到的单元上执行。 此脚本可停止从主端到辅助端的存储复制，还原 DR 卷上最新的快照，并为 DR 卷提供装载点  
- **azure\_hana\_测试\_dr\_failover.pl**：此脚本将测试故障转移执行到 DR 站点。 与 azure_hana_dr_failover.pl 脚本相反，此执行不会中断从主端到辅助端的存储复制。 而会在 DR 端创建复制的存储卷的克隆，并提供克隆卷的装载点。 
- **HANABackupCustomerDetails.txt**：此文件是可修改的配置文件，需要将其进行修改以适应 SAP HANA 配置。 HANABackupCustomerDetails.txt 文件是脚本的控制与配置文件，用于运行存储快照。 根据用途和设置调整该文件。 在部署实例后，应会收到 Azure 上的 SAP HANA 服务管理部门提供的**存储备份名称**和**存储 IP 地址**。 不能修改此文件中任何变量的顺序、排序或空格。 否则，脚本不会正常运行。 此外，还会收到 Azure 上的 SAP HANA 服务管理部门提供的纵向扩展节点或主节点（如果是横向扩展）的 IP 地址。 还会知道在安装 SAP HANA 期间获取的 HANA 实例编号。 现在，需要将备份名称添加到配置文件。

对于纵向扩展或横向扩展部署，在填写 HANA 大型实例单元的服务器名称和服务器的 IP 地址后，配置文件如以下示例所示。 在使用 SAP HANA 系统复制的情况下，请使用 HANA 系统复制配置的虚拟 IP 地址。 填写想要备份或恢复的每个 SAP HANA SID 的所有必填字段。 还可以标注出一段时间内不希望备份的实例行，方法是在所需的字段前添加“#”。 如果不需要备份或恢复特定实例，则无需输入服务器上包含的所有 SAP HANA 实例。 必须保留所有字段的格式，否则所有脚本都将显示错误消息，并且脚本会停止运行。 最后一个 SAP HANA 实例投入使用后，可以删除未使用的任何 SID 信息详情的其他所需行。  必须填写、标注或删除所有行。

>[!IMPORTANT]
>从版本 2.1 到版本 3.0，文件的结构有所更改。 如果想使用 3.0 版本的脚本，则需要调整配置文件结构。 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

对于在 HANA 大型实例单元上配置的每个实例或横向扩展配置，需按以下方式定义数据

    
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
对于横向扩展和 HANA 系统复制配置，建议对每一个节点重复此配置。 此度量值可确保在失败的情况下，备份和最终存储复制仍可以继续工作。   

将所有配置数据填入 HANABackupCustomerDetails.txt 文件后，需要检查 HANA 实例数据的相关配置是否正确。 使用脚本 `testHANAConnection.pl`。 此脚本独立于 SAP HANA 纵向扩展或横向扩展配置。

```
testHANAConnection.pl
```

如果具有 SAP HANA 横向扩展配置，请确保 HANA 主实例有权访问全部所需的 HANA 服务器和实例。 测试脚本中不带参数，但必须将数据添加到 HANABackupCustomerDetails.txt 配置文件的才能使脚本正常运行。 只会返回 shell 命令错误代码，因此脚本无法检查每个特定实例中的错误。 但尽管如此，脚本确实会提供一些有用的注释来帮助用户做仔细检查。

若要运行该脚本，请输入以下命令：
```
 ./testHANAConnection.pl
```
如果脚本成功获取 HANA 实例的状态，会显示 HANA 连接成功的消息。


下一测试步骤是基于放入 HANABackupCustomerDetails.txt 配置文件的数据检查与存储的连接，并执行测试快照。 在执行 `azure_hana_backup.pl` 脚本之前，必须先执行此测试。 如果某个卷不包含任何快照，就无法确定该卷是否为空，或者在获取快照详细信息时是否发生了 SSH 失败。 因此，脚本将执行两个步骤：

- 它验证要执行快照的脚本有权访问租户的存储虚拟机和接口。
- 根据 HANA 实例为每个卷创建一个测试或虚构快照。

因此，HANA 实例包含为参数。 如果执行失败，无法提供与存储连接相关的错误检查。 即使不执行错误检查，该脚本也能提供有用的提示。
执行命令序列以开展此测试：

```
ssh <StorageUserName>@<StorageIP>
```

移交 HANA 大型实例单元时，已提供存储用户名和存储 IP 地址。

第二个步骤是按以下形式运行测试脚本：
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
脚本会尝试使用之前设置步骤中提供的公钥和在 HANABackupCustomerDetails.txt 文件中配置的数据登录到存储。 如果登录成功，会显示以下内容：

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

如果连接到存储控制台时出现问题，输出将如下所示：

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

成功登录到存储虚拟机接口后，脚本会继续执行阶段 #2 并创建测试快照。 此处显示的输出适用于 SAP HANA 的三节点横向扩展配置：

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

在完成所有准备步骤后，可以开始配置实际存储快照的配置。 要计划的脚本会使用 SAP HANA 纵向扩展和横向扩展配置。 备份脚本的定期和常规执行应通过 cron 来计划。 

可以创建实现三种类型的快照备份：
- **HANA**：组合快照备份，其中包含 /hana/data 和 /hana/shared（包含 /usr/sap）的卷的快照通过协调式快照实现。 可从此快照还原单个文件。
- **Logs**：/hana/logbackups 卷的快照备份。 执行此存储快照时不触发任何 HANA 快照。 此存储卷是要包含 SAP HANA 事务日志备份的卷。 SAP HANA 事务日志备份的执行频率更高，以限制日志增长并防止潜在的数据丢失。 可从此快照还原单个文件。 频率不应小于三分钟。
- **Boot**：包含 HANA 大型实例启动逻辑单元号 (LUN) 的卷的快照。 此快照备份仅适用于 HANA 大型实例 I 类型 SKU。 无法从包含启动 LUN 的卷的快照执行单个文件还原。


>[!NOTE]
> 随着 3.0 版本脚本（支持 MCOD 部署）的到来，这三种不同类型快照的调用语法发生了变化。 没有必要再指定实例的 HANA SID。 需确保在配置文件 HANABackupCustomerDetails.txt中配置了一个单元的 SAP HANA 实例。

>[!NOTE]
> 首次执行脚本时，在多 sid 环境中，脚本可能会显示某些意外错误。 只需再次重新运行脚本问题就会得到修复。



使用脚本 azure_hana_backup.pl执行存储快照的新调用语法如下所示：

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

参数的详细信息如下所示： 

- 第一个参数确定相应快照备份类型的特征。 允许的值为 **hana**、**logs** 和 **boot**。 
- 参数 <HANA Large Instance Type> 仅对于启动卷备份是必须的。 有两个有效的值“TypeI”或“TypeII”，具体取决于 HANA 大型实例单元。 若要了解单元的“类型”，请阅读此[文档](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。  
- <snapshot_prefix> 参数是一个快照，或该快照类型的备份标签。 它有两个用途。 第一个用途是指定快照的名称，以便知道这些快照的作用。 第二个用途是让 azure\_hana\_backup.pl 确定该特定标签下保留的存储快照数。 如果计划两个具有相同类型（例如 **hana**）、不同标签的存储快照备份，并定义要对每个备份保留 30 个快照，则最终会有 60 个受影响的存储卷快照。 
- 参数 <snapshot_frequency>  保留供未来发展使用，无任何影响。 我们建议执行该类型日志的备份时立即将其设置为“3 分钟”，并在执行其他备份类型时将其设为“15 分钟”
- 参数 <number of snapshots retained> 通过定义要保留的快照数（拥有相同快照前缀/标签）间接定义快照的保留期。 对于通过 cron 制定的执行计划，此参数十分重要。 如果拥有相同快照前缀的快照数将超出此参数提供的编号，在执行新的存储快照之前，将删除时间最早的快照。

对于横向扩展，该脚本会执行一些附加检查，确保可以访问所有 HANA 服务器。 该脚本还会在创建 SAP HANA 快照之前，检查所有 HANA 实例是否返回实例的相应状态。 执行 SAP HANA 快照之后，执行存储快照。

执行脚本 `azure_hana_backup.pl` 会在以下三个不同的阶段创建存储快照：

1. 执行 SAP HANA 快照
2. 执行存储快照
3. 删除已创建的 SAP HANA 快照，然后再执行存储快照

若要执行该脚本，请从脚本复制到的 HDB 可执行文件文件夹调用该脚本。 

保留期根据执行脚本时作为参数提交的快照数管理。 存储快照消耗的时间量由两个因素决定：执行时间长短，以及执行脚本时作为参数提供的快照数。 如果保留的快照数超过了脚本调用中指定为参数的数字，则执行新快照之前，将删除同一标签中时间最早的存储快照。 作为最后一个调用参数指定的数字是可用于控制保留的快照数的数字。 通过该数字还可间接控制用于快照的磁盘空间。 

> [!NOTE]
>更改标签后，将立即重新开始计数。 这意味着需要严格处理标签，以免意外删除快照。

### <a name="snapshot-strategies"></a>快照策略
不同类型快照的频率取决于是否使用 HANA 大型实例灾难恢复功能。 HANA 大型实例的灾难恢复功能依赖于存储快照。 依赖于存储帐户这个特点可能需要实施某些与存储快照频率和执行时间长短相关的特殊建议。 

以下注意事项和建议假设不使用 HANA 大型实例提供的灾难恢复功能。 假设用户使用存储快照来获取备份并提供过去 30 天的时点恢复。 鉴于快照数和空间的限制，客户考虑了以下要求：

- 时间点恢复的恢复时间。
- 使用的空间。
- 潜在灾难恢复的恢复点目标和恢复时间目标。
- 针对磁盘最终执行的 HANA 完整数据库备份。 每当针对磁盘执行完整数据库备份或执行 **backint** 接口时，存储快照的执行会失败。 如果打算基于存储快照执行完整数据库备份，请确保在此期间已禁用存储快照执行。
- 每个卷的快照数限制为 255。


对于不使用 HANA 大型实例灾难恢复功能的客户，快照频率会低一些。 在这类情况下，客户以 12 小时或 24 小时的周期对 /hana/data 和 /hana/shared（包括 /usr/sap）执行组合快照，并将这些快照保留一个月。 对日志备份卷的快照也执行相同操作。 但是，日志备份卷的 SAP HANA 事务日志备份按 5 - 15 分钟的周期执行。

建议使用 cron 执行计划的存储快照。 此外，建议使用同一个脚本来满足所有备份和灾难恢复的需求。 修改脚本输入，使之与请求的不同备份时间匹配。 可以根据这些快照的执行时间，在 cron 中做好上述一切计划：每小时、12 小时、每天或每周。 

/etc/crontab 中的 cron 计划示例如下所示：
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
上述示例中有一个每小时执行一次的组合快照，该快照的实施对象包括含 /hana/data 和 /hana/shared（包括 /usr/sap）位置的卷。 此类型的快照用于快速实现过去两天内的时点恢复。 此外，将对这些卷执行每日快照。 因此，会执行两天的每时快照和四周的每日快照。 此外，事务日志备份卷每天备份一次。 这些备份将保留四周。 从 crontab 第三行可知，计划每五分钟执行一次 HANA 事务日志备份。 执行存储快照的不同 cron 作业的启动时间是错开的，因此不会在某个时间点同时执行所有这些快照。 

在以下示例中，将每小时执行一次组合快照，该快照涵盖包含 /hana/data 和 /hana/shared（包括 /usr/sap）位置的卷。 这些快照会保留两天。 事务日志备份卷的快照每五分钟执行一次，并保留四个小时。 与之前一样，HANA 事务日志文件的备份计划为每五分钟执行一次。 启动事务日志备份后，执行事务日志备份卷的快照时会存在两分钟的延迟。 一般情况下，SAP HANA 事务日志备份应在这两分钟内完成。 与之前一样，包含启动 LUN 的卷通过存储快照每天备份一次，且会保留四周。

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

下图演示了上述示例的序列（不包括启动 LUN）：

![备份和快照之间的关系](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 对 /hana/log 卷执行常规写入，将提交的更改记录到数据库。 SAP HANA 将保存点定期写入 /hana/data 卷。 根据 crontab 中所指定，SAP HANA 事务日志备份每五分钟执行一次。 由于对 /hana/data 和 /hana/shared 卷触发组合存储快照，因此还会每小时执行一次 SAP HANA 快照。 成功执行 HANA 快照后，会执行组合存储快照。 如 crontab 中所指示，在 HANA 事务日志备份后两分钟左右，/hana/logbackup 卷上的存储快照每五分钟执行一次。

> [!NOTE]
>如果在 HANA 系统复制设置的两个节点上计划存储快照备份，需要确保两个节点之间的快照备份执行不重叠。 SAP HANA 具有限制，一次只能处理一个 HANA 快照。 由于 HANA 快照是成功存储快照备份的基本组件，需确保主节点和辅助节点以及最终的第三节点上的存储快照及时相互分离。


>[!IMPORTANT]
> 仅当与 SAP HANA 事务日志备份配合执行存储快照时，才能针对 SAP HANA 备份使用存储快照。 这些事务日志备份需要能够涵盖执行存储快照的间隔时间段。 

如果向用户承诺时间点恢复目标为 30 天，则需要满足以下条件：

- 在极端情况下，需要能够访问针对 /hana/data 和 /hana/shared 且保留期限已达 30 天的组合存储快照。
- 创建保留时长长于任何两个组合存储快照间隔时间的连续事务日志备份。 因此事务日志备份卷快照的最长保留时长需为 30 天。 如果将事务日志备份复制到位于 Azure 存储的另一个 NFS 共享，则不存在这种情况。 在这种情况下，可以从该 NFS 共享拉取旧事务日志备份。

若要从存储快照和事务日志备份的最终存储复制中获益，需要更改 SAP HANA 写入事务日志备份的位置。 可在 HANA Studio 中进行此更改。 虽然 SAP HANA 会自动备份完整日志段，但仍应指定确定的日志备份间隔时间。 使用灾难恢复选项时尤其如此，因为通常需要以确定性的周期执行日志备份。 在以下案例中，日志备份间隔设为 15 分钟。

![在 SAP HANA Studio 中计划 SAP HANA 备份日志](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

可以选择频率高于 15 分钟的备份。 较低的频率设置通常与 HANA 大型实例的灾难恢复功能结合使用。 某些客户每五分钟执行一次事务日志备份。  

如果从未备份过数据库，则最后一步是执行基于文件的数据库备份，以创建必须在备份目录中存在的单个备份项。 否则，SAP HANA 无法启动指定的日志备份。

![创建基于文件的备份以创建单个备份项](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


成功执行第一个存储快照后，还可删除在步骤 6 中执行的测试快照。 为此，请运行脚本 `removeTestStorageSnapshot.pl`：
```
./removeTestStorageSnapshot.pl <hana instance>
```

脚本的输出如下所示：
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

- `du –sh .snapshot`：提供快照目录中所有快照的总数。
- `du –sh --max-depth=1`：列出 **.snapshot** 文件夹中保存的所有快照，以及每个快照的大小。
- `du –hc`：提供所有快照占用的总大小。

使用以下命令可确保创建和存储的快照不会耗尽卷上的所有存储。

>[!NOTE]
>使用上述命令时，不显示启动 LUN 的快照。

### <a name="getting-details-of-snapshots"></a>获取有关快照的详细信息
若要获取有关快照的更多详细信息，也可以使用脚本 `azure_hana_snapshot_details.pl`。 如果灾难恢复位置处存在活动服务器，则可以在任一位置运行此脚本。 此脚本根据每个包含快照的卷提供以下细分输出： 
   * 一个卷中全部快照的大小。
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）

此脚本的执行语法如下所示：

```
./azure_hana_snapshot_details.pl 
```

由于脚本会尝试检索 HANA 备份 ID，因此需要连接到 SAP HANA 实例。 此连接需要正确设置 HANABackupCustomerDetails.txt 配置文件。 卷中两个快照的输出如下所示：

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


### <a name="file-level-restore-from-a-storage-snapshot"></a>从存储快照实现文件级还原
对于 hana 和 logs 快照类型，可以直接在 **.snapshot** 目录中的卷上访问快照。 每个快照的有一个子目录。 应能够将执行了快照的每个文件以文件在快照瞬间的状态从该子目录复制到实际目录结构。

>[!NOTE]
>单个文件还原不适用于独立于 HANA 大型实例单元类型的启动 LUN 的快照。 **.snapshot** 目录不在启动 LUN 中公开。 


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
 > 仅当存在超过一小时的快照时，此脚本才能减少快照数。 该脚本不会删除不超过一小时的快照。 这些限制与提供的可选灾难恢复功能相关。

如果不再想要保留一组包含特定备份标签（如语法示例中的 **hanadaily**）的快照，可以使用 **0** 作为保留数量来执行该脚本。 使用该保留参数会删除与该标签匹配的所有快照。 但是，删除所有快照可能会影响 HANA 大型实例灾难恢复的功能。

第二种删除特定快照的方法是使用脚本 `azure_hana_snapshot_delete.pl`。 此脚本旨在使用在 HANA Studio 中找到的 HANA 备份 ID 或通过快照名称本身删除快照或一组快照。 目前，备份 ID 仅与为 **hana** 快照类型创建的快照绑定。 **logs** 和 **boot** 类型的快照备份不执行 SAP HANA 快照。 因此，这些快照没有可供查找的备份 ID。 如果输入快照名称，将在不同卷上查找所有与输入的快照名称匹配的快照。 要调用该脚本，需指定 HANA 实例的 SID。 该脚本的调用语法是：

```
./azure_hana_snapshot_delete.pl <SID>

```

需要以 **root** 用户身份执行脚本。

如果选中一个快照，可单独删除每个快照。 首先提供包含快照的卷，然后提供快照名称。 如果快照存在于该卷且存在时间超过一小时，则会删除该快照。 可以通过执行 `azure_hana_snapshot_details` 脚本查找卷名和快照名称。 

>[!IMPORTANT]
>如果有数据仅存在于要删除的快照上，则执行删除操作会永久丢失数据。

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>恢复到最近的 HANA 快照

如果遇到生产中断的情况，可向 Microsoft Azure 支持发起客户事件，启动从存储快照恢复的过程。 这是由于在生产系统中删除了数据造成的高度紧急问题，这时，检索数据的唯一办法就是还原生产数据库。

在不同的情况下，时间点恢复可能不太紧急，因此可以提前几天规划。 可与 Azure 上的 SAP HANA 服务管理部门合作规划这种恢复，而不要提出高优先级问题。 例如，计划通过应用新的增强包升级 SAP 软件。 然后又需要还原到升级增强包之前的快照状态。

发送请求之前，需要做好准备。 然后，Azure 上的 SAP HANA 服务管理团队便可以处理请求，提供还原的卷。 接下来，需要基于快照还原 HANA 数据库。 下面说明了如何准好请求准备：

>[!NOTE]
>根据所用的 SAP HANA 版本，用户界面可能与下面所示的屏幕截图不同。

1. 确定要还原的快照。 除非另有说明，否则只能还原 hana/data 卷。 

2. 关闭 HANA 实例。

 ![关闭 HANA 实例](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 卸载每个 HANA 数据库节点上的数据卷。 如果仍将数据卷载入操作系统，快照还原会失败。
 ![卸载每个 HANA 数据库节点上的数据卷](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. 提出 Azure 支持请求，指示要还原特定的快照。

 - 还原期间：Azure 上的 SAP HANA 服务管理部门可能要求用户参加电话会议以进行协调、验证并确认还原的是正确的存储快照。 

 - 还原后：Azure 上的 SAP HANA 服务管理部门会告知存储快照已还原。

5. 完成还原过程后，请重新装载所有数据卷。

 ![重新装载所有数据卷](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. 在 SAP HANA Studio 中选择恢复选项（如果在通过 SAP HANA Studio 重新连接到 HANA DB 时未自动显示）。 以下示例演示如何还原到最后一个 HANA 快照。 存储快照中嵌入了一个 HANA 快照。 如果要还原到最近的存储快照，该快照应是最近的 HANA 快照。 （如果要还原到较旧的存储快照，需要根据存储快照的创建时间找到 HANA 快照。）

 ![在 SAP HANA Studio 中选择恢复选项](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. 选择“将数据库恢复到特定的数据备份或存储快照”。

 ![“指定恢复类型”窗口](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. 选择“指定不包含目录的备份”。

 ![“指定备份位置”窗口](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. 在“目标类型”列表中，选择“快照”。

 ![“指定要恢复的备份”窗口](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. 选择“完成”启动恢复过程。

 ![选择“完成”启动恢复过程](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA 数据库已还原并恢复到存储快照包含的 HANA 快照。

 ![HANA 数据库已还原并恢复到 HANA 快照](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>恢复到最新状态

以下过程将还原存储快照中包含的 HANA 快照。 然后，将事务日志备份还原到还原存储快照之前的最新数据库状态。

>[!IMPORTANT]
>在继续下一步之前，请确保具备完整且连续的事务日志备份链。 如果没有这些备份，则无法还原数据库的当前状态。

1. 完成[恢复到最近的 HANA 快照](#recovering-to-the-most-recent-hana-snapshot)中的步骤 1-6。

2. 选择“将数据库恢复到其最近状态”。

 ![选择“将数据库恢复到其最近状态”](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 指定最新 HANA 日志备份的位置。 该位置需要包含从创建 HANA 快照开始，到处于最新状态为止的所有 HANA 事务日志备份。

 ![指定最新 HANA 日志备份的位置](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. 选择一个备份用作恢复数据库的基础。 在本示例中，屏幕截图中的 HANA 快照为存储快照中包含的 HANA 快照。 

 ![选择一个备份用作恢复数据库的基础](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. 如果增量备份在创建 HANA 快照之后、处于最近状态之前不存在，请清除“使用增量备份”复选框。

 ![如果不存在增量备份，则清除“使用增量备份”复选框](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 在摘要屏幕上选择“完成”启动还原过程。

 ![在摘要屏幕上单击“完成”](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>恢复到另一个时间点
若要恢复到 HANA 快照（包含在存储快照中）与晚于 HANA 快照时间点恢复之间的时间点，请执行以下步骤：

1. 确保提供从创建 HANA 快照开始，到要恢复到的时间为止的所有事务日志备份。
2. 开始执行[恢复到最新状态](#recovering-to-the-most-recent-state)下的过程。
3. 执行步骤 2 时，请在“指定恢复类型”窗口中选择“将数据库恢复到以下时间点”，指定时间点。 然后完成步骤 3-6。

### <a name="monitoring-the-execution-of-snapshots"></a>监视快照执行

使用 HANA 大型实例的存储快照时，还需要监视这些存储快照的执行。 执行存储快照的脚本会将输出写入某个文件，并将其保存在 Perl 脚本所在的同一位置。 会针对每个存储快照单独写入一个文件。 每个文件的输出明确显示了快照脚本执行的各个阶段：

1. 查找需要创建快照的卷。
2. 查找基于这些卷创建的快照。
3. 根据指定的快照数量删除最终的现有快照。
4. 创建 SAP HANA 快照。
5. 基于卷创建存储快照。
6. 删除 SAP HANA 快照。
7. 将最新的快照重命名为 **.0**。

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


## <a name="disaster-recovery-principles"></a>灾难恢复原则
HANA 大型实例提供不同 Azure 区域中 HANA 大型实例戳之间的灾难恢复功能。 例如，如果在 Azure 美国西部区域部署 HANA 大型实例单元，则可将美国东部区域中的 HANA 大型实例单元用作灾难恢复单元。 如前所述，不会自动配置灾难恢复，因为用户需要为 DR 区域中的另一个 HANA 大型实例单元付费。 灾难恢复设置适用于纵向扩展和横向扩展设置。 

在目前已部署的方案中，客户使用 DR 区域中的单元运行非生产系统，这些系统使用已安装的 HANA 实例。 HANA 大型实例单元的 SKU 应与用于生产目的的 SKU 相同。 Azure 生产区域和灾难恢复区域中的服务器单元之间的磁盘配置如下所示：

![从磁盘角度的 DR 设置配置](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

如此概览图中所示，需要订购另一组磁盘卷。 目标磁盘卷的大小与灾难恢复单元中用于生产实例的生产卷大小相同。 这些磁盘卷与灾难恢复站点中的 HANA 大型实例服务器单元相关联。 以下卷从生产区域复制到 DR 站点：

- /hana/data
- /hana/logbackups 
- /hana/shared（包括 /usr/sap）

由于从 /hana/log 卷还原的方式不需要 SAP HANA 事务日志，因此不会复制这些卷。 

灾难恢复功能的提供基于 HANA 大型实例基础结构提供的存储复制功能。 存储卷发生更改时，在存储端使用的功能并不是以异步方式复制的恒定更改流。 它是依赖于以下事实的机制：定期创建这些卷的快照。 然后，已复制的快照和尚未复制的新快照之间的差量会转移到灾难恢复站点中的目标磁盘卷。  这些快照存储在卷上；灾难恢复故障转移时，需要在这些卷上还原这些快照。  

卷中完整数据的首次传输应发生在数据量小于快照之间的差量之前。 因此，DR 站点中的卷包含在生产站点中执行的每个卷的快照。 这样便可以最终使用该 DR 系统回到较早的状态，从而复原丢失的数据而无需回滚生产系统。

如果在一个 HANA 大型实例单元上有多个独立的 SAP HANA 实例时采用 MCOD 部署，应将所有的 SAP HANA 实例存储复制到 DR 端。

在生产站点中将 HANA 系统复制作为高可用性功能使用时，将仅复制第 2 层（或副本）实例的卷。 如果保留或移除次要副本（第 2 层）服务器单元或此单元中的 SAP HANA 实例，此配置可能导致在将存储复制到 DR 站点时出现延迟。 


>[!IMPORTANT]
>对于多层 HANA 系统复制，当使用 HANA 大型实例灾难恢复功能时，关闭第 2 层 HANA 实例或服务器单元会中断到灾难恢复站点的复制。


>[!NOTE]
>HANA 大型实例存储复制功能是镜像和复制存储快照。 因此，如果不按照本文档备份部分中介绍的方式执行存储快照，则无法复制到灾难恢复站点。 执行存储快照是将存储复制到灾难恢复站点的先决条件。



## <a name="preparation-of-the-disaster-recovery-scenario"></a>灾难恢复方案的准备
假设在生产 Azure 区域中的 HANA 大型实例上运行了一个生产系统。 对于以下文档，我们假设该 HANA 系统的 SID 为“PRD”。 另外，假设在 DR Azure 区域中运行的 HANA 大型实例上运行了一个非生产系统。 对于文档，假设该系统的 SID 为“TST”。 配置如下所示：

![DR 设置开始](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

如果尚未订购包含附加存储卷集的服务器实例，Azure 上的 SAP HANA 服务管理部门会将附加卷集作为生产副本的目标附加到运行 TST HANA 实例的 HANA 大型实例单元。 为此，需要提供生产 HANA 实例的 SID。 在 Azure 上的 SAP HANA 服务管理部门确认已附加这些卷后，需要将这些卷装载到 HANA 大型实例单元。

![DR 设置后续步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

下一步是在 DR Azure 区域中运行 TST HANA 实例的 HANA 大型实例单元上安装第二个 SAP HANA 实例。 新安装的 SAP HANA 实例需有相同的 SID。 创建的用户需要有与生产实例相同的 UID 和组 ID。 如果安装成功，需要：

- 执行文档中之前的存储快照准备步骤 #2
- 如果之前未执行该步骤，请为 HANA 大型实例单元的 DR 单元创建公钥。 文档中之前的存储快照准备步骤 #3 介绍了该过程
- 使用新的 HANA 实例维护 HANABackupCustomerDetails.txt 并测试是否可正确连接到存储。  
- 在 DR Azure 区域中的 HANA 大型实例单元上停止新安装的 SAP HANA 实例。
- 卸载这些 PRD 卷并联系 Azure 上的 SAP HANA 服务管理部门。 卷无法一直保持在单元上，因为它们在作为存储复制目标运行时无法对其进行访问。  

![建立复制之前的 DR 设置步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

运营团队会在生产 Azure 区域中的 PRD 卷与 DR Azure 区域中的 PRD 卷之间建立复制关系。

>[!IMPORTANT]
>不会复制 /hana/log 卷，因为在灾难恢复站点中将复制的 SAP HANA 数据库还原到一致状态时不需要该卷。

下一步是设置或调整存储快照备份计划，以便在发生灾难时实现 RTO 和 RPO。 若要将恢复点目标最小化，请在 HANA 大型实例服务中设置以下复制间隔：
- 组合快照（快照类型为 **hana**）涵盖的卷每隔 15 分钟复制到灾难恢复站点中的等效存储卷目标。
- 事务日志备份卷（类型快照为 **logs**）每隔 3 分钟复制到灾难恢复站点中的等效存储卷目标。

若要将恢复点目标最小化，请使用以下设置：
- 每隔 30 分钟至 1 小时执行一次 **hana** 类型存储快照（请参阅“步骤 7：执行快照”）。
- 每隔 5 分钟执行一次 SAP HANA 事务日志备份。
- 每隔 5-15 分钟执行一次 **logs** 类型存储快照。 使用此间隔周期，RPO 应能达到大约 15-25 分钟。

如果使用此设置，事务日志备份序列、存储快照以及 HANA 事务日志备份卷、/hana/data 和 /hana/shared（包括 /usr/sap）的复制可能如下图中的数据所示：

 ![事务日志备份快照和时间轴上的快照镜像之间的关系](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

若要在灾难恢复方案中实现更好的 RPO，可将 Azure 上的 SAP HANA（大型实例）中的 HANA 事务日志备份复制到其他 Azure 区域。 若要进一步降低 RPO，请执行以下大致步骤：

1. 以尽可能高的频率将 HANA 事务日志备份到 /hana/logbackups。
2. 使用 rsync 将事务日志备份复制到 NFS 共享托管的 Azure 虚拟机上。 这些 VM 位于 Azure 生产区域和 DR 区域中的 Azure 虚拟网络中。 需要将这两个 Azure 虚拟网络都连接到线路，该线路将 HANA 生产大型实例连接到 Azure。 请参阅[使用 HANA 大型实例实现灾难恢复的网络注意事项](#Network-considerations-for-disaster-recovery-with-HANA-Large-Instances)部分中的插图。 
3. 在该区域中附加到 NFS 导出存储的 VM 上保留事务日志备份。
4. 在灾难故障转移方案中，使用在灾难恢复站点中 NFS 共享上较新创建的事务日志备份去补充在 /hana/logbackups 卷上找到的事务日志备份。 
5. 现在，能够对可保存到 DR 区域的最新备份执行事务日志备份还原。

当 HANA 大型实例操作确认正在设置复制关系并启动执行存储快照备份时，即表示已开始复制数据。

![建立复制之前的 DR 设置步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

在复制过程中，不会还原 DR Azure 区域中的 PRD 卷上的快照。 只会存储这些快照。 如果以这种状态装载卷，则呈现在 DR Azure 区域中的服务器单元上安装 PRD SAP HANA 实例后卸载这些卷时的状态。 此外，这些卷还会呈现尚未还原的存储备份。

在故障转移时，还可以选择还原到旧的存储快照而不是最新的存储快照。

## <a name="disaster-recovery-failover-procedure"></a>灾难恢复故障转移过程
故障转移到 DR 站点时，有两种不同的情况需要考虑：

- 需要将 SAP HANA 数据库返回到最新状态的数据。 在这种情况下，使用自助服务脚本可以执行故障转移，无需联系 Microsoft。 尽管故障回复时需要与 Microsoft 合作。
- 若希望还原不是最新复制快照的存储快照，则需与 Microsoft 合作。 

>[!NOTE]
>下面的流程步骤需要在表示 DR 单元的 HANA 大型实例单元上执行。 
 
如需还原到最新的复制存储快照，大致步骤如下所示： 

1. 由于在 HANA 大型实例的灾难恢复单元上运行了一个 HANA 非生产实例，因此需要关闭此实例。 假定已预安装 HANA 休眠生产实例。
2. 确保未运行任何 SAP HANA 进程。 使用以下命令执行此项检查：`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`。 输出应显示 **hdbdaemon** 进程处于停止状态且没有其他 HANA 进程处于运行或已开始状态。
3. 在 DR 站点 HANA 大型实例单元上, 执行脚本 azure_hana_dr_failover.pl。 脚本要求还原 SAP HANA SID。 请求脚本时，键入一个或唯一一个已复制并保存在 DR 站点中的 HANA 大型实例单元上 HANABackupCustomerDetails.txt 文件中的 SAP HANA SID。 如果希望故障转移多个 SAP HANA 实例，需要多次运行脚本，并 对希望故障转移和还原的 SAP HANA SID 中的请求类型运行该脚本。 完成时，脚本会显示添加到 HANA 大型实例单元的卷的装载点列表。 此列表还包括已还原的 DR 卷
4. 通过 Linux 操作系统命令，将已还原的灾难恢复卷装载到灾难恢复站点中的 HANA 大型实例单元。 
6. 启动目前处于休眠状态的 SAP HANA 生产实例。
7. 如果此外还选择复制事务日志备份以缩短 RPO 时间，则需要将这些事务日志备份合并到新装载的 DR /hana/logbackups 目录。 不要覆盖现有备份。 只复制尚未使用最新存储快照复制内容复制的较新备份。
8. 还可以还原已复制到 DR Azure 区域中 /hana/shared/PRD 卷的快照中的单个文件。 

也可测试 DR 故障转移，而不会影响实际的复制关系。 若要执行测试故障转移，请执行上文列出的步骤 1 和 2。 步骤 3 将有所变化。

>[!IMPORTANT]
>使用下文介绍的脚本测试故障转移期间，可能未在 DR 站点中创建的实例上运行任何生产事务。 下面介绍的命令会创建一组卷，这些卷与主站点没有任何关系。 结果是无法同步回主站点。 

故障转移测试的步骤 #3 如下所示：

在 DR 站点 HANA 大型实例单元上, 执行脚本 azure_hana_test_dr_failover.pl。 此脚本不会停止主站点和 DR 站点之间的复制关系。 相反，此脚本会克隆 DR 存储卷。 克隆过程成功之后，克隆的卷将还原到最新快照的状态，然后装载到 DR 单元。 脚本要求还原 SAP HANA SID。 键入一个或唯一一个已复制并保存在 DR 站点中的 HANA 大型实例单元上 HANABackupCustomerDetails.txt 文件中的 SAP HANA SID。 如果希望测试多个 SAP HANA 实例，需要多次运行脚本，并 对希望测试故障转移的 SAP HANA SID 中的请求类型运行该脚本。 完成时，脚本会显示添加到 HANA 大型实例单元的卷的装载点列表。 此列表还包括已克隆的 DR 卷。

然后继续执行上述过程的步骤 4 - 8。

如果需要故障转移到 DR 站点，以修复一些几小时前删除的数据，并因此需要将 DR 卷设置为早于最新快照的状态，此过程适用。 

1. 由于在 HANA 大型实例的灾难恢复单元上运行了一个 HANA 非生产实例，因此需要关闭此实例。 假定已预安装 HANA 休眠生产实例。
2. 确保未运行任何 SAP HANA 进程。 使用以下命令执行此项检查：`/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`。 输出应显示 **hdbdaemon** 进程处于停止状态且没有其他 HANA 进程处于运行或已开始状态。
3. 确定希望将灾难恢复站点还原到的快照名称或 SAP HANA 备份 ID。 在真实的灾难恢复案例中，此快照通常是最新的快照。 如果需要恢复丢失的数据，请选择更早的快照。
4. 通过高优先级支持请求联系 Azure 支持部门，并请求在 DR 站点上还原该快照（快照的名称和日期）或请求提供 HANA 备份 ID。 默认情况下，运营团队只会还原 /hana/data 卷。 如果还想要还原 / hana/logbackups 卷，需要特意提出此要求。 建议不要还原 /hana/shared 卷。 应在重新装载 PRD 的 /hana/shared 卷之后挑选特定的文件，例如，**.snapshot** 及其子目录中的 global.ini。 将在操作端执行以下步骤：a. 停止生产卷到灾难恢复卷的快照复制。 如果生产站点服务中断是需要执行灾难恢复过程的原因，则此中断可能已经发生。
    b. 在灾难恢复卷上还原所选的存储快照或具有备份 ID 的快照。
    c. 还原后，灾难恢复卷可装载到灾难恢复区域中的 HANA 大型实例单元。
5. 将灾难恢复卷装载到灾难恢复站点中的 HANA 大型实例单元。 
6. 启动目前处于休眠状态的 SAP HANA 生产实例。
7. 如果此外还选择复制事务日志备份以缩短 RPO 时间，则需要将这些事务日志备份合并到新装载的 DR /hana/logbackups 目录。 不要覆盖现有备份。 只复制尚未使用最新存储快照复制内容复制的较新备份。
8. 还可以还原已复制到 DR Azure 区域中 /hana/shared/PRD 卷的快照中的单个文件。

接下来的一系列步骤包括基于还原的存储快照和可用的事务日志备份恢复 SAP HANA 生产实例。 步骤如下所示：

1. 使用 SAP HANA Studio 将备份位置更改为 **/hana/logbackups**。
   ![更改 DR 恢复的备份位置](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA 会逐个扫描备份文件位置并建议要还原到的最新事务日志备份。 扫描需要花费几分钟时间，扫描完成后会显示如下所示的屏幕：![DR 恢复的事务日志备份列表](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. 调整一些默认设置：

      - 清除“使用增量备份”。
      - 选择“初始化日志区域”。

   ![设置“初始化日志区域”](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. 选择“完成”。

   ![完成 DR 还原](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

应显示如此处所示的进度窗口。 请记住，此为 SAP HANA 三节点横向扩展配置的灾难恢复还原示例。

![还原进度](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

如果还原过程一直停留在“完成”屏幕且不显示进度屏幕，请确认辅助角色节点上的所有 SAP HANA 实例是否正常运行。 如有必要，请手动启动 SAP HANA 实例。


### <a name="failback-from-dr-to-a-production-site"></a>从 DR 站点故障回复到生产站点
可以从 DR 站点故障回复到生产站点。 让我们探讨以下案例：到灾难恢复站点的故障转移是由生产 Azure 区域中的问题引起，而不是因为需要恢复丢失的数据。 用户已在灾难恢复站点中运行了 SAP 生产工作负荷一段时间。 随着生产站点中问题的解决，需要故障回复到生产站点。 由于不能丢失数据，因此回到生产站点这一过程涉及到几个步骤且需要与 Azure 上的 SAP HANA 运营团队进行紧密协作。 问题解决后，需要联系运营团队，以便开始同步回到生产站点。

步骤顺序如下所示：

1. Azure 上的 SAP HANA 运营团队获取从灾难恢复存储卷同步生产存储卷（现表示生产状态）的触发指令。 在此状态中，生产站点中的 HANA 大型实例单元处于关闭状态。
2. Azure 上的 SAP HANA 运营团队监视复制并确保在通知用户前实现跟进
3. 需要关闭在灾难恢复站点使用 HANA 生产实例的应用程序。 然后执行 HANA 事务日志备份。 接下来，停止灾难恢复站点中 HANA 大型实例单元上运行的 HANA 实例。
4. 关闭灾难恢复站点中 HANA 大型实例单元上运行的 HANA 实例后，运营团队再次手动同步磁盘卷。
5. Azure 上的 SAP HANA 运营团队再次启动生产站点中的 HANA 大型实例单元，并将其交接于你。 请确保在 HANA 大型实例单元启动时，SAP HANA 实例处于关闭状态。
6. 执行相同的数据库还原步骤，与之前故障转移到灾难恢复站点时一样。

### <a name="monitoring-disaster-recovery-replication"></a>监视灾难恢复复制

可通过执行脚本 `azure_hana_replication_status.pl` 监视存储复制的进度状态。 必须从灾难恢复位置中运行的单元运行此脚本。 否则它不会按预期运行。 无论复制是否处于活动状态，此脚本都会正常运行。 可针对灾难恢复位置中租户的每个 HANA 大型实例单元运行此脚本。 不能使用此脚本获取有关启动卷的详细信息。

调用如下所示的脚本：
```
./replication_status.pl <HANA SID>
```

输出按卷细分为以下部分：  

- 链接状态
- 当前复制活动
- 最新复制的快照 
- 最新快照的大小
- 当前快照之间的迟延时间 -- 上一个完成的快照复制与当前复制  

链接状态显示为“活动”，除非位置之间的链接关闭或当前正在进行故障转移事件。 复制活动会处理当前是否正在复制任何数据、数据是否处于空闲状态或该链接是否发生其他活动的问题。 上一个复制的快照应仅显示为 `snapmirror…`。 然后将显示上一个快照的大小。 最后显示迟延时间。 迟延时间表示从计划的复制时间到复制结束之间的时间。 对于数据复制，迟延时间可能超过一小时，尤其是初始复制（尽管复制已开始）。 迟延时间将继续延长，直到完成正在进行的复制。

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












