---
title: 关于 Azure 文件共享备份
description: 了解如何在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396281"
---
# <a name="about-azure-file-share-backup"></a>关于 Azure 文件共享备份

Azure 文件共享备份是一种基于云的本机备份解决方案，可保护云中的数据，并消除本地备份解决方案中涉及的其他维护开销。 Azure 备份服务与 Azure 文件同步顺利集成，并允许您集中文件共享数据以及备份。 此简单、可靠和安全的解决方案使您能够通过几个简单步骤配置企业文件共享的保护，并保证在发生任何灾难时可以恢复数据。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 文件共享备份的主要优势

* 零基础结构：无需部署即可配置文件共享的保护。
* 内置管理功能：您可以计划备份并指定所需的保留期，而无需增加数据修剪的开销。
* 即时还原：Azure 文件共享备份使用文件共享快照，因此只能选择要立即还原的文件。
* 警报和报告：您可以为备份和还原失败配置警报，并使用 Azure 备份提供的报告解决方案来获取有关文件共享备份的见解。

## <a name="architecture"></a>体系结构

![Azure 文件共享备份体系结构](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>备份过程的工作原理

1. 配置 Azure 文件共享备份的第一步是创建恢复服务保管库。 保管库为您提供了跨不同工作负载配置的备份的整合视图。

2. 创建保管库后，Azure 备份服务将发现可以向保管库注册的存储帐户。 您可以选择托管要保护的文件共享的存储帐户。

3. 选择存储帐户后，Azure 备份服务将列出存储帐户中存在的文件共享集，并将它们的名称存储在管理层目录中。

4. 然后根据您的要求配置备份策略（计划和保留），然后选择要备份的文件共享。 Azure 备份服务在控制平面中注册计划以执行计划备份。

5. 根据指定的策略，Azure 备份计划程序在计划的时间触发备份。 作为该作业的一部分，使用文件共享 API 创建文件共享快照。 只有快照 URL 存储在元数据存储中。

    >[!NOTE]
    >文件共享数据不会传输到备份服务，因为备份服务创建和管理属于存储帐户的快照。

6. 可以从源文件共享上可用的快照还原 Azure 文件共享内容（单个文件或完整共享）。 触发操作后，将从元数据存储中检索快照 URL，并将数据从源快照列出并传输到您选择的目标文件共享。

7. 备份和还原作业监视数据推送到 Azure 备份监视服务。 这允许您在单个仪表板中监视文件共享的云备份。 此外，您还可以在备份运行状况受到影响时配置警报或电子邮件通知。 电子邮件通过 Azure 电子邮件服务发送。

## <a name="backup-costs"></a>备份成本

Azure 文件共享备份是基于快照的解决方案，根据[此处](https://azure.microsoft.com/pricing/details/storage/files/)提到的定价详细信息，快照产生的存储费用与 Azure 文件使用情况一起计费。

但是，利用备份解决方案的受保护实例费用根据["为 Azure 文件备份"](https://azure.microsoft.com/pricing/details/backup/)部分中描述的定价模型。 目前，实际价格已仅针对美国中西部进行了更新。 对于其他地区，确切的价格将很快更新与一些区域变化，但使用相同的定价模型。

>[!NOTE]
>在预览期间，没有"受保护的实例费用"，您将仅根据[此处](https://azure.microsoft.com/pricing/details/storage/files/)提到的定价收取快照费用。

## <a name="next-steps"></a>后续步骤

* 了解如何备份[Azure 文件共享](backup-afs.md)
* 查找[有关备份 Azure 文件问题的答案](backup-azure-files-faq.md)
