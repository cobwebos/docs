---
title: 通过使用自愿迁移工具迁移在 Azure Monitor 经典警报
description: 了解如何使用自愿迁移工具迁移经典警报规则。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 55e6b515328df635a423e0ecf736b03e17a90b8f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698426"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>使用自愿迁移工具迁移经典警报规则

作为[之前公布](monitoring-classic-retirement.md)，在 Azure Monitor 经典警报将被停用在 2019 年 7 月中。 迁移工具是的客户使用经典警报规则的用户，那些想要自行触发迁移到 Azure 门户中可用。 此文章介绍了如何使用迁移工具将自动迁移开始在 2019 年 7 月之前自愿迁移经典警报规则。

## <a name="benefits-of-new-alerts"></a>新警报的优点

要通过 Azure Monitor 中的全新、 统一警报替换经典警报。 新的警报平台具有以下优势：

- 可以根据各种多维指标发出警报[许多更多的 Azure 服务](alerts-metric-near-real-time.md#metrics-and-dimensions-supported)。
- 新指标警报支持[多资源的警报规则](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)，极大地减少管理多个规则的开销。
- 统一的通知机制，它支持：
  - [操作组](action-groups.md)，适用于 （指标、 日志和活动日志） 的所有新警报类型的模块化通知机制。
  - 新的通知机制，如 SMS、 语音、 和 ITSM 连接器。
- [统一的警报体验](alerts-overview.md)上不同的信号 （指标、 日志和活动日志） 的所有警报都将引入一个位置。

## <a name="before-you-migrate"></a>在迁移之前

迁移过程将经典警报规则转换到新的、 等效的警报规则，并创建操作组。 做好准备，注意以下几点：

- 通知有效负载格式和 Api 来创建和管理新的警报规则是不同的经典警报规则，因为它们支持更多的功能。 [了解如何为迁移准备](alerts-prepare-migration.md)。

- 不能使用工具迁移一些经典警报规则。 [了解不能迁移哪些规则，应如何处理它们](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated)。

    > [!NOTE]
    > 迁移过程不会影响经典警报规则的评估。 它们将继续运行并发送警报，直到迁移和新警报规则才会生效。

## <a name="how-to-use-the-migration-tool"></a>如何使用迁移工具

若要触发的经典警报规则在 Azure 门户中迁移，请执行以下步骤：

1. 在中[Azure 门户](https://portal.azure.com)，选择**监视器**。

1. 选择**警报**，然后选择**管理警报规则**或**查看经典警报**。

1. 选择**迁移到新的规则**转到迁移登陆页。 此页显示所有订阅及其迁移状态的列表：

    ![迁移登陆](media/alerts-migration/migration-landing.png "迁移规则")

    可以使用该工具进行迁移的所有订阅都标记为**准备好迁移**。

    > [!NOTE]
    > 迁移工具即将推出阶段到使用经典警报规则的所有订阅。 在推出的早期阶段，可能会看到某些订阅标记为未准备好进行迁移。

1. 选择一个或多个订阅，然后选择**预览迁移**。

    在生成的页面显示了将迁移以供订阅一次的经典警报规则的详细信息。 您还可以选择**下载此订阅的迁移详细信息**CSV 格式中获取详细信息。

    ![迁移-preview](media/alerts-migration/migration-preview.png "预览迁移")

1. 指定要通知的迁移状态的一个或多个电子邮件地址。 迁移完成时，或如果你不需要任何操作，你将收到电子邮件。

1. 选择**开始迁移**。 读取在确认对话框中显示的信息并确认你做好开始迁移过程。

    > [!IMPORTANT]
    > 启动订阅的迁移后，你将无法编辑或创建该订阅的经典警报规则。 此限制可确保在迁移到新的规则将失去对经典警报规则的任何更改。 尽管你将无法更改经典警报规则，但仍会继续运行并提供警报，直到它们已被迁移。 为你的订阅，在迁移完毕后，不能再使用经典警报规则。

    ![迁移确认](media/alerts-migration/migration-confirm.png "确认开始迁移")

1. 迁移完成后，或不需要你执行操作，你将收到一封电子邮件在前面提供的地址。 可以也会定期检查时在门户中的迁移登录页的状态。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>为什么我的订阅显示为未准备好迁移？

迁移工具向推出客户分阶段。 在早期阶段，大多数或所有订阅可能被都标记为**尚未做好迁移**。 Mid-年 4 月，但是，所有订阅都应准备好迁移。

在订阅处于准备好进行迁移，订阅所有者将收到一则电子邮件消息，指出此工具均可用。 请密切关注此消息。

### <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

具有在订阅级别分配给他们的监视参与者角色的用户将能够触发迁移。 [了解有关基于角色的访问控制迁移过程的详细信息](alerts-understand-migration.md#who-can-trigger-the-migration)。

### <a name="how-long-will-the-migration-take"></a>将迁移需要多长？

对于大多数订阅中，在一小时完成迁移。 您可以跟踪的迁移登录页面上的迁移进度。 在迁移期间，确保经典警报系统或新的警报仍在运行。

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>如果我会在迁移期间遇到问题，我可以做什么？

请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)有关在迁移期间可能会遇到的问题的帮助。 如果你不需要任何操作以完成迁移，则会在你设置该工具时提供的电子邮件地址通知您。

## <a name="next-steps"></a>后续步骤

- [为迁移准备](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
