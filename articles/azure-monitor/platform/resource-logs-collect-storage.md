---
title: 将 Azure 资源日志存档到存储帐户 | Microsoft Docs
description: 了解如何存档 Azure 资源日志，将其长期保留在存储帐户中。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804599"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>将 Azure 资源日志存档到存储帐户
Azure 中的[平台日志](platform-logs-overview.md)（包括 Azure 活动日志和资源日志）提供 Azure 资源及其所依赖的 Azure 平台的详细诊断和审核信息。  本文介绍如何将平台日志收集到到 Azure 存储帐户，以便保留要存档的数据。

## <a name="prerequisites"></a>必备条件
需[创建 Azure 存储帐户](../../storage/common/storage-account-create.md)（如果还没有）。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，存储帐户就不必位于发送日志的资源所在的订阅中。

> [!IMPORTANT]
> 若要将数据发送到不可变存储，请设置存储帐户的不可变策略，如为[Blob 存储设置和管理永久性策略](../../storage/blobs/storage-blob-immutability-policies-manage.md)中所述。 必须遵循本文中的所有步骤，包括启用受保护的追加 blob 写入操作。

> [!IMPORTANT]
> Azure Data Lake Storage Gen2 帐户目前不支持作为诊断设置的目标，即使它们可能在 Azure 门户中被列为有效选项。


不应使用其中存储了其他非监视数据的现有存储帐户，以便更好地控制数据所需的访问权限。 不过，如果要将活动日志和资源日志一同存档，则可以选择使用该存储帐户在一个中心位置保留所有监视数据。

## <a name="create-a-diagnostic-setting"></a>创建诊断设置
需要通过创建 Azure 资源的诊断设置，将平台日志发送到存储和其他目标。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的日志和指标](diagnostic-settings.md)。


## <a name="collect-data-from-compute-resources"></a>对来自计算资源的数据进行收集
诊断设置将如同收集任何其他资源一样收集 Azure 计算资源的资源日志，但不是其来宾操作系统或工作负荷。 若要收集该数据，请安装 [Windows Azure 诊断代理](diagnostics-extension-overview.md)。 


## <a name="schema-of-platform-logs-in-storage-account"></a>存储帐户中的平台日志架构

创建诊断设置以后，一旦在已启用的日志类别之一中出现事件，就会在存储帐户中创建存储容器。 容器中的 blob 使用以下命名约定：

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，网络安全组的 blob 的名称可能如下所示：

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

每个 PT1H.json blob 都包含一个 JSON blob，其中的事件为在 blob URL 中指定的小时（例如 h=12）内发生的。 在当前的小时内发生的事件将附加到 PT1H.json 文件。 分钟值始终为 00 (m=00)，因为资源日志事件按小时细分成单个 blob。

在 PT1H.json 文件中，每个事件都按以下格式存储。 这将使用公用顶级架构，但对于[资源日志架构](diagnostic-logs-schema.md)和[活动日志架构](activity-log-schema.md)中所述的每个 Azure 服务都是唯一的。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 平台日志使用 [JSON 行](http://jsonlines.org/)写入到 blob 存储，其中每个事件都是一行，换行符表示新事件。 此格式已在 2018 年 11 月实现。 在此日期之前，日志以记录的 json 数组形式写入到 blob 存储，详见[为存档到存储帐户的 Azure Monitor 平台日志的格式更改做准备](resource-logs-blob-format.md)。

## <a name="next-steps"></a>后续步骤

* [详细阅读资源日志](platform-logs-overview.md)。
* [创建诊断设置以收集 Azure 中的日志和指标](diagnostic-settings.md)。
* [下载 blob 进行分析](../../storage/blobs/storage-quickstart-blobs-dotnet.md)。
* [Azure Monitor 存档 Azure Active Directory 日志](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)。
