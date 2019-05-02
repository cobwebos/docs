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
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707256"
---
# <a name="backup-and-restore"></a>备份和还原

>[!IMPORTANT]
>本文档不是 SAP HANA 管理文档或 SAP 说明的替代文档。 读者应深入了解 SAP HANA 管理和操作的专业技能，尤其是在备份、还原和高可用性以及灾难恢复主题方面。 本文档中提供 SAP HANA Studio 的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

重要的是使用自己的 HANA 版本或发布，在相应的环境中执行步骤和过程。 本文档中介绍的某些过程进行了简化，以便于理解，不是用作最终操作手册的详细步骤。 如果想针对配置创建操作手册，需要测试并实践相关过程，并记录与特定配置相关的过程。 

在操作数据库方面，一个要点是在发生各种灾难事件时保护数据库。 这些事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。

备份数据库并具备还原到任意时间点（例如，在某人删除关键数据之前的时间点）的能力，可以尽可能地还原到发生中断之前所处的状态。

若要实现此类功能来还原，必须执行两种类型的备份：

- 数据库备份：完整备份、增量备份或差异备份
- 事务日志备份

除了在应用程序级别执行完整数据库备份以外，还可以使用存储快照执行备份。 存储快照不会替代事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 但是，存储快照可以通过快速提供数据库的前滚映像来提升恢复速度。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- 自制 (DIY)。 确定有足够的磁盘空间后，请使用以下磁盘备份方法之一执行完整的数据库和日志备份。 可以直接备份到 HANA 大型实例单元上附加的卷，或者备份到 Azure 虚拟机 (VM) 中设置的网络文件共享 (NFS)。 在后一种情况中，客户在 Azure 中设置 Linux VM、将 Azure 存储附加到 VM 并通过该 VM 中配置的 NFS 服务器共享此存储。 如果对直接附加到 HANA 大型实例单元的卷执行备份，需要将备份复制到 Azure 存储帐户（在设置用于基于 Azure 存储导出 NFS 共享的 Azure VM 之后）。 还可以使用 Azure 备份保管库或 Azure 冷存储。 

   另一个选项是将备份复制到 Azure 存储帐户后，使用第三方数据保护工具来存储备份。 对于出于符合性和审核目的而需要长期存储的数据，还有必要使用 DIY 备份选项。 在所有情况下，备份均复制到通过 VM 和 Azure 存储表示的 NFS 共享中。

- 基础结构备份和还原功能。 还可以使用 Azure 上的 SAP HANA（大型实例）底层基础结构提供的备份和还原功能。 此选项满足备份和快速还原的需要。 本部分的余下内容介绍 HANA 大型实例提供的备份和还原功能。 本部分还会介绍备份和还原功能与 HANA 大型实例提供的灾难恢复功能之间的关系。

> [!NOTE]
>   HANA 大型实例底层基础结构使用的快照技术依赖于 SAP HANA 快照。 目前，SAP HANA 快照无法与 SAP HANA 多租户数据库容器的多个租户配合工作。 如果只部署一个租户，则 SAP HANA 快照可以正常工作，并可以使用此方法。

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>使用 Azure 上的 SAP HANA（大型实例）的存储快照

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 当触发对 /hana/data、和 /hana/shared（包括 /usr/sap）卷的快照时，快照技术将在执行存储快照前启动 SAP HANA 快照。 在恢复存储快照后，此 SAP HANA 快照是最终日志还原的设置点。 对于成功的 HANA 快照，您需要的活动的 HANA 实例。  在 HSR 方案中，不能执行 HANA 快照的当前辅助节点不支持存储快照。
- 成功执行存储快照后，将删除 SAP HANA 快照。
- 事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来创建快照。 在这种情况下，不需要执行 HANA 快照。
- 如果您必须将数据库还原到的某一点时间，请求的 Microsoft Azure 支持 （会造成生产中断） 或 SAP HANA Azure 还原到特定的存储快照。 一个例子就是按计划将沙盒系统还原到其原始状态。
- 存储快照中包含的 SAP HANA 快照是创建存储快照后，应用执行并存储的事务日志备份偏移点。
- 创建这些事务日志备份是为了将数据库还原到某个时间点。

可针对三种类型的卷执行存储快照：

- 基于 /hana/data 和 /hana/shared（包括 /usr/sap）创建组合快照。 此快照需要创建 SAP HANA 快照，为存储快照进行准备工作。 从存储点的角度来看，SAP HANA 快照可确保数据库处于一致状态。 这对于还原过程来说就是在一个点上进行设置。
- 基于 /hana/logbackups 创建单独的快照。
- 操作系统分区。

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 获取最新的快照脚本和文档。 在下载中的快照脚本包[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)，您会获得三个文件的其中一个是提供的功能的 PDF 文档。 请确保你请继续执行此过程一章中的指令获取快照工具时下载该工具集。

## <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照使用分配到 HANA 大型实例单元的存储空间。 需要从计划存储快照和保存的存储快照数量角度考虑以下方面。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 特定的存储快照（在最初创建时）几乎不消耗存储空间。
- 随着数据内容以及存储卷上 SAP HANA 数据文件内容的不断更改，快照需要存储原始块内容以及数据更改。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 执行这些卷的快照会消耗卷空间。 您需要确定何时计划存储快照。 此外，还需要监视存储卷的空间消耗，并管理存储的快照数。 将大量数据导入 HANA 数据库或对其执行其他重大更改时，可以禁用存储快照。 


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
1. 将脚本和配置文件从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 复制到 SAP HANA 安装中的 **hdbsql** 位置。
1. 必要时，根据相应的客户规范修改 *HANABackupDetails.txt* 文件。

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 获取最新的快照脚本和文档。 有关以上所列的详细步骤，请参阅[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>MCOD 方案的注意事项
如果对一个 HANA 大型实例单元上的多个 SAP HANA 实例运行 [MCOD 场景](https://launchpad.support.sap.com/#/notes/1681092)，将获得为每个 SAP HANA 实例预配的单独存储卷。 有关 MDC 和其他注意事项的详细信息，请[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章**重要事项需记住**。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步骤 1：安装 SAP HANA HDB 客户端

Azure 上的 SAP HANA（大型实例）上安装的 Linux 操作系统包含所需的文件夹和脚本用于针对备份和灾难恢复目的执行 SAP HANA 存储快照。 在 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 中查找最新版本。 脚本的最新版本为 4.0。 相同的主要版本中，不同的脚本可能具有不同的次要发行版本。

客户需要负责在安装 SAP HANA 时在 HANA 大型实例单元上安装 SAP HANA HDB 客户端。

### <a name="step-2-change-the-etcsshsshconfig"></a>步骤 2：更改 /etc/ssh/ssh\_config

此步骤中所述检查中的最新版本中的详细信息[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章中**启用与存储的通信**


### <a name="step-3-create-a-public-key"></a>步骤 3：创建公钥

为能够访问 HANA 大型实例租户的存储快照接口，需要通过公钥建立登录过程。 在租户中的第一台 Azure 上的 SAP HANA（大型实例）服务器上，创建一个公钥用于访问存储基础结构。 公钥可以确保无需使用密码登录到存储快照接口。 创建公钥也意味着不需要保留密码凭据。 确切步骤如何生成公共密钥所述[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章中**启用与存储的通信**


### <a name="step-4-create-an-sap-hana-user-account"></a>步骤 4：创建 SAP HANA 用户帐户

为了启动 SAP HANA 快照的创建，需要在 SAP HANA 中创建一个可供存储快照脚本使用的用户帐户。 在 SAP HANA Studio 中创建用于此目的的 SAP HANA 用户帐户。 必须在 SYSTEMDB 下创建用户，而不是在适用于 MDC 的 SID 数据库下创建。 在单个容器环境中，在租户数据库中创建用户。 此帐户必须拥有以下权限：“备份管理”和“目录读取”。 有关设置用户并使用用户的确切步骤，请阅读[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0)一章中**启用与 SAP HANA 的通信**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步骤 5：为 SAP HANA 用户帐户授权

在此步骤中，向创建的 SAP HANA 用户帐户授权，以便脚本无需在运行时提交密码。 使用 SAP HANA 命令 `hdbuserstore` 可以创建存储在一个或多个 SAP HANA 节点上的 SAP HANA 用户密钥。 用户可以使用用户密钥从脚本流程内部访问 SAP HANA，而无需管理密码。 本文稍后会介绍脚本过程。

>[!IMPORTANT]
>使用相同的用户上下文中执行快照命令运行这些配置命令。 否则，将快照命令不能正常工作。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步骤 6：获取快照脚本、配置快照及测试配置和连接

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) 下载最新版本的脚本。 版本 4.0 中的脚本，脚本将安装的方式已更改 majorly。 有关确切详细信息，请阅读[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)一章中**启用与 SAP HANA 的通信**

有关具体命令顺序，阅读一章**的快照工具 （默认值） 的简单安装**的文档[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 我们建议在默认安装的使用情况。 如果你想要从版本升级 3.x 到 4.0 中，检查的部分**升级现有安装**的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 用于卸载 4.0 工具集，请按照中的说明**的快照工具卸载'** 中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

别忘了要执行的步骤中所述**的快照工具完成安装**的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

不同脚本和文件的用途有安装已列出并详述**什么是这些快照工具？** 文档的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

配置快照工具之前, 请确保您也 HANA 备份位置和设置正确配置中所述**SAP HANA 配置**文档的[Microsoft 的 SAP HANA 快照工具在 Azure 上](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

中详细介绍了快照工具集配置**配置文件-HANABackupCustomerDetails.txt'** 的文档[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

#### <a name="testing-connectivity-with-sap-hana"></a>测试与 SAP HANA 的连接

将所有配置数据填入 *HANABackupCustomerDetails.txt* 文件后，需要检查 HANA 实例数据的相关配置是否正确。 使用脚本独立于 SAP HANA 纵向扩展或横向扩展配置的脚本 `testHANAConnection`。

有关详细信息，请参阅**检查与 SAP HANA-testHANAConnection 的连接**文档的[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>测试存储的连接

下一测试步骤是基于放入 *HANABackupCustomerDetails.txt* 配置文件的数据检查与存储的连接，并执行测试快照。 在执行之前`azure_hana_backup`命令时，必须运行此测试。 用于此测试的命令序列被列入**检查与存储空间-testStorageSnapshotConnection 的连接**的文档[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

成功登录到存储虚拟机接口后，脚本会继续执行阶段 2 并创建测试快照。 此处显示的输出适用于 SAP HANA 的三节点横向扩展配置：

如果已使用脚本成功执行测试快照，则可以继续计划实际存储快照。 如果不成功，请先调查问题，然后继续。 测试快照应保留至第一批实际快照完成为止。


### <a name="step-7-perform-snapshots"></a>步骤 7：执行快照

完成准备步骤后，你可以开始配置和计划的实际存储快照。 要计划的脚本会使用 SAP HANA 纵向扩展和横向扩展配置。 备份脚本的定期和常规执行应使用 cron 实用工具来计划。 

有关的确切命令语法和功能，请阅读**执行快照备份-azure_hana_backup'** 的文档[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。  

执行脚本 `azure_hana_backup` 会在以下三个阶段创建存储快照：

1. 执行 SAP HANA 快照
1. 执行存储快照
1. 删除已创建的 SAP HANA 快照，然后再执行存储快照

若要执行该脚本，请从脚本复制到的 HDB 可执行文件文件夹调用该脚本。 

保留期根据执行脚本时作为参数提交的快照数管理。 存储快照消耗的时间量由以下因素决定：执行时间长短，以及执行脚本时作为参数提供的快照数。 如果保留的快照数超过了脚本调用中指定为参数的数字，则执行新快照之前，将删除同一标签中时间最早的存储快照。 作为最后一个调用参数指定的数字是可用于控制保留的快照数的数字。 通过该数字还可间接控制用于快照的磁盘空间。 


## <a name="snapshot-strategies"></a>快照策略
不同类型快照的频率取决于是否使用 HANA 大型实例灾难恢复功能。 此功能依赖于存储快照，这可能需要实施某些与存储快照频率和执行时间长短相关的特殊建议。 

以下注意事项和建议假设不使用 HANA 大型实例提供的灾难恢复功能。 假设用户使用存储快照来获取备份并提供过去 30 天的时点恢复。 鉴于快照数和空间的限制，客户考虑了以下要求：

- 时间点恢复的恢复时间。
- 使用的空间。
- 潜在灾难恢复的恢复点和恢复时间目标。
- 针对磁盘最终执行的 HANA 完整数据库备份。 每当针对磁盘执行完整数据库备份或执行 **backint** 接口时，存储快照的执行会失败。 如果打算基于存储快照执行完整数据库备份，请确保在此期间已禁用存储快照执行。
- 每个卷的快照数（限制为 250）。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

对于不使用 HANA 大型实例灾难恢复功能的客户，快照频率会低一些。 在这类情况下，客户以 12 小时或 24 小时的周期对 /hana/data 和 /hana/shared（包括 /usr/sap）执行组合快照，并将这些快照保留一个月。 对日志备份卷的快照也执行相同操作。 但是，日志备份卷的 SAP HANA 事务日志备份的执行发生在 5 到 15 分钟时段。

最好是使用 cron 执行计划的存储快照。 使用同一个脚本来满足所有备份和灾难恢复的需求，并修改脚本输入，使之与请求的不同备份时间匹配。 可以根据这些快照的执行时间，在 cron 中做好上述一切计划：每小时、12 小时、每天或每周。 

下面是 /etc/crontab 中的 cron 计划的示例：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
在上一示例中，是每小时组合快照，该快照包含的 hana/数据卷和 /hana/shared/SID （包括/usr/sap） 的位置。 使用此类型的快照快速实现过去两天内的时点恢复。 此外，将对这些卷执行每日快照。 因此，会执行两天的每时快照和四周的每日快照。 此外，事务日志备份卷每天备份一次。 这些备份将保留四周。 从 crontab 第三行可知，计划每 5 分钟执行一次 HANA 事务日志备份。 执行存储快照的不同 cron 作业的启动时间是错开的，因此不会在某个时间点同时执行所有这些快照。 

在以下示例中，执行包含的 hana/数据和 /hana/shared/SID （包括/usr/sap） 的卷创建组合的快照每小时的基础上的位置。 这些快照会保留两天。 事务日志备份卷的快照每 5 分钟执行一次，并保留 4 个小时。 与之前一样，HANA 事务日志文件的备份计划为每 5 分钟执行一次。 启动事务日志备份后，执行事务日志备份卷的快照时会存在 2 分钟的延迟。 一般情况下，SAP HANA 事务日志备份应在这 2 分钟内完成。 与之前一样，包含启动 LUN 的卷通过存储快照每天备份一次，且会保留四周。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下图演示了上述示例的序列（不包括启动 LUN）：

![备份和快照之间的关系](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 对 /hana/log 卷执行常规写入，将提交的更改记录到数据库。 SAP HANA 将保存点定期写入 /hana/data 卷。 根据 crontab 中所指定，SAP HANA 事务日志备份每 5 分钟执行一次。 你还可以看到，执行一次 SAP HANA 快照是 /hana/data 和 /hana/shared/SID 卷触发组合的存储快照每隔一小时。 成功执行 HANA 快照后，会执行组合存储快照。 如 crontab 中所指示，在 HANA 事务日志备份后 2 分钟左右，/hana/logbackup 卷上的存储快照每 5 分钟执行一次。

> 

>[!IMPORTANT]
> 仅当与 SAP HANA 事务日志备份配合执行存储快照时，才能针对 SAP HANA 备份使用存储快照。 这些事务日志备份需要能够涵盖执行存储快照的间隔时间段。 

如果向用户承诺时间点恢复目标为 30 天，则需要：

- 在极端情况下，访问的组合的存储快照对 /hana/data、 和 30 天的 /hana/shared/SID。
- 创建保留时长长于任何两个组合存储快照间隔时间的连续事务日志备份。 因此事务日志备份卷快照的最长保留时长需为 30 天。 如果将事务日志备份复制到位于 Azure 存储的另一个 NFS 共享，则不存在这种情况。 在这种情况下，可以从该 NFS 共享拉取旧事务日志备份。

若要从存储快照和事务日志备份的最终存储复制中获益，需要更改 SAP HANA 写入事务日志备份的位置。 可在 HANA Studio 中进行此更改。 虽然 SAP HANA 会自动备份完整日志段，但仍应指定确定的日志备份间隔时间。 使用灾难恢复选项时尤其如此，因为通常需要以确定性的周期执行日志备份。 在以下案例中，日志备份间隔设为 15 分钟。

![在 SAP HANA Studio 中计划 SAP HANA 备份日志](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

还可以选择频率高于 15 分钟的备份。 较高的频率设置通常与 HANA 大型实例的灾难恢复功能结合使用。 某些客户每 5 分钟执行一次事务日志备份。  

如果从未备份过数据库，则最后一步是执行基于文件的数据库备份，以创建必须在备份目录中存在的单个备份项。 否则，SAP HANA 无法启动指定的日志备份。

![创建基于文件的备份以创建单个备份项](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


第一个成功存储快照执行完毕后，你需要删除在步骤 6 中执行的测试快照。 读取**删除测试快照-removeTestStorageSnapshot'** 文档中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)有关详细信息。 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>监视磁盘卷上的快照数量和大小

在特定的存储卷上，可以监视快照的数量以及这些快照的存储消耗量。 `ls` 命令不会显示快照目录或文件。 但是，Linux OS 命令 `du` 显示有关这些存储快照的详细信息（因为这些快照存储在相同的卷上）。 此命令适用以下选项：

- `du –sh .snapshot`：此选项提供快照目录中所有快照的总数。
- `du –sh --max-depth=1`：此选项列出 .snapshot 文件夹中保存的所有快照，以及每个快照的大小。
- `du –hc`：此选项提供所有快照占用的总大小。

使用以下命令可确保创建和存储的快照不会耗尽卷上的所有存储。

>[!NOTE]
>使用上述命令时，不显示启动 LUN 的快照。

### <a name="getting-details-of-snapshots"></a>获取有关快照的详细信息
若要获取有关快照的更多详细信息，也可以使用脚本 `azure_hana_snapshot_details`。 如果灾难恢复位置处存在活动服务器，则可以在任一位置运行此脚本。 此脚本根据每个包含快照的卷提供以下细分输出： 
   * 一个卷中全部快照的大小
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）

有关命令和输出检查的语法**列出快照-azure_hana_snapshot_details'** 文档中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>减少服务器上的快照数

如前所述，可以减少存储的带有特定标签的快照数。 用于启动快照的命令的最后两个参数是要保留的快照的标签与数量。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在上面的示例中，快照标签为“dailyhana”，要保留的带有此标签的快照数为 28。 对磁盘空间消耗量做出响应时，可能想要减少存储的快照数。 例如，如果希望将快照数减少至 15，实现此操作的简单方法是将脚本的最后一个参数设置为 **15** 并运行该脚本：

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果使用此设置运行脚本，快照数（包括新存储快照）为 15。 会保存最新的 15 个快照，同时删除 15 个较旧的快照。

 >[!NOTE]
 > 仅当存在超过 1 小时的快照时，此脚本才能减少快照数。 该脚本不会删除不超过 1 小时的快照。 这些限制与提供的可选灾难恢复功能相关。

如果不再想要保留一组包含备份的前缀的快照**dailyhana**语法示例中可以执行具有脚本**0**作为保留数量。 然后删除所有与该标签匹配的快照。 但是，删除所有快照可能会影响 HANA 大型实例灾难恢复的功能。

第二种删除特定快照的方法是使用脚本 `azure_hana_snapshot_delete`。 此脚本旨在使用在 HANA Studio 中找到的 HANA 备份 ID 或通过快照名称本身删除快照或一组快照。 目前，备份 ID 仅与为 **hana** 快照类型创建的快照绑定。 **logs** 和 **boot** 类型的快照备份不执行 SAP HANA 快照，因此，这些快照没有可供查找的备份 ID。 如果输入快照名称，将在不同卷上查找所有与输入的快照名称匹配的快照。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

有关脚本的详细信息，请参阅 **'删除快照-azure_hana_snapshot_delete'** 文档中[Microsoft Azure 上的 SAP HANA 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)。

需要以 **root** 用户身份执行脚本。

>[!IMPORTANT]
>如果有数据仅存在于要删除的快照上，则删除快照后，会永久丢失数据。

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>从存储快照实现文件级还原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
对于 **hana** 和 **logs** 快照类型，可以直接在 **.snapshot** 目录中的卷上访问快照。 每个快照的有一个子目录。 可将执行了快照的每个文件以文件在快照瞬间的状态从该子目录复制到实际目录结构。 在脚本的最新版本，没有**否**还原 （尽管可以执行快照还原，如自助服务 DR 的一部分编写的脚本在 DR 站点故障转移期间） 提供快照还原为自助服务脚本。 必须通过打开服务请求联系 Microsoft 操作团队，要求从现有可用快照还原所需快照。

>[!NOTE]
>单个文件还原不适用于独立于 HANA 大型实例单元类型的启动 LUN 的快照。 **.snapshot** 目录不在启动 LUN 中公开。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>恢复到最近的 HANA 快照

如果遇到生产中断的情况，可向 Microsoft Azure 支持发起客户事件，启动从存储快照恢复的过程。 这是由于在生产系统中删除了数据造成的高度紧急问题，这时，检索数据的唯一办法就是还原生产数据库。

在不同的情况下，时间点恢复可能不太紧急，因此可以提前几天规划。 可以在 Azure 上规划与 SAP HANA 此恢复，而不要提出高优先级标志。 例如，计划通过应用新的增强包升级 SAP 软件。 然后又需要还原到升级增强包之前的快照状态。

发送请求之前，需要做好准备。 然后，Azure 团队上的 SAP HANA 可以处理该请求，并提供已还原的卷。 接下来，需要基于快照还原 HANA 数据库。

一节中描述的可能性来获取快照还原具有新的工具集**如何还原快照**的文档[手动恢复指南为 Azure 上 SAP HANA 存储快照从](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。

下面说明如何做好请求准备：

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



用于获取，例如，从存储快照恢复的 SAP HANA 数据文件的另一种可能记录在文档的步骤 7[手动恢复指南为 Azure 上 SAP HANA 存储快照从](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)。

文档[手动恢复指南为 Azure 上 SAP HANA 存储快照从](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)说明了从快照备份还原顺序。 还原执行使用该文档。 

>[!Note]
>不执行如果拥有在快照还原由 Microsoft 操作所需步骤 7。


### <a name="recover-to-another-point-in-time"></a>恢复到另一个时间点
文档[手动恢复指南为 Azure 上 SAP HANA 存储快照从](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)部分中的时间说明到特定的时间点还原顺序**将数据库恢复到以下点在时间中'**. 还原到某个点在时间中执行使用该文档。 


## <a name="next-steps"></a>后续步骤
- 请参阅[灾难恢复原则和准备](hana-concept-preparation.md)。
