---
title: Azure Monitor 日志查询中的 resource （）表达式 |Microsoft Docs
description: 资源表达式用于以资源为中心 Azure Monitor 日志查询，以检索多个资源中的数据。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 4787dc8a8cc06e0ad0be88597dab3481284fb58b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900212"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure Monitor 日志查询中的 resource （）表达式

`resource` 表达式用于[作用域为资源](scope.md#query-scope)的 Azure Monitor 查询，以从其他资源中检索数据。 


## <a name="syntax"></a>语法

`resource(`标识符`)`

## <a name="arguments"></a>参数

- *标识符*：资源的资源 ID。

| 标识符 | 描述 | 示例
|:---|:---|:---|
| 资源 | 包含资源的数据。 | 资源（"/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm"） |
| 资源组或订阅 | 包含资源的数据以及它包含的所有资源。  | 资源（"/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup"） |


## <a name="notes"></a>说明

* 您必须对资源具有读取访问权限。


## <a name="examples"></a>示例

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>后续步骤

- 有关查询范围的详细信息，请参阅[Azure Monitor Log Analytics 中的日志查询范围和时间范围](scope.md)。
- 访问有关 [Kusto 查询语言](/azure/kusto/query/)的完整文档。
