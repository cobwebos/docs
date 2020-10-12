---
title: 了解 Azure Monitor 经典警报的自动迁移过程是如何工作的
description: 了解自动迁移过程的工作方式。
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 6a2d032c6aa33b72fe422638df45ca48bf8b1036
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87847276"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>了解经典警报规则的自动迁移过程

如前所述 [，Azure Monitor](monitoring-classic-retirement.md)中的经典警报已停用，但对于尚不支持新警报的资源仍处于有限使用。 在停用过程中， [迁移工具](alerts-using-migration-tool.md) 在 Azure 门户中提供，供客户自行触发迁移。
本文将指导你完成自动迁移过程，并帮助你解决你可能遇到的任何问题。

  > [!NOTE]
  > 本文仅适用于 Azure 公有云。 Azure 政府云和 Azure 中国世纪 Azure Monitor 经典警报的停用过程将在未来的日期公布。

## <a name="what-will-happen-during-the-automatic-migration-process"></a>自动迁移过程中将发生什么情况？

- 从 **2019 年9月1日**开始，客户将无法创建除 [特定指标](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)之外的任何新的经典警报规则。
- 对于例外，客户可以继续创建新的经典警报规则并使用其经典警报，直到进一步公告。
- 从 **2019 年9月1日**开始，对于具有经典警报的所有客户，将成批触发经典警报的迁移。
- 与自愿迁移工具一样，某些不可迁移的经典警报规则将保留原样。 在进一步公告之前，将继续支持这些经典警报规则。 但是，任何无效的经典警报规则将被删除，因为它们不起作用。
监视已删除目标资源或 [不再受支持的度量值](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) 的任何经典警报规则都被视为无效。
- 订阅启动后，除非有任何问题，否则应在一小时内完成迁移。 [在 Azure Monitor 中，客户可以监视迁移边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)上的迁移状态。
- 成功完成迁移后，订阅所有者将收到一封电子邮件。
- 如果在迁移过程中出现任何问题，订阅所有者还会收到一封电子邮件，告知他们。 客户可以使用 "迁移" 边栏选项卡查看问题的完整详细信息。
- 如果客户需要执行操作（如暂时禁用资源锁定或更改策略分配），则客户需要解决此类问题。 如果未解决问题，则无法保证经典警报的成功迁移。

    > [!NOTE]
    > 如果你不想等待自动迁移过程开始，你仍可以使用迁移工具主动触发迁移。

## <a name="important-things-to-note"></a>需要注意的重要事项

迁移过程会将经典警报规则转换为新的等效警报规则，并创建操作组。 在准备期间，请注意以下几点：

- 新警报规则的通知负载格式不同于经典警报规则的通知负载格式，因为它们支持更多功能。 如果有由经典警报规则触发的逻辑应用、runbook 或 webhook，则在迁移完成后，由于通知有效负载不同，它们可能会停止正常工作。 [了解如何准备迁移](alerts-prepare-migration.md)。

- 不能使用工具迁移某些经典警报规则。 [了解无法迁移哪些规则以及](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts)如何处理它们。

    > [!NOTE]
    > 迁移过程不会影响经典警报规则的评估。 在完成迁移并且新的警报规则生效之前，这些经典规则会继续运行并发送警报。

## <a name="what-if-the-automatic-migration-fails"></a>如果自动迁移失败，该怎么办？

当自动迁移过程失败时，订阅所有者将收到一封电子邮件，通知他们此问题。 可以使用 Azure Monitor 中的 "迁移" 边栏选项卡查看问题的完整详细信息。

请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)来解决迁移期间可能遇到的问题。

  > [!NOTE]
  > 如果客户需要执行操作（如暂时禁用资源锁定或更改策略分配），则客户需要解决此类问题。 如果未解决问题，则无法保证经典警报的成功迁移。

## <a name="next-steps"></a>后续步骤

- [迁移准备](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
