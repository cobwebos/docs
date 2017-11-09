---
title: "Azure 虚拟机上的 SAP HANA 备份指南 | Microsoft Docs"
description: "SAP HANA 备份指南介绍适用于 Azure 虚拟机上的 SAP HANA 的两种可行的主要备份方法。"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9e5b124643b753f404ba6012d3df998f567be59a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA 备份指南

## <a name="getting-started"></a>入门

适用于 Azure 虚拟机上运行的 SAP HANA 的备份指南只介绍特定于 Azure 的主题。 有关常规 SAP HANA 备份相关的项目，请查看 SAP HANA 文档（参阅本文稍后的 _SAP HANA 备份文档_）。

本文重点介绍适用于 Azure 虚拟机上的 SAP HANA 的两种可行的主要备份方法：

- 将 HANA 备份到 Azure Linux 虚拟机中的文件系统（参阅[文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)）
- 使用 Azure 存储 Blob 快照功能以手动方式或者使用 Azure 备份服务进行基于存储快照的 HANA 备份（参阅[基于存储快照的 SAP HANA 备份](sap-hana-backup-storage-snapshots.md)）

SAP HANA 提供一个备份 API，第三方备份工具可以借助此 API 来直接与 SAP HANA 集成。 （这不在本指南的介绍范畴内。）目前，无法基于此 API 将 SAP HANA 直接与 Azure 备份服务集成。

Azure VM GS5 类型正式支持将 SAP HANA 用作单一实例，但会对 OLAP 工作负荷实施更多的限制（参阅 SAP 网站上的 [Find Certified IaaS Platforms](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)（查找认证的 IaaS 平台））。 随着 Azure 上的 SAP HANA 新产品的推出，本文将不断更新。

Azure 上还提供了一个 SAP HANA 混合解决方案，其中的 SAP HANA 以非虚拟化的方式在物理服务器上运行。 但是，本 SAP HANA Azure 备份指南只涉及单纯的 Azure 环境，其中的 SAP HANA 在 Azure VM 中运行，而不是在&quot;大型实例&quot;上运行。有关在&quot;大型实例&quot;上基于存储快照使用此备份解决方案的详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）概述和体系结构](hana-overview-architecture.md)。

有关 Azure 支持的 SAP 产品的一般信息，请参阅 [SAP 说明 1928533](https://launchpad.support.sap.com/#/notes/1928533)。

以下三幅插图概述了目前可以使用本机 Azure 功能进行 SAP HANA 备份的选项，此外还显示了将来可能会实现的三种备份方案。 相关文章[文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)和[基于存储快照的 SAP HANA 备份](sap-hana-backup-storage-snapshots.md)更详细介绍了这些选项，包括进行 TB 量级的 SAP HANA 备份时需要注意的有关大小和性能的事项。

![此图显示了保存当前 VM 状态的两种可行方法](media/sap-hana-backup-guide/image001.png)

此图显示了通过 Azure 备份服务或 VM 磁盘手动快照保存当前 VM 状态的可行方法。 如果使用此方法，则无需管理 SAP HANA 备份。 磁盘快照方案的难题在于保持文件系统一致性以及应用程序一致的磁盘状态。 本文稍后的_创建存储快照时保持 SAP HANA 数据一致性_部分介绍有关一致性的主题。 本文稍后还会介绍与 SAP HANA 备份相关的 Azure 备份服务功能和限制。

![此图显示了用于在 VM 中创建 SAP HANA 文件备份的选项](media/sap-hana-backup-guide/image002.png)

此图显示了用于在 VM 中创建 SAP HANA 文件备份，然后使用不同的工具将备份存储在位于其他某个位置的 HANA 备份文件中的选项。 创建 HANA 备份所需的时间比基于快照的备份解决方案要长，但在完整性和一致性方面具有优势。 本文稍后将提供更多详细信息。

![此图显示了将来可能会实现的 SAP HANA 备份方案](media/sap-hana-backup-guide/image003.png)

此图显示了将来可能会实现的 SAP HANA 备份方案。 如果 SAP HANA 允许从复制辅助副本创建备份，则它会添加备份策略的更多选项。 根据 SAP HANA Wiki 中的某个贴子，目前无法做到这一点：

_&quot;是否可以在辅助端创建备份？_

_目前不可以，只能在主端创建数据和日志的备份。如果已启用自动日志备份，则在切换到辅助端后，将在辅助端自动写入日志备份。&quot;_

## <a name="sap-resources-for-hana-backup"></a>用于 SAP HANA 备份的资源

### <a name="sap-hana-backup-documentation"></a>SAP HANA 备份文档

- [SAP HANA 管理简介](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [使用 ABAP DBACOCKPIT 计划 HANA 备份](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [计划数据备份（SAP HANA 考核中心）](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- [SAP 说明 1642148](https://launchpad.support.sap.com/#/notes/1642148) 中有关 SAP HANA 备份的常见问题解答
- [SAP 说明 2039883](https://launchpad.support.sap.com/#/notes/2039883) 中有关 SAP HANA 数据库和存储快照的常见问题解答
- [SAP 说明 1820529](https://launchpad.support.sap.com/#/notes/1820529) 中所述的不适用于备份和恢复的网络文件系统

### <a name="why-sap-hana-backup"></a>为何使用 SAP HANA 备份？

Azure 存储原生提供高可用性和可靠性（有关 Azure 存储的详细信息，请参阅 [Microsoft Azure 存储简介](../../../storage/common/storage-introduction.md)）。

&quot;备份&quot;的最低要求就是必须符合 Azure SLA，将 SAP HANA 数据和日志文件保存在已附加到 SAP HANA 服务器 VM 的 Azure VHD 上。 这种方案涵盖了 VM 故障，但不涵盖 SAP HANA 数据和日志文件可能发生的损坏，也不涵盖无意中删除数据或文件等逻辑错误。 此外，还会出于符合性或法规要求而执行备份。 简单而言，SAP HANA 备份始终是一项需求。

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>如何验证 SAP HANA 备份的正确性
使用存储快照时，我们建议在不同的系统上运行测试还原。 使用这种方案可以确保备份正确，并且内部的备份和还原过程能够按预期方式运行。 尽管这种方案在本地很难实现，但在云中实现却要容易得多，只需暂时性地为此目的提供所需的资源即可。

请注意，执行简单的还原并检查 HANA 是否正常运行并不足够。 理想情况下，应该运行表一致性的检查，确保还原的数据库正常。 SAP HANA 提供 [SAP 说明 1977584](https://launchpad.support.sap.com/#/notes/1977584) 中所述的多种一致性检查。

在 SAP 网站上的 [Table and Catalog Consistency Checks](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)（表和目录一致性检查）中也能找到有关表一致性检查的信息。

对于标准文件备份，不需要执行测试还原。 可以借助两个 SAP HANA 工具来检查哪个备份可用于还原：hdbbackupdiag 和 hdbbackupcheck。 有关这些工具的详细信息，请参阅[手动检查是否可以恢复](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm)。

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA 备份相比存储快照的优点和缺点

SAP 不会在 HANA 备份与存储快照之间做出优先选择， 而是列出各自的优点与缺点，使用户能够根据情况和适用的存储技术确定要使用哪个服务（请参阅[规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)）。

在 Azure 上，请注意这样一个事实：Azure Blob 快照功能无法保证文件系统一致性（请参阅[通过 PowerShell 使用 Blob 快照](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)）。 下一部分_创建存储快照时保持 SAP HANA 数据一致性_介绍了有关此功能的一些注意事项。

此外，必须了解频繁使用 Blob 快照产生的计费影响，如以下文章中所述：[Understanding How Snapshots Accrue Charges](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)（了解快照计费方式）— 与使用 Azure 虚拟磁盘时相比，快照产生的费用不是很明显。

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>创建存储快照时保持 SAP HANA 数据一致性

创建存储快照时，文件系统和应用程序一致性是个复杂的问题。 避免问题的最简单方法是关闭 SAP HANA 甚至整个虚拟机。 可以在演示或原型设计系统甚至开发系统中执行关闭，但在生产系统中请不要这样做。

在 Azure 上，必须注意 Azure Blob 快照功能无法保证文件系统一致性。 但是，使用 SAP HANA 快照功能可以保证这种一致性，前提是只涉及到单个虚拟磁盘。 但即使使用单个磁盘，也必须检查其他项目。 [SAP 说明 2039883](https://launchpad.support.sap.com/#/notes/2039883) 提供了有关通过存储快照创建 SAP HANA 备份的重要信息。 例如，该文档提到，使用 XFS 文件系统时，必须先运行 **xfs\_freeze**，才能启动存储快照，以保证一致性（有关 **xfs\_freeze** 的详细信息，请参阅 [xfs\_freeze(8) - Linux 手册页](https://linux.die.net/man/8/xfs_freeze)）。

如果单个文件系统跨越多个磁盘/卷，一致性问题就会变得更棘手。 例如，需要使用 mdadm 或 LVM 和条带化。 上述 SAP 说明指出：

_&quot;但请注意，存储系统在为每个 SAP HANA 数据卷创建存储快照时必须保证 I/O 一致性，也就是说，为 SAP HANA 服务特定的数据卷创建快照必须是原子操作。&quot;_

假设某个 XFS 文件系统跨越四个 Azure 虚拟磁盘，以下步骤将提供表示 HANA 数据区域的一致快照：

- HANA 快照准备
- 冻结文件系统（例如，使用 **xfs\_freeze**）
- 在 Azure 上创建所有必要的 Blob 快照
- 解冻文件系统
- 确认 HANA 快照

为了稳妥起见，我们建议在所有情况下都使用上述过程，不管文件系统是什么， 也不管它是否是单个磁盘或条带化卷，是否通过 mdadm 或 LVM 跨越多个磁盘。

必须确认 HANA 快照。 由于使用了&quot;写入时复制&quot;，SAP HANA 在处于此快照准备模式时，可能不需要额外的磁盘空间。 此外，只有在确认 SAP HANA 快照后，才能启动新的备份。

Azure 备份服务使用 Azure VM 扩展来处理文件系统一致性。 这些 VM 扩展不可单独使用。 用户仍然必须自行管理 SAP HANA 一致性。 有关详细信息，请参阅相关文章[文件级别的的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)。

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 备份计划策略

SAP HANA 文章[规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)指明了一个用于执行备份的基本计划：

- 存储快照（每日）
- 使用文件或 bacint 格式完成数据备份（每周一次）
- 自动日志备份

（可选）可以完全不使用存储快照；可将这些快照替换为 HANA 增量备份，类似于递增式备份或差异备份（请参阅[增量备份](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)）。

HANA 管理指南提供了示例列表。 建议使用以下备份序列将 SAP HANA 恢复到特定的时间点：

1. 完整数据备份
2. 差异备份
3. 增量备份 1
4. 增量备份 2
5. 日志备份

在特定备份类型的执行时间和执行频率方面，我们无法针对明确的计划提供总体准则 — 这在很大程度上具体于不同的客户，以及系统中发生的数据更改量。 SAP 方面提供的一项基本建议是，每周创建一个完整的 HANA 备份，可将这项建议视为总体准则。
有关日志备份，请参阅 SAP HANA 文档[日志备份](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)。

SAP 还建议针对备份目录执行一些清理，使它能够无限保持增长（请参阅 [Housekeeping for Backup Catalog and Backup Storage](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)（备份目录和备份存储的清理））。

### <a name="sap-hana-configuration-files"></a>SAP HANA 配置文件

如 [SAP 说明 1642148](https://launchpad.support.sap.com/#/notes/1642148) 中的常见问题解答中所述，SAP HANA 配置文件不是标准 HANA 备份的一部分。 还原系统时，这些文件并非必不可少。 还原后，可以手动更改 HANA 配置。 如果在还原过程中想要获取相同的自定义配置，必须单独备份 HANA 配置文件。

如果标准 HANA 备份会转到专用的 HANA 备份文件系统，用户还可以将配置文件复制到相同的备份文件系统，然后连同所有内容一起复制到最终存储目标（例如 Blob 冷存储）。

### <a name="sap-hana-cockpit"></a>SAP HANA 考核中心

在 SAP HANA 考核中心可以通过浏览器监视和管理 SAP HANA。 此外，还可以处理 SAP HANA 备份处理，因此，考核中心可以取代 SAP HANA Studio 和 ABAP DBACOCKPIT（有关详细信息，请参阅 [SAP HANA 考核中心](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm)）。

![此图显示了 SAP HANA 考核中心数据库管理屏幕](media/sap-hana-backup-guide/image004.png)

此图显示了 SAP HANA 考核中心数据库管理屏幕，左侧为备份磁贴。 用于查看备份磁贴的登录帐户需要相应的用户权限。

![备份正在进行时，可在 SAP HANA 考核中心监视备份](media/sap-hana-backup-guide/image005.png)

备份正在进行时，可在 SAP HANA 考核中心监视备份；备份完成后，会显示所有备份详细信息。

![在 Azure SLES 12 VM 上配合 Gnome 桌面使用 Firefox 的示例](media/sap-hana-backup-guide/image006.png)

上面的屏幕截图取自 Azure Windows VM。 这是在 Azure SLES 12 VM 上配合 Gnome 桌面使用 Firefox 的示例。 其中显示了用于在 SAP HANA 考核中心定义 SAP HANA 备份计划的选项。 我们还可以看到，它建议使用日期/时间作为备份文件的前缀。 在 SAP HANA Studio 中，执行完整文件备份时，默认的前缀为 &quot;COMPLETE\_DATA\_BACKUP&quot;。 建议使用唯一的前缀。

### <a name="sap-hana-backup-encryption"></a>SAP HANA 备份加密

SAP HANA 提供数据和日志加密。 如果 SAP HANA 数据和日志未加密，则备份也不会加密。 客户需负责使用某种形式的第三方解决方案来加密 SAP HANA 备份。 有关 SAP HANA 加密的详细信息，请参阅 [Data and Log Volume Encryption](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm)（数据和日志卷加密）。

在 Microsoft Azure 上，客户可以使用 IaaS VM 加密功能来加密。 例如，可以使用已附加到 VM 的、用于存储 SAP HANA 备份的专用数据磁盘，并创建这些磁盘的副本。

Azure 备份服务可以处理加密的 VM/磁盘（请参阅[如何使用 Azure 备份来备份和还原已加密的虚拟机](../../../backup/backup-azure-vms-encryption.md)）。

另一种做法是保留 SAP HANA VM 及其磁盘而不进行加密，并将 SAP HANA 备份文件存储在已启用加密的存储帐户中（请参阅[静态数据的 Azure 存储服务加密](../../../storage/common/storage-service-encryption.md)）。

## <a name="test-setup"></a>测试设置

### <a name="test-virtual-machine-on-azure"></a>在 Azure 上测试虚拟机

我们已使用 Azure GS5 VM 中的 SAP HANA 安装进行以下备份/还原测试。

![此图显示了 HANA 测试 VM 的一部分 Azure 门户概览](media/sap-hana-backup-guide/image007.png)

此图显示了 HANA 测试 VM 的一部分 Azure 门户概览。

### <a name="test-backup-size"></a>测试备份大小

![此图取自 HANA Studio 中的备份控制台，显示 HANA 索引服务器的备份文件大小为 229 GB](media/sap-hana-backup-guide/image008.png)

虚拟表中已填满数据，最终总共有超过 200 GB 的数据备份大小用于派生实际性能数据。 该图取自 HANA Studio 中的备份控制台，显示 HANA 索引服务器的备份文件大小为 229 GB。 测试时，使用了 SAP HANA Studio 中的默认备份前缀“COMPLETE_DATA_BACKUP”。 在实际生产系统中，应该定义更适用的前缀。 SAP HANA 考核中心建议使用日期/时间作为前缀。

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>用于将文件直接复制到 Azure 存储的测试工具

为了将 SAP HANA 备份文件直接传输到 Azure Blob 存储或 Azure 文件共享，我们使用了 blobxfer 工具，因为该工具支持这两个目标，并且可以轻松集成到自动化脚本中（它采用命令行接口）。 可在 [GitHub](https://github.com/Azure/blobxfer) 上获取 blobxfer 工具。

### <a name="test-backup-size-estimation"></a>测试备份大小估算

必须估算 SAP HANA 的备份大小。 由于复制文件时可提供并行度，此估算值可以通过定义多个备份文件的最大备份文件大小来帮助提高性能。 （本文稍后介绍相关的详细信息。）此外，用户必须确定是要执行完整备份还是增量备份（递增式备份或差异备份）。

幸运的是，有一个简单的 SQL 语句可以估算备份文件的大小：**select \* from M\_BACKUP\_SIZE\_ESTIMATIONS**（请参阅 [Estimate the Space Needed in the File System for a Data Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)（估算文件系统中数据备份所需的空间））。

![此 SQL 语句的输出几乎与磁盘上完整数据备份的实际大小完全匹配](media/sap-hana-backup-guide/image009.png)

对于测试系统而言，此 SQL 语句的输出几乎完全与磁盘上完整数据备份的实际大小匹配。

### <a name="test-hana-backup-file-size"></a>测试 HANA 备份文件大小

![使用 HANA Studio 备份控制台可以限制 HANA 备份文件的最大文件大小](media/sap-hana-backup-guide/image010.png)

使用 HANA Studio 备份控制台可以限制 HANA 备份文件的最大文件大小。 在示例环境中，使用该功能可能会导致生成多个较小的备份文件，而不是一个 230-GB 的备份文件。 较小的文件大小会对性能造成明显的影响（请参阅相关文章[文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)）。

## <a name="summary"></a>摘要

下表根据测试结果显示了用于备份 Azure 虚拟机上运行的 SAP HANA 数据库的解决方案的优点和缺点。

**将 SAP HANA 备份到文件系统，然后将备份文件复制到最终备份目标**

|解决方案                                           |优点                                 |缺点                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|在 VM 磁盘上保留 HANA 备份                      |无需附加的管理工作     |会耗尽本地 VM 磁盘空间           |
|用于将备份文件复制到 Blob 存储的 Blobxfer 工具 |利用并行度来复制多个文件，可以选择使用 Blob 冷存储 | 需要执行附加的工具维护和自定义脚本编写 | 
|通过 Powershell 或 CLI 进行 Blob 复制                    |无需使用附加的工具，可通过 Azure Powershell 或 CLI 完成 |手动过程，客户必须负责对用于还原的复制 Blob 编写脚本和进行管理|
|复制到 NFS 共享                                  |在其他 VM 上对备份文件进行后处理，不会影响 HANA 服务器|复制过程缓慢|
|使用 Blobxfer 复制到 Azure 文件服务                |不会耗尽本地 VM 磁盘上的空间|无法使用 HANA 备份提供的直接写和支持，文件共享的大小限制目前为 5 TB|
|Azure 备份代理                                 | 是首选的解决方案         | 目前在 Linux 上不可用    |



**基于存储快照的 SAP HANA 备份**

|解决方案                                           |优点                                 |缺点                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure 备份服务                               | 允许基于 Blob 快照的 VM 备份 | 不使用文件级还原时，需要为还原过程创建新的 VM，这意味着需要新的 SAP HANA 许可证密钥|
|手动 Blob 快照                              | 无需更改唯一的 VM ID，即可灵活创建和还原特定的 VM 磁盘|整个过程必须由客户手动完成|

## <a name="next-steps"></a>后续步骤
* [文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)介绍了基于文件的备份选项。
* [基于存储快照的 SAP HANA 备份](sap-hana-backup-storage-snapshots.md)介绍了基于存储快照的备份选项。
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
