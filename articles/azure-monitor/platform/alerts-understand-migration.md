---
title: 了解有关 Azure Monitor 警报自愿迁移工具的工作原理
description: 了解警报迁移工具的工作原理和解决问题。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: b5a13254fc9dfd58db83a1bc8b9dd071cfbbdab2
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015594"
---
# <a name="understand-how-the-migration-tool-works"></a>了解迁移工具的工作原理

作为[之前公布](monitoring-classic-retirement.md)，在 Azure Monitor 经典警报将被在 2019 年 9 月停用 (已最初年 7 月 2019年)。 迁移工具是的客户使用经典警报规则的用户，那些想要自行触发迁移到 Azure 门户中可用。

此文章介绍了自愿性迁移工具的工作原理。 它还介绍了一些常见问题的补救措施。

> [!NOTE]
> 由于推出的迁移工具中的延迟，经典警报迁移的停用日期已[延长到 2019 年 8 月 31 日](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)从 2019 年 6 月 30 日的最初公布日期。

## <a name="which-classic-alert-rules-can-be-migrated"></a>可以迁移哪些经典警报规则？

尽管该工具可以迁移几乎所有经典警报规则，但有一些例外情况。 通过使用工具 （或在 2019 年 9 月中的自动迁移期间），则不会迁移以下警报规则：

- 在虚拟机来宾指标 （Windows 和 Linux） 上的经典警报规则。 请参阅[重新创建此类中新指标警报的警报规则的指南](#guest-metrics-on-virtual-machines)这篇文章中更高版本。
- 在经典存储度量值的经典警报规则。 请参阅[监视经典存储帐户的指南](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)。
- 在某些存储帐户指标的经典警报规则。 请参阅[详细信息](#storage-account-metrics)这篇文章中更高版本。

如果你的订阅具有经典的任何此类规则，必须手动进行迁移。 我们无法提供的自动迁移，因为这些类型的任何现有的经典指标警报将继续工作到 2020 年 6 月。 此扩展使用户有时间将移动到新的警报。 但是，可以在 2019 年 8 月之后创建新的经典警报。

### <a name="guest-metrics-on-virtual-machines"></a>虚拟机上的来宾指标

可以针对来宾指标创建新的指标警报前，来宾指标必须发送到 Azure Monitor 的自定义指标存储区。 请按照以下说明启用诊断设置中的 Azure Monitor 接收器操作：

- [为 Windows Vm 启用来宾指标](collect-custom-metrics-guestos-resource-manager-vm.md)
- [为 Linux Vm 启用来宾指标](collect-custom-metrics-linux-telegraf.md)

完成这些步骤后，可以在来宾指标来创建新指标警报。 并创建新的指标警报后，可以删除经典警报。

### <a name="storage-account-metrics"></a>存储帐户指标

存储帐户上的所有经典警报可以迁移这些指标的警报除外：

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

百分比指标规则必须迁移经典警报基于[旧的和新的存储度量值之间的映射](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)。 将需要进行适当地修改，因为在可用的新度量值是绝对阈值。

经典警报规则上 AnonymousThrottlingError 和 SASThrottlingError 必须将拆分为两个新警报，因为没有提供相同的功能没有合并的指标。 将需要相应地调整阈值。

## <a name="rollout-phases"></a>推出阶段

迁移工具推出阶段中于使用经典警报规则的客户。 已准备好使用该工具来迁移订阅时，订阅所有者将收到一封电子邮件。

> [!NOTE]
> 由于正在分阶段推出该工具，您可能会看到，大部分订阅不尚未准备好进行迁移的早期阶段。

当前订阅的子集被标记为迁移做好准备。 该子集包括具有经典警报规则仅在以下资源类型上的这些订阅。 将在即将推出的阶段中添加对更多资源类型支持。

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

任何用户都包含在订阅级别的监视参与者内置角色可以触发迁移。 具有以下权限的自定义角色的用户还可以触发迁移：

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-problems-and-remedies"></a>常见问题和补救措施

检查完[触发迁移](alerts-using-migration-tool.md)，你将收到电子邮件地址提供通知您迁移已完成，或如果你不需要任何操作。 本部分介绍一些常见的问题以及如何处理它们。

### <a name="validation-failed"></a>验证失败

由于你的订阅中的经典警报规则对一些最新更改，无法迁移订阅。 此问题是临时的。 迁移状态移回后，你可以重新启动迁移**准备好进行迁移**中的几天。

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>阻止我们迁移你的规则的策略或作用域锁

作为迁移的一部分，将创建新的指标警报和新操作组，并将然后删除经典警报规则。 但是，没有策略或作用域锁，导致我们无法创建资源。 根据策略或作用域的锁，无法迁移部分或全部规则。 可以通过暂时删除作用域锁或策略，以及触发迁移试来解决此问题。

## <a name="next-steps"></a>后续步骤

- [如何使用迁移工具](alerts-using-migration-tool.md)
- [为迁移准备](alerts-prepare-migration.md)
