---
title: 来自 Azure 流分析的表存储输出
description: 本文介绍 azure 表存储作为 Azure 流分析的输出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 3e5b747e55c8f246167bcf8bbde3542146e69706
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891964"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>来自 Azure 流分析的表存储输出

[Azure 表存储](../storage/common/storage-introduction.md) 提供了具有高可用性且可大规模缩放的存储，因此应用程序可以自动缩放以满足用户需求。 表存储是 Microsoft 推出的 NoSQL 键/属性存储，适用于对架构的约束性较少的结构化数据。 Azure 表存储可用于持久地存储数据，方便进行高效的检索。

下表列出了用于创建表输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |该名称是在查询中使用的友好名称，用于将查询输出定向到此表存储。 |
| 存储帐户 |存储帐户的名称（正在向该存储帐户发送输出）。 |
| 存储帐户密钥 |与存储帐户关联的访问密钥。 |
| 表名称 |表的名称。 如果表不存在，则创建该表。 |
| 分区键 |包含分区键的输出列的名称。 分区键是某个表中分区的唯一标识符，分区键构成了实体主键的第一部分。 它是最大可为 1 KB 的字符串值。 |
| 行键 |包含行键的输出列的名称。 行键是分区中实体的唯一标识符。 行键构成了实体主键的第二部分。 行键是一个最大为 1 KB 的字符串值。 |
| 批大小 |批处理操作的记录数。 默认值 (100) 对大部分作业来说都已足够。 有关修改设置的详细信息，请参阅[表批处理操作规范](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table.tablebatchoperation)。 |

## <a name="partitioning"></a>分区

分区键为任何输出列。 输出写入器的数目按照 [完全并行化查询](stream-analytics-scale-jobs.md)的输入分区进行。

## <a name="output-batch-size"></a>输出批大小

有关最大消息大小，请参阅 [Azure 存储限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)。 默认值为每个单个事务100个实体，但你可以根据需要将其配置为较小的值。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure CLI 创建 Azure 流分析作业](quick-create-azure-cli.md)
* [快速入门：使用 ARM 模板创建 Azure 流分析作业](quick-create-azure-resource-manager.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)
