---
title: 从 Azure 流分析 Azure Cosmos DB 输出
description: 本文介绍如何将数据从 Azure 流分析输出到 Azure Cosmos DB。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 7832c53cfa9e0c5c3d0903c52bae8033402a2c0d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875591"
---
# <a name="azure-cosmos-db-output-from-azure-stream-analytics"></a>从 Azure 流分析 Azure Cosmos DB 输出

[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 是一种分布全球的数据库服务，它提供全球范围内不设限的弹性缩放、丰富查询和自动索引（经由与构架无关的数据模型）。 若要了解流分析的 Azure Cosmos DB 容器选项，请参阅[将 Azure Cosmos DB 用作输出的流分析](stream-analytics-documentdb-output.md)一文。

流分析中的 Azure Cosmos DB 输出当前不可在 Azure 中国世纪互联和 Azure 德国 (T-Systems International) 区域中使用。

> [!Note]
> Azure 流分析仅支持使用 SQL API 连接到 Azure Cosmos DB。
> 尚不支持使用其他 Azure Cosmos DB API。 如果使用其他 API 将 Azure 流分析指向 创建的 Azure Cosmos DB 帐户，则可能无法正确存储数据。

下表描述了用于创建 Azure Cosmos DB 输出的属性。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 | 用于在流分析查询中引用此输出的别名。 |
| 接收器 | Azure Cosmos DB。 |
| 导入选项 | 选择“从订阅中选择 Cosmos DB”或“手动提供 Cosmos DB 设置” 。
| 帐户 ID | Azure Cosmos DB 帐户的名称或终结点 URI。 |
| 帐户密钥 | Azure Cosmos DB 帐户的共享访问密钥。 |
| 数据库 | Azure Cosmos DB 数据库名称。 |
| 容器名称 | 要使用的容器名称，该名称必须在 Cosmos DB 中存在。 示例：  <br /><ul><li> _MyContainer_：名为“MyContainer”的容器必须存在。</li>|
| 文档 ID |可选。 输出事件中的字段的名称，该字段用于指定插入或更新操作所基于的主键。

## <a name="partitioning"></a>分区

分区键基于查询中的 PARTITION BY 子句。 输出写入器的数目按照 [完全并行化查询](stream-analytics-scale-jobs.md)的输入分区进行。 流分析将 Cosmos DB 输出分区键转换为字符串。 例如，如果您的分区键的值为1且类型为 bigint，则将其转换为类型字符串的 "1"。

## <a name="output-batch-size"></a>输出批大小

有关最大消息大小，请参阅 [Azure Cosmos DB 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cosmos-db-limits)。 批大小和写入频率根据 Azure Cosmos DB 响应动态调整。 流分析不会施加预先确定的限制。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure CLI 创建 Azure 流分析作业](quick-create-azure-cli.md)
* [快速入门：使用 ARM 模板创建 Azure 流分析作业](quick-create-azure-resource-manager.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-vs-code.md)
