---
title: 关于 Azure 文件共享备份
description: 了解如何在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 40cb9ca0bd34fd65ab1983af6384d617db26e996
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539085"
---
# <a name="about-azure-file-share-backup"></a>关于 Azure 文件共享备份

Azure 文件共享备份是一种基于云的本机备份解决方案，用于保护云中的数据，并消除了本地备份解决方案所涉及的额外维护开销。 Azure 备份服务可与 Azure 文件同步顺畅集成，并使你能够集中处理文件共享数据以及备份。 这种简单、可靠和安全的解决方案使你可以通过几个简单的步骤来配置企业文件共享的保护，并且可以在发生任何灾难情况时恢复数据。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 文件共享备份的主要优点

* **零基础结构**：配置对文件共享的保护不需要进行部署。
* **自定义保留**：可以根据需要配置每日/每周/每月/每年保留的备份。
* **内置的管理功能**：你可以计划备份并指定所需的保留期，而无需额外的数据删除开销。
* **即时还原**： Azure 文件共享备份使用文件共享快照，因此，你可以只选择想要立即还原的文件。
* **警报和报告**：你可以为备份和还原失败配置警报，并使用 Azure 备份提供的报表解决方案来深入了解文件共享中的备份。
* **防止意外删除文件共享**： Azure 备份允许在保留期为14天的存储帐户级别启用[软删除功能](../storage/files/storage-files-prevent-file-share-deletion.md)。 即使恶意执行组件删除文件共享，文件共享的内容和恢复点（快照）也会保留在可配置的保留期内，从而能够成功恢复源内容和快照，而不会丢失数据。

## <a name="architecture"></a>体系结构

![Azure 文件共享备份体系结构](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>备份进程的工作方式

1. 为 Azure 文件共享配置备份的第一步是创建恢复服务保管库。 保管库提供跨不同工作负荷配置的备份的合并视图。

2. 创建保管库后，Azure 备份服务会发现可向保管库注册的存储帐户。 你可以选择要保护的文件共享所在的存储帐户。

3. 选择存储帐户后，Azure 备份服务会列出存储帐户中存在的文件共享集，并将其名称存储在管理层目录中。

4. 然后，根据你的要求配置备份策略（计划和保留），并选择要备份的文件共享。 Azure 备份服务在控制平面中注册计划以执行计划的备份。

5. 根据指定的策略，Azure 备份计划程序会在计划的时间触发备份。 在该作业中，文件共享快照是使用文件共享 API 创建的。 仅快照 URL 存储在元数据存储区中。

    >[!NOTE]
    >文件共享数据不会传输到备份服务，因为备份服务创建并管理属于存储帐户的快照，而不会将备份传输到保管库。

6. 你可以从源文件共享上可用的快照还原 Azure 文件共享内容（单个文件或完全共享）。 触发该操作后，将从元数据存储中检索快照 URL，并列出数据并从源快照传输到所选的目标文件共享。

7. 备份和还原作业监视数据将推送到 Azure 备份监视服务。 这允许你在单个仪表板中监视文件共享的云备份。 此外，你还可以在备份运行状况受到影响时配置警报或电子邮件通知。 电子邮件通过 Azure 电子邮件服务发送。

## <a name="backup-costs"></a>备份成本

目前，你只需要为快照付费，因为 Azure 文件共享备份是基于快照的解决方案。 根据[此处](https://azure.microsoft.com/pricing/details/storage/files/)所述的定价详细信息，按 Azure 文件使用情况对快照产生的存储费用进行计费。

## <a name="next-steps"></a>后续步骤

* 了解如何[备份 Azure 文件共享](backup-afs.md)
* 查找[有关备份 Azure 文件的问题](backup-azure-files-faq.md)的答案
