---
title: 关于 Azure 文件共享备份
description: 了解如何在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 0e17b05a3febaa673fb29d45c2bcef25e2996df8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78386715"
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

## <a name="next-steps"></a>后续步骤

* 了解如何备份[Azure 文件共享](backup-afs.md)
* 查找[有关备份 Azure 文件问题的答案](backup-azure-files-faq.md)
