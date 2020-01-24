---
title: 使用自愿迁移工具在 Azure Monitor 中迁移经典警报
description: 了解如何使用自愿迁移工具迁移经典警报规则。
author: yanivlavi
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: c4ebb2e9572f1dcc9ade548a55fc44d7441e5a79
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705575"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自愿迁移工具迁移经典警报规则

如前所述[，Azure Monitor](monitoring-classic-retirement.md)中的经典警报在2019年9月（最初是2019年7月）内停用。 向使用经典警报规则的客户和想要触发迁移的客户提供 Azure 门户迁移工具。 本文介绍如何使用迁移工具在2019年9月开始自动迁移之前主动迁移经典警报规则。

> [!NOTE]
> 由于迁移工具的推出延迟，经典警报的停用日期已于2019年6月 30 2019 日最初发布日期[延长到了年8月 31](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)日。

## <a name="benefits-of-new-alerts"></a>新警报的优点

经典警报将替换为 Azure Monitor 中的新的统一警报。 新的警报平台具有以下优势：

- 对于[更多的 Azure 服务](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)，可以针对多种多维指标发出警报。
- 新的指标警报支持[多资源警报规则](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)，大大降低了管理许多规则的开销。
- 统一通知机制，支持：
  - [操作组](action-groups.md)，一种模块化通知机制，适用于所有新的警报类型（指标、日志和活动日志）。
  - 新的通知机制，如短信、语音和 ITSM 连接器。
- [统一的警报体验](alerts-overview.md)将不同信号（指标、日志和活动日志）上的所有警报都引入到一个位置。

## <a name="before-you-migrate"></a>迁移之前

迁移过程将经典警报规则转换为新的等效警报规则，并创建操作组。 准备好后，请注意以下几点：

- 通知负载格式和用于创建和管理新警报规则的 Api 不同于经典警报规则，因为它们支持更多功能。 [了解如何为迁移做准备](alerts-prepare-migration.md)。

- 不能使用工具迁移某些经典警报规则。 [了解无法迁移哪些规则以及](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)如何处理它们。

    > [!NOTE]
    > 迁移过程不会影响经典警报规则的评估。 它们将继续运行并发送警报，直到它们被迁移，新的警报规则生效。

## <a name="how-to-use-the-migration-tool"></a>如何使用迁移工具

若要在 Azure 门户中触发经典警报规则的迁移，请执行以下步骤：

1. 在[Azure 门户](https://portal.azure.com)中，选择 "**监视器**"。

1. 选择 "**警报**"，然后选择 "**管理警报规则**" 或 "**查看经典警报**"。

1. 选择 "**迁移到新规则**" 以转到 "迁移" 登陆页。 此页显示所有订阅及其迁移状态的列表：

    ![迁移-登陆](media/alerts-migration/migration-landing.png "迁移规则")

    使用此工具可以迁移的所有订阅都标记为已**准备好进行迁移**。

    > [!NOTE]
    > 迁移工具正在逐步推出使用经典警报规则的所有订阅。 在部署的早期阶段，你可能会看到某些标记为 "未准备好进行迁移" 的订阅。

1. 选择一个或多个订阅，然后选择 "**预览迁移**"。

    生成的页面将显示经典警报规则的详细信息，这些规则将一次为一个订阅迁移。 你还可以选择 **"下载此订阅的迁移详细信息**" 以获取 CSV 格式的详细信息。

    ![迁移-预览](media/alerts-migration/migration-preview.png "预览迁移")

1. 指定一个或多个要接收迁移状态通知的电子邮件地址。 迁移完成后，你将收到电子邮件，或者你需要执行任何操作。

1. 选择 "**开始迁移**"。 阅读确认对话框中显示的信息，并确认已准备好开始迁移过程。

    > [!IMPORTANT]
    > 启动订阅的迁移后，将无法为该订阅编辑或创建经典警报规则。 此限制可确保在迁移到新规则的过程中不会丢失对经典警报规则所做的任何更改。 尽管不能更改经典警报规则，但仍会继续运行，并在迁移之前提供警报。 完成订阅的迁移后，不能再使用经典警报规则。

    ![迁移-确认](media/alerts-migration/migration-confirm.png "确认开始迁移")

1. 迁移完成后，或者如果需要，你将在之前提供的地址收到电子邮件。 你还可以在门户中的 "迁移" 登录页上定期检查状态。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>为什么我的订阅列为 "未准备好迁移"？

迁移工具会分阶段向客户推出。 在早期阶段，大多数或所有订阅可能被标记为 "**未准备好进行迁移**"。 

订阅准备好迁移时，订阅所有者会收到一封电子邮件，指出该工具可用。 请留意此消息。

### <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

在订阅级别分配有 "监视参与者" 角色的用户能够触发迁移。 有关[迁移过程的基于角色的访问控制的详细信息](alerts-understand-migration.md#who-can-trigger-the-migration)，请参阅。

### <a name="how-long-will-the-migration-take"></a>迁移需要多长时间？

在一小时内完成大多数订阅的迁移。 可以在迁移登录页上跟踪迁移进度。 在迁移过程中，请确保你的警报仍在经典警报系统中或新的警报系统中运行。

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>如果在迁移过程中遇到问题，我该怎么办？

请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)以获取有关在迁移过程中可能遇到的问题的帮助。 如果完成迁移需要执行任何操作，则会在设置工具时提供的电子邮件地址收到通知。

## <a name="next-steps"></a>后续步骤

- [准备迁移](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
