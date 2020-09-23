---
title: 从 Azure 流分析 Power BI 输出
description: 本文介绍如何将数据从 Azure 流分析输出到 Power BI。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d398cfe063dbbb2bc87a3debf1669afa6a16b43e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891987"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>从 Azure 流分析 Power BI 输出

[Power BI](https://powerbi.microsoft.com/) 可以用作流分析作业的输出，以便提供丰富的分析结果可视化体验。 此功能可用于操作仪表板、生成报告以及进行指标驱动型报告。

流分析中的 Power BI 输出当前不可在 Azure 中国世纪互联和 Azure 德国 (T-Systems International) 区域中使用。

## <a name="output-configuration"></a>输出配置

下表列出了用于配置 Power BI 输出的属性名称及其说明。

| 属性名称 | 说明 |
| --- | --- |
| 输出别名 |提供了一个在查询中使用的易记名称，用于将查询输出定向到此 Power BI 输出。 |
| 组工作区 |若要与其他 Power BI 用户启用共享数据，可以在 Power BI 帐户中选择组。如果不想写入组，可以选择“我的工作区”。 更新现有组需要对 Power BI 重新进行身份验证。 |
| 数据集名称 |提供数据集名称，供 Power BI 输出使用。 |
| 表名称 |在 Power BI 输出的数据集下提供表名称。 目前，流分析作业的 Power BI 输出在 1 个数据集中只能有 1 个表。 |
| 授权连接 | 你需要进行 Power BI 授权，才能配置输出设置。 授予此输出对 Power BI 仪表板的访问权限后，可以通过更改用户帐户密码、删除作业输出或删除流分析作业，来撤销访问权限。 | 

有关配置 Power BI 输出和仪表板的演练，请参阅 [Azure 流分析和 Power BI](stream-analytics-power-bi-dashboard.md) 教程。

> [!NOTE]
> 不要在 Power BI 仪表板中显式创建数据集和表。 当作业启动并且开始将输出抽取到 Power BI 中时，会自动填充数据集和表。 如果作业查询没有生成任何结果，则不会创建数据集和表。 如果 Power BI 已经有一个数据集和表，且与流分析作业中提供的数据集和表同名，则会覆盖现有数据。
>

### <a name="create-a-schema"></a>创建架构

Azure 流分析会为用户创建一个 Power BI 数据集和表（如果不存在）。 在所有其他情况下，表可以更新使用新值。 目前，一个数据集内只能有一个表。 

Power BI 使用先入先出 (FIFO) 保留策略。 数据将收集在表中，直到它达到 200,000 行。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>将数据类型从流分析转换到 Power BI

如果输出架构更改，Azure 流分析会在运行时动态更新数据模型。 列名称更改、列类型更改，以及添加或删除列，这些都会进行跟踪。

此表介绍了在不存在 Power BI 数据集和表的情况下，如何将数据类型从[流分析数据类型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)转换为 Power BI 的[实体数据模型 (EDM) 类型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)。

从流分析 | 到 Power BI
-----|-----
bigint | Int64
nvarchar(max) | 字符串
datetime | Datetime
FLOAT | Double
记录数组 | 字符串类型，常量值“IRecord”或“IArray”

### <a name="update-the-schema"></a>更新架构

流分析根据输出中的第一组事件推断数据模型架构。 以后会根据需要对数据模型架构进行更新，使之适合那些不符合原始架构的传入事件。

避免使用 `SELECT *` 查询，防止跨行进行动态架构更新。 除了潜在的性能影响，它还可能导致生成结果所需的时间不确定。 对那些需要显示在 Power BI 仪表板上的具体字段进行选择。 此外，数据值应与所选数据类型相符。

以前/当前 | Int64 | 字符串 | Datetime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | 字符串 | 字符串 | Double
Double | Double | 字符串 | 字符串 | Double
字符串 | 字符串 | 字符串 | 字符串 | 字符串 
Datetime | 字符串 | 字符串 |  Datetime | 字符串

## <a name="output-batch-size"></a>输出批大小

有关输出批大小，请参阅 [Power BI REST API 限制](https://msdn.microsoft.com/library/dn950053.aspx)。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure CLI 创建 Azure 流分析作业](quick-create-azure-cli.md)
* [快速入门：使用 ARM 模板创建 Azure 流分析作业](quick-create-azure-resource-manager.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)
