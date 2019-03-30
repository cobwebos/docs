---
title: 了解如何自愿警报迁移工具在 Azure Monitor 的工作原理
description: 了解警报的迁移工具的工作原理和时遇到问题进行故障排除。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632052"
---
# <a name="understand-how-the-migration-tool-works"></a>了解迁移工具的工作原理

作为[之前公布](monitoring-classic-retirement.md)，在 Azure Monitor 经典警报将被停用在 2019 年 7 月中。 迁移工具来自动触发迁移可在 Azure 门户中，并向使用经典警报规则的客户推出。

本文将引导您完成自愿迁移工具的工作原理。 它还介绍了有关的一些常见问题的补救措施。

## <a name="which-classic-alert-rules-can-be-migrated"></a>可以迁移哪些经典警报规则？

虽然可以使用工具迁移几乎所有的经典警报规则，有一些例外情况。 不使用该工具会迁移以下警报规则 （或在 2019 年 7 月中的自动迁移期间）

- 在虚拟机来宾指标 （Windows 和 Linux） 上的经典警报规则。 [有关如何重新创建此类中新指标警报的警报规则，请参阅指南](#guest-metrics-on-virtual-machines)
- 在经典存储度量值的经典警报规则。 [有关监视你的经典存储帐户，请参阅指南](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- 在某些存储帐户指标的经典警报规则。 [下面的详细信息](#storage-account-metrics)

如果你的订阅具有经典的任何此类规则，将迁移其他规则，但这些规则将需要手动迁移。 我们无法提供的自动迁移，因为任何此类现有经典指标警报将继续开始，逐渐到年 6 月 2020 来提供用户有时间将移动到新的警报。 但是，新的经典警报可以创建 post 2019 年 6 月。

### <a name="guest-metrics-on-virtual-machines"></a>虚拟机上的来宾指标

若要能够在来宾指标上创建新的指标警报，来宾指标需要发送到 Azure Monitor 的自定义指标存储。 请按照下面的说明来启用诊断设置中的 Azure Monitor 接收器。

- [为 Windows Vm 启用来宾指标](collect-custom-metrics-guestos-resource-manager-vm.md)
- [为 Linux Vm 启用来宾指标](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

完成上述步骤后，可以在来宾指标创建新指标警报。 一旦已重新创建新的指标警报，可以删除经典警报。

### <a name="storage-account-metrics"></a>存储帐户指标

存储帐户上的所有经典警报可以迁移这些警报基于以下指标除外：

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

百分比指标的经典警报规则将需要迁移基于[旧的和新的存储度量值之间的映射](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)。 将需要适当地修改因为可用的新度量值是绝对阈值。

拆分为两个新警报与将需要 AnonymousThrottlingError 和 SASThrottlingError 的经典警报规则是没有合并的指标，提供相同的功能。 将需要相应地调整阈值。

## <a name="roll-out-phases"></a>推出阶段

迁移工具推出阶段中于使用经典警报规则的客户。 **订阅所有者**已准备好使用工具迁移订阅时将收到一封电子邮件。

> [!NOTE]
> 该工具正在推出阶段，在早期阶段，用户可以看到，大部分订阅不尚未准备好进行迁移。

目前**子集**的订阅，其中**仅**类型被标记为准备好进行迁移的以下资源上设置了经典警报规则。 将在即将推出的阶段中添加对更多资源类型支持。

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>谁可以触发迁移？

任何用户都具有内置角色的**监视参与者**在订阅级别将能够触发迁移。 具有以下权限的自定义角色的用户还可以触发迁移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>常见的问题和修正

一次你[触发迁移](alerts-using-migration-tool.md)，我们将使用提供通知你完成的迁移的电子邮件地址，或是否有你需要执行的操作。 以下部分介绍的一些常见问题以及如何更正它们

### <a name="validation-failed"></a>验证失败

由于你的订阅中的经典警报规则对一些最新更改，无法迁移订阅。 这是临时问题。 你可以在迁移状态向后移动后重新启动迁移**准备好进行迁移**中的几天。

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>策略/作用域锁导致我们无法迁移你的规则

作为迁移的一部分，将创建新的指标警报和新操作组和经典警报规则会被删除 （已创建新的规则）。 但是，没有策略或作用域锁，导致我们无法创建资源。 根据策略或作用域的锁，无法迁移部分或全部规则。 你可以通过暂时删除作用域锁/策略来解决此问题，并触发试迁移。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [准备迁移](alerts-prepare-migration.md)
