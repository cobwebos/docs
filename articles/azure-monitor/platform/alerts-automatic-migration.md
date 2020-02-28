---
title: 了解 Azure Monitor 经典警报的自动迁移过程是如何工作的
description: 了解自动迁移过程的工作方式。
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668241"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>了解经典警报规则的自动迁移过程

如前所述[，Azure Monitor](monitoring-classic-retirement.md)中的经典警报在2019年9月（最初是2019年7月）内停用。 在停用过程中，[迁移工具](alerts-using-migration-tool.md)在 Azure 门户中提供，供客户自行触发迁移。 如果你尚未在2019年8月31日前使用迁移工具，Azure Monitor 将开始在2019年9月1日开始自动迁移经典警报。
本文将指导你完成自动迁移过程，并帮助你解决你可能遇到的任何问题。

  > [!NOTE]
  > 本文仅适用于 Azure 公有云。 Azure 政府云和 Azure 中国世纪 Azure Monitor 经典警报的停用过程将在未来的日期公布。

## <a name="what-will-happen-during-the-automatic-migration-process"></a>自动迁移过程中将发生什么情况？

- 从**2019 年9月1日**开始，客户将无法创建除[特定指标](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)之外的任何新的经典警报规则。
  - 对于例外，客户可以继续创建新的经典警报规则，并在2020年6月之前使用其经典警报。
- 从**2019 年9月1日**开始，对于具有经典警报的所有客户，将成批触发经典警报的迁移。
- 与自愿迁移工具一样，某些不可迁移的经典警报规则将保留原样。 在2020年6月之前，将继续支持这些经典警报规则。 但是，任何无效的经典警报规则将被删除，因为它们不起作用。
监视已删除目标资源或[不再受支持的度量值](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)的任何经典警报规则都被视为无效。
- 订阅启动后，除非有任何问题，否则应在一小时内完成迁移。 [在 Azure Monitor 中，客户可以监视迁移边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)上的迁移状态。
- 成功完成迁移后，订阅所有者将收到一封电子邮件。
- 如果在迁移过程中出现任何问题，订阅所有者还会收到一封电子邮件，告知他们。 客户可以使用 "迁移" 边栏选项卡查看问题的完整详细信息。
- 如果客户需要执行某个操作（如暂时禁用资源锁定或更改策略分配），则客户需要解决2019年10月31日的任何问题。 如果未解决问题，则无法保证经典警报的成功迁移。

    > [!NOTE]
    > 如果你不想等待自动迁移过程开始，你仍可以使用迁移工具主动触发迁移。

## <a name="important-things-to-note"></a>需要注意的重要事项

迁移过程将经典警报规则转换为新的等效警报规则，并创建操作组。 准备好后，请注意以下几点：

- 新警报规则的通知负载格式不同于经典警报规则的通知负载格式，因为它们支持更多功能。 如果有由经典警报规则触发的逻辑应用、runbook 或 webhook，则在迁移完成后，由于通知有效负载不同，它们可能会停止正常工作。 [了解如何为迁移做准备](alerts-prepare-migration.md)。

- 不能使用工具迁移某些经典警报规则。 [了解无法迁移哪些规则以及](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)如何处理它们。

    > [!NOTE]
    > 迁移过程不会影响经典警报规则的评估。 它们将继续运行并发送警报，直到它们被迁移，新的警报规则生效。

## <a name="what-if-the-automatic-migration-fails"></a>如果自动迁移失败，该怎么办？

当自动迁移过程失败时，订阅所有者将收到一封电子邮件，通知他们此问题。 可以使用 Azure Monitor 中的 "迁移" 边栏选项卡查看问题的完整详细信息。

请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)以获取有关在迁移过程中可能遇到的问题的帮助。

  > [!NOTE]
  > 如果客户需要执行某个操作（如暂时禁用资源锁定或更改策略分配），则客户需要解决2019年10月31日的任何问题。 如果未解决问题，则无法保证经典警报的成功迁移。

## <a name="next-steps"></a>后续步骤

- [准备迁移](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
