---
title: 迁移中使用的自愿迁移工具的 Azure Monitor 经典警报
description: 了解如何使用自愿迁移工具迁移经典警报规则。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 58c664beee942fe7115c7fff38a039c23bed6ac3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346008"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自愿迁移工具迁移经典警报规则

作为[之前公布](monitoring-classic-retirement.md)，在 Azure Monitor 经典警报将被停用在 2019 年 7 月中。 迁移工具来自动触发迁移可在 Azure 门户中，并向使用经典警报规则的客户推出。 本文将引导你完成如何使用迁移工具将自动迁移开始在 2019 年 7 月之前自愿迁移经典警报规则。

## <a name="benefits-of-new-alerts"></a>新警报的优点

要通过 Azure Monitor 中新统一警报替换经典警报。 新的警报平台具有以下优势：

- 在多个多维指标的警报[许多更多的 Azure 服务](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)
- 新指标警报支持[多资源的警报规则](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)这极大地减少管理多个规则的开销。
- 统一的通知机制
  - [操作组](action-groups.md)是适用于 （指标、 日志和活动日志） 的所有新警报类型的模块化通知机制
  - 您还将能够充分利用新的通知机制，如 SMS、 语音和 ITSM 连接器
- [统一的警报体验](alerts-overview.md)上不同的信号将所有警报 (指标、 活动日志和日志) 在一个位置

## <a name="before-you-migrate"></a>在迁移之前

作为迁移的一部分，经典警报规则转换为等效的新警报规则和创建操作组。

- 通知有效负载格式，以及 Api 来创建和管理新的警报规则是不同的经典警报规则，因为它们支持更多的功能。 了解[如何为迁移准备](alerts-prepare-migration.md)。

- 不能使用工具迁移一些经典警报规则。 [了解哪些规则不是可迁移，请参阅如何将其迁移](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated)。

    > [!NOTE]
    > 迁移过程不会影响经典警报规则的评估。 它们将继续运行并发送警报，直到它们被迁移，新的警报规则开始评估。


## <a name="how-to-use-the-migration-tool"></a>如何使用迁移工具

以下过程描述如何在触发经典警报规则在 Azure 门户中的迁移：

1. 在 [Azure 门户](https://portal.azure.com)中单击“监视”。

2. 单击**警报**然后单击**管理警报规则**或**查看经典警报**。

3. 单击**迁移到新的规则**转到迁移登陆页。 此页显示所有订阅和为其迁移的状态的列表。

    ![迁移登陆](media/alerts-migration/migration-landing.png "迁移规则")

4. 可以使用工具迁移的所有订阅将被都标记为**准备好迁移**。

    > [!NOTE]
    > 迁移工具即将推出阶段到使用经典警报规则的所有订阅。 在推出的早期阶段，可能会看到一些其他订阅中的作为未准备好进行迁移。

5. 选择一个或多个订阅，然后单击**预览迁移**

6. 在此页上，可以看到将迁移以供订阅一次的经典警报规则的详细信息。 此外可以**下载此订阅的迁移详细信息**.csv 格式。

    ![迁移-preview](media/alerts-migration/migration-preview.png "预览迁移")

7. 提供一个或多个**电子邮件地址**的迁移状态通知。 在迁移完成，或从你需要操作时，我们将发送一封电子邮件。

8. 单击**开始迁移**。 读取在确认对话框中显示的信息并确认是否已准备好开始迁移过程。

    >[!IMPORTANT]
    > 一旦启动订阅的迁移过程，您将不能编辑/创建订阅的经典警报规则。 但是，经典警报规则将继续运行并向用户提供警报，直到它们都迁移。 这是为了确保经典警报规则以及在迁移过程中创建的新规则之间的保真度。 完成迁移后为你的订阅，您不能再使用经典警报规则。

    ![迁移确认](media/alerts-migration/migration-confirm.png "确认开始迁移")

9. 为我们完成迁移，或者需要从你的操作，您将收到一封电子邮件在步骤 8 中提供的电子邮件地址。 可以也会定期检查从门户中迁移登录页面的状态。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-is-my-subscriptions-listed-as-not-ready-for-migration"></a>**为什么我的订阅列为未准备好进行迁移？**

迁移工具推出阶段中于所有客户。 在早期阶段，大多数或所有订阅可能被标记为**尚未做好迁移**。 但是，中旬年 4 月，所有订阅都应准备好迁移。

在订阅处于准备好进行迁移，订阅所有者将收到电子邮件通知的工具的可用性。 请密切关注此通知。

### <a name="who-can-trigger-the-migration"></a>**谁可以触发迁移？**

具有在订阅级别分配给他们的监视参与者角色的用户将能够触发迁移。 详细了解如何[Role Based Access Control 的迁移过程](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-is-the-migration-going-to-take"></a>**多长时间迁移需要？**

对于大多数订阅，迁移完成此过程通常在一小时。 您可以跟踪的迁移进度，从迁移登录页面。  在此期间，请确保在经典警报系统或新建一个中，警报仍在运行。

### <a name="what-can-i-do-if-i-run-into-an-issue-during-migration"></a>**如果我会在迁移期间遇到问题，我可以做什么？**

请按照[故障排除指南，以了解在迁移期间可能会遇到任何问题的修正步骤](alerts-understand-migration.md#common-issues-and-remediations)。 如果你不需要任何操作以完成迁移，你将在迁移过程中提供的电子邮件地址上收到通知。

## <a name="next-steps"></a>后续步骤

- [准备迁移](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
