---
title: 关于 Azure 文件共享备份
description: 了解如何在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 0e17b05a3febaa673fb29d45c2bcef25e2996df8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386715"
---
# <a name="about-azure-file-share-backup"></a>关于 Azure 文件共享备份

Azure 文件共享备份是一种基于云的本机备份解决方案，用于保护云中的数据，并消除了本地备份解决方案所涉及的额外维护开销。 Azure 备份服务可与 Azure 文件同步顺畅集成，并使你能够集中处理文件共享数据以及备份。 这种简单、可靠和安全的解决方案使你可以通过几个简单的步骤来配置企业文件共享的保护，并且可以在发生任何灾难情况时恢复数据。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 文件共享备份的主要优点

* 零基础结构：配置对文件共享的保护不需要进行部署。
* 内置的管理功能：你可以计划备份并指定所需的保留期，而无需额外的数据删除开销。
* 即时还原： Azure 文件共享备份使用文件共享快照，因此，你可以只选择想要立即还原的文件。
* 警报和报告：你可以为备份和还原失败配置警报，并使用 Azure 备份提供的报表解决方案来深入了解文件共享中的备份。

## <a name="architecture"></a>体系结构

![Azure 文件共享备份体系结构](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>备份过程的工作方式

1. 为 Azure 文件共享配置备份的第一步是创建恢复服务保管库。 保管库提供跨不同工作负荷配置的备份的合并视图。

2. 创建保管库后，Azure 备份服务会发现可向保管库注册的存储帐户。 你可以选择要保护的文件共享所在的存储帐户。

3. 选择存储帐户后，Azure 备份服务会列出存储帐户中存在的文件共享集，并将其名称存储在管理层目录中。

4. 然后，根据你的要求配置备份策略（计划和保留），并选择要备份的文件共享。 Azure 备份服务在控制平面中注册计划以执行计划的备份。

5. 根据指定的策略，Azure 备份计划程序会在计划的时间触发备份。 在该作业中，文件共享快照是使用文件共享 API 创建的。 仅快照 URL 存储在元数据存储区中。

    >[!NOTE]
    >文件共享数据不会传输到备份服务，因为备份服务创建并管理属于存储帐户的快照。

6. 你可以从源文件共享上可用的快照还原 Azure 文件共享内容（单个文件或完全共享）。 触发该操作后，将从元数据存储中检索快照 URL，并列出数据并从源快照传输到所选的目标文件共享。

7. 备份和还原作业监视数据将推送到 Azure 备份监视服务。 这允许你在单个仪表板中监视文件共享的云备份。 此外，你还可以在备份运行状况受到影响时配置警报或电子邮件通知。 电子邮件通过 Azure 电子邮件服务发送。

## <a name="next-steps"></a>后续步骤

* 了解如何[备份 Azure 文件共享](backup-afs.md)
* 查找[有关备份 Azure 文件的问题](backup-azure-files-faq.md)的答案
