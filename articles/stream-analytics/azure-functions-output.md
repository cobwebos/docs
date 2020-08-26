---
title: 从 Azure 流分析 Azure Functions 输出
description: 本文介绍 azure 函数作为 Azure 流分析的输出。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a8fbf9f2890ef169016108db61c93bfe6dd44326
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875587"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>从 Azure 流分析 Azure Functions 输出

Azure Functions 是一个无服务器计算服务，可以使用它按需运行代码，而无需显式预配或管理基础结构。 它允许实现由 Azure 或合作伙伴服务中出现的事件所触发的代码。 Azure Functions 响应触发的这一功能使其成为 Azure 流分析的自然输出。 此输出适配器允许用户将流分析连接到 Azure Functions，并运行脚本或一段代码来响应各种事件。

流分析中的 Azure Functions 输出当前不可在 Azure 中国世纪互联和 Azure 德国 (T-Systems International) 区域中使用。

Azure 流分析通过 HTTP 触发器调用 Azure Functions。 提供具有以下可配置属性的 Azure Functions 输出适配器：

| 属性名称 | 说明 |
| --- | --- |
| 函数应用 |Azure Functions 应用的名称。 |
| 函数 |Azure Functions 应用中的函数的名称。 |
| 密钥 |若要使用其他订阅中的 Azure 函数，可提供用于访问该函数的密钥。 |
| 最大批大小 |此属性可用于设置将发送到 Azure 函数的每个输出批的最大大小。 输入单元以字节为单位。 默认情况下，此值为 262,144 字节 (256 KB)。 |
| 最大批数  |一个用于指定发送到 Azure Functions 的每个批中的最大事件数的属性。 默认值为 100。 |

对于已成功处理的批，Azure 流分析预期函数应用中的 HTTP 状态为 200。

当 Azure 流分析从 Azure 函数中收到 413（“http 请求实体过大”）异常时，它将减小发送到 Azure Functions 的批的大小。 在 Azure 函数代码中，使用此异常以确保 Azure 流分析不会发送过大的批。 此外，确保函数中使用的最大批数和最大批大小值与在流分析门户中输入的值一致。

> [!NOTE]
> 在测试连接过程中，流分析会将空批发送到 Azure Functions，以测试两者之间的连接是否正常。 确保 Functions 应用处理空批请求，以确保通过连接测试。

另外，如果时间窗口中没有任何事件登录，则不生成任何输出。 因此，不会调用 **computeResult** 函数。 此行为与内置窗口化聚合函数一致。

## <a name="partitioning"></a>分区

分区键基于查询中的 PARTITION BY 子句。 输出写入器的数目按照 [完全并行化查询](stream-analytics-scale-jobs.md)的输入分区进行。

## <a name="output-batch-size"></a>输出批大小

默认批大小为 262,144 字节 (256 KB)。 每批的默认事件计数为 100。 批大小是可配置的，可在流分析输出选项中增大或减小。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure CLI 创建 Azure 流分析作业](quick-create-azure-cli.md)
* [快速入门：使用 ARM 模板创建 Azure 流分析作业](quick-create-azure-resource-manager.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-vs-code.md)