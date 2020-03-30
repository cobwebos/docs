---
title: Azure 监视器日志查询中的资源（） 表达式 |微软文档
description: 资源表达式用于以资源为中心的 Azure 监视器日志查询中，以便从多个资源检索数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665693"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure 监视器日志查询中的资源（） 表达式

该`resource`表达式用于 Azure 监视器查询[，该查询范围限定为资源](scope.md#query-scope)，以便从其他资源检索数据。 


## <a name="syntax"></a>语法

`resource(`*标识符*`)`

## <a name="arguments"></a>自变量

- *标识符*：资源的资源 ID。

| 标识符 | 说明 | 示例
|:---|:---|:---|
| 资源 | 包括资源的数据。 | 资源（"/订阅/xxxxxx-xxx-xxx-xxxxxx-xxxxxxxxxxxx/资源组/我的资源组/供应商/微软.计算/虚拟机/myvm"） |
| 资源组或订阅 | 包括资源及其包含的所有资源的数据。  | 资源（"/订阅/xxxxxxxxx-xxx-xxx-xxxxxxxxxxxxxxx/资源组/我的资源组） |


## <a name="notes"></a>说明

* 您必须具有对资源的读取访问权限。


## <a name="examples"></a>示例

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>后续步骤

- 有关查询范围的详细信息，请参阅[Azure 监视器日志分析中的日志查询范围和时间范围](scope.md)。
- 访问有关 [Kusto 查询语言](/azure/kusto/query/)的完整文档。
