---
title: 了解 Azure 监视器经典警报的自动迁移过程的工作原理
description: 了解自动迁移过程的工作原理。
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 8df83439d6754440648688ac1cc36ff66556a4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668241"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>了解经典警报规则的自动迁移过程

根据[之前的公告](monitoring-classic-retirement.md)，Azure Monitor 中的经典警报即将在 2019 年 9 月（原来是 2019 年 7 月）停用。 作为停用过程的一部分，Azure 门户中提供了[迁移工具](alerts-using-migration-tool.md)，供客户自己触发迁移。 如果您在 2019 年 8 月 31 日之前尚未使用迁移工具，Azure 监视器将从 2019 年 9 月 1 日开始自动迁移经典警报的过程。
本文将引导您完成自动迁移过程，并帮助您解决可能会遇到的任何问题。

  > [!NOTE]
  > 本文仅适用于 Azure 公共云。 Azure 监视器在 Azure 政府云和 Azure 中国 21Vianet 中的经典警报的停用过程将在未来日期公布。

## <a name="what-will-happen-during-the-automatic-migration-process"></a>在自动迁移过程中会发生什么？

- 从**2019 年 9 月 1 日起**，客户将不能创建任何新的经典警报规则，除非在某些[指标](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)上。
  - 对于例外情况，客户可以继续创建新的经典警报规则，并在 2020 年 6 月之前使用其经典警报。
- 从**2019 年 9 月 1**日起，经典警报的迁移将分批触发任何具有经典警报的客户。
- 与自愿迁移工具一样，某些不可迁移的经典警报规则将保留为它们。 这些经典警报规则将继续支持到 2020 年 6 月。 但是，任何无效的经典警报规则都将被删除，因为它们是不起作用的。
监视已删除的目标资源或[不再支持的指标](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics)上的任何经典警报规则都被视为无效。
- 订阅的迁移开始后，除非存在任何问题，否则迁移应在一小时内完成。 客户可以在[Azure 监视器 中的迁移边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)上监视迁移状态。
- 订阅所有者将收到一封有关成功完成迁移的电子邮件。
- 如果在迁移过程中存在任何问题，订阅所有者也会收到一封电子邮件，通知他们同样的问题。 客户可以使用迁移边栏选项卡查看问题的完整详细信息。
- 如果需要客户执行临时禁用资源锁定或更改策略分配等操作，客户需要在 2019 年 10 月 31 日前解决任何问题。 如果届时问题未得到解决，则无法保证成功迁移经典警报。

    > [!NOTE]
    > 如果不想等待自动迁移过程启动，您仍可以使用迁移工具自愿触发迁移。

## <a name="important-things-to-note"></a>需要注意的重要事项

迁移过程会将经典警报规则转换为新的等效警报规则，并创建操作组。 在准备期间，请注意以下几点：

- 新警报规则的通知有效负载格式不同于经典警报规则的通知有效负载格式，因为它们支持更多功能。 如果您有由经典警报规则触发的逻辑应用、Runbook 或 Webhook，则由于通知负载的差异，迁移完成后，它们可能会按预期方式停止运行。 [了解如何准备迁移](alerts-prepare-migration.md)。

- 无法使用该工具迁移某些经典警报规则。 [了解哪些规则不能迁移，以及如何处理它们](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated)。

    > [!NOTE]
    > 迁移过程不会影响经典警报规则的评估。 在完成迁移并且新的警报规则生效之前，这些经典规则会继续运行并发送警报。

## <a name="what-if-the-automatic-migration-fails"></a>如果自动迁移失败怎么办？

当自动迁移过程失败时，订阅所有者将收到一封电子邮件，通知他们问题。 您可以使用 Azure 监视器中的迁移边栏选项卡查看问题的完整详细信息。

请参阅[故障排除指南](alerts-understand-migration.md#common-problems-and-remedies)来解决迁移期间可能遇到的问题。

  > [!NOTE]
  > 如果需要客户执行临时禁用资源锁定或更改策略分配等操作，客户需要在 2019 年 10 月 31 日前解决任何问题。 如果届时问题未得到解决，则无法保证成功迁移经典警报。

## <a name="next-steps"></a>后续步骤

- [准备迁移](alerts-prepare-migration.md)
- [了解迁移工具的工作原理](alerts-understand-migration.md)
