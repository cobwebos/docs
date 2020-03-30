---
title: Azure 虚拟机上的 SAP HANA 备份指南 | Microsoft Docs
description: SAP HANA 备份指南介绍适用于 Azure 虚拟机上的 SAP HANA 的两种可行的主要备份方法。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: bb32350597059209e5baf01d53b0c59fdc2344f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255221"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA 备份指南

## <a name="getting-started"></a>入门

适用于 Azure 虚拟机上运行的 SAP HANA 的备份指南只介绍特定于 Azure 的主题。 有关常规 SAP HANA 备份相关项目，请查看 SAP HANA 文档。 我们希望您熟悉具有健全有效的备份策略的原则数据库备份策略、原因和动机，并了解贵公司对备份过程、备份保留期和恢复的要求程序。

SAP HANA 受到了各种 Azure VM 类型的官方支持，例如 Azure M 系列。 有关 SAP HANA 认证的 Azure VM 和 HANA 大型实例单元的完整列表，请查看[查找认证 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 Microsoft Azure 提供了许多单元，SAP HANA 在物理服务器上运行非虚拟化。 此服务称为[HANA 大型实例](hana-overview-architecture.md)。 本指南将不介绍 HANA 大型实例的备份过程和工具。 但将仅限于 Azure 虚拟机。 有关使用 HANA 大型实例进行备份/还原进程的详细信息，请阅读文章[HLI 备份和还原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)。

本文的重点是 SAP HANA 在 Azure 虚拟机上的三种备份可能性：

- 通过 Azure[备份服务](https://docs.microsoft.com/azure/backup/backup-overview)进行 HANA 备份 
- 将 HANA 备份到 Azure Linux 虚拟机中的文件系统（参阅[文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)）
- 使用手动 Azure 存储 Blob 快照功能或 Azure 备份服务基于存储快照的 HANA 备份


SAP HANA 提供一个备份 API，第三方备份工具可以借助此 API 来直接与 SAP HANA 集成。 Azure 备份服务或[Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)等产品正在使用此专有接口触发 SAP HANA 数据库或重做日志备份。 


有关如何查找 Azure 上支持的 SAP 软件的信息，请参阅文章["Azure 部署支持哪些 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)"。

## <a name="azure-backup-service"></a>Azure 备份服务

显示的第一个方案是 Azure 备份服务使用 SAP HANA`backint`接口从 SAP HANA 数据库执行流式备份。 或者，使用 Azure 备份服务更通用的功能来创建应用程序一致的磁盘快照，并将该快照传输到 Azure 备份服务。

Azure 备份集成并认证为 SAP HANA 的备份解决方案，使用称为[backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)的专有 SAP HANA 接口。 有关解决方案、其功能及其可用的 Azure 区域的更多详细信息，请阅读有关[在 Azure VM 上备份 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)的文章。 有关 HANA Azure 备份服务的详细信息和原则，请阅读[有关 Azure VM 中的 SAP HANA 数据库备份](https://docs.microsoft.com/azure/backup/sap-hana-db-about)的文章。 

利用 Azure 备份服务的第二种可能性是使用 Azure 高级存储的磁盘快照创建应用程序一致的备份。 其他 HANA 认证的 Azure 存储（如[Azure 超磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)和[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)）不支持通过 Azure 备份服务进行此类快照。 阅读以下文章：

- [在 Azure 中计划 VM 备份基础结构](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Azure Linux VM 的应用程序一致性备份](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) 

出现此序列的活动：

- Azure 备份需要执行预快照脚本，使应用程序（本例中为 SAP HANA）处于一致状态
- 确认此一致状态后，Azure 备份将执行磁盘快照
- 完成快照后，Azure 备份将撤消在快照前脚本中执行的活动
- 成功执行后，Azure 备份会将数据流式传输到备份保管库

对于 SAP HANA，大多数客户对包含 SAP HANA 重做日志的卷使用 Azure 写入加速器。 Azure 备份服务将自动从快照中排除这些卷。 此排除不会损害 HANA 的恢复能力。 尽管它会阻止使用几乎所有其他 SAP 支持的 DBMS 进行还原的能力。

这种可能性的缺点是，您需要开发自己的快照前和快照后脚本。 预快照脚本需要创建 HANA 快照并处理最终异常情况。 而快照后脚本需要再次删除 HANA 快照。 有关所需逻辑的更多详细信息，请从[SAP 支持说明#2039883](https://launchpad.support.sap.com/#/notes/2039883)开始。 本文中"在拍摄存储快照时 SAP HANA 数据一致性"一节的注意事项完全适用于此类备份。

> [!NOTE]
> 在使用多个数据库容器的部署中，SAP HANA 的基于磁盘快照的备份需要至少发布 HANA 2.0 SP04
> 

请参阅本文档后面的有关存储快照的详细信息。

![此图显示了保存当前 VM 状态的两种可行方法](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>其他 HANA 备份方法
还有三种其他备份方法或路径可以考虑：

- 备份基于 Azure NetApp 文件 （ANF） 的 NFS 共享。 ANF 再次能够创建存储备份的卷的快照。 给定您最终编写备份所需的吞吐量，此解决方案可能会成为一种昂贵的方法。 虽然易于建立，因为 HANA 可以直接将备份写入 Azure 本机 NFS 共享
- 针对标准 SSD 或 Azure 高级存储的 VM 连接磁盘执行 HANA 备份。 作为下一步，您可以根据 Azure Blob 存储复制这些备份文件。 这种策略可能是价格明智的吸引力
- 针对标准 SSD 或 Azure 高级存储的 VM 连接磁盘执行 HANA 备份。 作为下一步，磁盘会定期进行快照。 在第一个快照之后，增量快照可用于降低成本

![此图显示了用于在 VM 中创建 SAP HANA 文件备份的选项](media/sap-hana-backup-guide/other-hana-backup-paths.png)

此图显示了用于在 VM 中创建 SAP HANA 文件备份，然后使用不同的工具将备份存储在位于其他某个位置的 HANA 备份文件中的选项。 但是，所有不涉及第三方备份服务或 Azure 备份服务的解决方案都有几个共同点。 其中一些可以列出，例如保留管理、自动还原过程以及提供 Azure 备份服务或其他专用第三方备份套件和服务的自动点恢复。 其中许多第三方服务能够在 Azure 上运行。 


## <a name="sap-resources-for-hana-backup"></a>用于 SAP HANA 备份的资源

### <a name="sap-hana-backup-documentation"></a>SAP HANA 备份文档

- [SAP HANA 管理简介](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [使用 ABAP DBACOCKPIT 计划 HANA 备份](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [计划数据备份（SAP HANA 考核中心）](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- [SAP 说明 1642148](https://launchpad.support.sap.com/#/notes/1642148) 中有关 SAP HANA 备份的常见问题解答
- [SAP 说明 2039883](https://launchpad.support.sap.com/#/notes/2039883) 中有关 SAP HANA 数据库和存储快照的常见问题解答
- [SAP 说明 1820529](https://launchpad.support.sap.com/#/notes/1820529) 中所述的不适用于备份和恢复的网络文件系统

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>如何验证 SAP HANA 备份的正确性
与备份方法无关，针对不同的系统运行测试还原是绝对必要的。 使用这种方案可以确保备份正确，并且内部的备份和还原过程能够按预期方式运行。 虽然还原备份可能是本地的障碍，因为它需要基础结构，但通过暂时为此提供必要的资源，在云中完成起来要容易得多。 正确程度是，HANA 提供了一些工具，可以检查备份文件的还原能力。 但是，频繁恢复练习的目的是测试数据库还原过程，并与操作人员一起训练该进程。

请注意，执行简单的还原并检查 HANA 是否正常运行并不足够。 应运行表一致性检查，以确保还原的数据库正常。 SAP HANA 提供 [SAP 说明 1977584](https://launchpad.support.sap.com/#/notes/1977584) 中所述的多种一致性检查。

在 SAP 网站上的 [Table and Catalog Consistency Checks](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)（表和目录一致性检查）中也能找到有关表一致性检查的信息。

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA 备份相比存储快照的优点和缺点

SAP 不会在 HANA 备份与存储快照之间做出优先选择， 而是列出各自的优点与缺点，使用户能够根据情况和适用的存储技术确定要使用哪个服务（请参阅[规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)）。

在 Azure 上，请注意 Azure blob 快照功能&#39;未跨多个磁盘提供文件系统一致性（请参阅[使用带 PowerShell 的 blob 快照](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)）。 

此外，必须了解频繁使用 Blob 快照产生的计费影响，如以下文章中所述：[Understanding How Snapshots Accrue Charges](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)（了解快照计费方式）— 与使用 Azure 虚拟磁盘时相比，快照产生的费用不是很明显。

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>创建存储快照时保持 SAP HANA 数据一致性

如前所述，在拍摄存储快照时，必须描述 Azure 备份、文件系统和应用程序一致性的快照备份功能。 避免问题的最简单方法是关闭 SAP HANA 甚至整个虚拟机。 对于生产实例来说，是不可行的。

> [!NOTE]
> 在使用多个数据库容器的部署中，SAP HANA 的基于磁盘快照的备份需要至少发布 HANA 2.0 SP04
> 

Azure 存储不会在快照过程中连接到 VM 的多个磁盘或卷中提供文件系统一致性。 这意味着快照期间的应用程序一致性需要由应用程序传递，在这种情况下，SAP HANA 本身。 [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)包含有关 SAP HANA 备份的重要信息（按存储快照进行）。 例如，对于 XFS 文件系统，在启动存储快照之前必须运行**xfs\_冻结**以提供应用程序一致性（有关**xfs\_冻结**的详细信息，请参阅[xfs\_freeze（8） - Linux 人页](https://linux.die.net/man/8/xfs_freeze)。

假设某个 XFS 文件系统跨越四个 Azure 虚拟磁盘，以下步骤将提供表示 HANA 数据区域的一致快照：

1. 创建 HANA 数据快照准备
1. 冻结所有磁盘/卷的文件系统（例如，使用**xfs\_冻结**）
1. 在 Azure 上创建所有必要的 Blob 快照
1. 解冻文件系统
1. 确认 HANA 数据快照（将删除快照）

使用 Azure 备份的功能执行应用程序一致的快照备份时，需要#1步骤进行编码/编写脚本，以便进行快照前脚本。 Azure 备份服务将执行#2和#3步骤。 #4和#5步骤需要由快照后脚本中的代码再次提供。 如果不使用 Azure 备份服务，还需要自行编写代码/脚本步骤#2并#3。
有关创建 HANA 数据快照的详细信息，请参阅以下文章：

- [HANA 数据快照](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- 有关执行步骤#1的详细信息，请参阅[创建数据快照（本机 SQL）](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html)的文章 
- 有关在步骤#5中根据需要确认/删除 HANA 数据快照的详细信息，请参阅[文章"创建数据快照"（本机 SQL） 中](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

必须确认 HANA 快照。 由于使用了&quot;写入时复制&quot;，SAP HANA 在处于此快照准备模式时，可能不需要额外的磁盘空间。 此外，只有在确认 SAP HANA 快照后，才能启动新的备份。


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 备份计划策略

SAP HANA 文章["规划备份和恢复策略](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)"中，提出了执行备份的基本计划。 在定义 SAP HANA 的备份/恢复策略和流程时，依靠有关 HANA 的 SAP 文档以及您与其他 DBMS 的经验。 不同类型的备份顺序和保留期高度依赖于您需要提供的 SL。


### <a name="sap-hana-backup-encryption"></a>SAP HANA 备份加密

SAP HANA 提供数据和日志加密。 如果 SAP HANA 数据和日志未加密，则默认情况下不会加密备份。 但是，SAP HANA 提供了单独的备份加密，如[SAP HANA 备份加密](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)中所述。 如果您正在运行较旧版本的 SAP HANA，则可能需要检查备份加密是否是已提供的功能的一部分。  


## <a name="next-steps"></a>后续步骤
* [文件级别的 SAP HANA Azure 备份](sap-hana-backup-file-level.md)介绍了基于文件的备份选项。
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
