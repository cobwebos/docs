---
title: 使用 Azure Monitor 创建 Azure Cosmos DB 的警报
description: 了解如何使用 Azure Monitor 为 Azure Cosmos DB 设置警报。
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: 84c2ad3a24d944db6a55f3f21e8a2a0c4084d033
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096627"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>使用 Azure Monitor 创建 Azure Cosmos DB 的警报

警报用于设置重复测试，以监视 Azure Cosmos DB 资源的可用性和响应能力。 警报可以通过电子邮件的形式向你发送通知，或在某个指标达到阈值或在活动日志中记录特定事件时执行 Azure Function。

你可以基于指标或 Azure Cosmos 帐户上的活动日志事件接收警报：

* **指标**-当指定指标的值超过分配的阈值时，会触发警报。 例如，使用的请求单位总数超过 1000 RU/秒。 当首次满足条件时，以及之后不再满足条件时，将触发此警报。 请参阅[监视数据参考](monitor-cosmos-db-reference.md#metrics)文章了解 Azure Cosmos DB 中提供的不同指标。

* **活动日志事件**–当发生特定事件时，会触发此警报。 例如，在访问或刷新 Azure Cosmos 帐户的密钥时。

你可以从 "Azure Cosmos DB" 窗格或 "Azure 门户" 中的 "Azure Monitor 服务" 设置警报。 这两个接口提供相同的选项。 本文说明如何使用 Azure Monitor 为 Azure Cosmos DB 设置警报。

## <a name="create-an-alert-rule"></a>创建警报规则

本部分说明如何在收到 HTTP 状态代码429时创建警报，请求会按速率限制。 例如，你可能希望在存在100或更多速率限制请求时接收警报。 本文说明如何使用 HTTP 状态代码为此类方案配置警报。 你还可以使用类似的步骤来配置其他类型的警报，你只需根据需求选择其他条件。

1. 登录到[Azure 门户。](https://portal.azure.com/)

1. 从左侧导航栏中选择 "**监视器**"，然后选择 "**警报**"。

1. 选择 "新建警报规则" 按钮，打开 "创建警报规则" 窗格。  

1. 填写**范围**部分：

   * 打开 "**选择资源**" 窗格，并配置以下各项：

   * 选择**订阅**名称。

   * 为**资源类型**选择 " **Azure Cosmos DB 帐户**"。

   * Azure Cosmos 帐户的**位置**。

   * 填写详细信息后，将显示所选作用域中的 Azure Cosmos 帐户列表。 选择要为其配置警报的用户，然后选择 "**完成**"。

1. 填写**条件**部分：

   * 打开 "**选择条件**" 窗格，打开 "**配置信号逻辑**" 页，并配置以下各项：

   * 选择一个信号。 **信号类型**可以是**度量值**或**活动日志**。 为此方案选择**指标**。 由于在请求单位总数方面存在速率限制问题，因此你希望收到警报。

   * 选择**监视服务**的**全部**

   * 选择**信号名称**。 若要获取 HTTP 状态代码的警报，请选择 "**请求单位总数**" 信号。

   * 在下一个选项卡中，你可以定义用于触发警报的逻辑，并使用图表查看 Azure Cosmos 帐户的趋势。 "**请求单位总数**" 指标支持维度。 这些维度允许你筛选指标。 如果未选择任何维度，则忽略此值。

   * 选择**StatusCode**作为**维度名称**。 选择 "**添加自定义值**" 并将状态代码设置为429。

   * 在**警报逻辑**中，将**阈值**设置为**Static**。 静态阈值使用用户定义的阈值来评估规则，而动态阈值使用内置机器学习算法持续了解指标行为模式并自动计算阈值。

   * 将**运算符**设置为 "**大于**"，将 "**聚合类型**" 设置为 "**总计**"，将 "**阈值**" 设置为 " **100**"。 使用此逻辑时，如果客户端发现超过100个429请求，则会触发警报。 你还可以根据你的需求配置聚合类型、聚合粒度和评估频率。

   * 填充窗体后，选择 "**完成**"。 以下屏幕截图显示了警报逻辑的详细信息：

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="配置逻辑以接收速率限制/429 请求的警报":::

1. 填写 "**操作组**" 部分：

   * 在 "**创建规则**" 窗格中，选择一个现有的操作组，或者创建一个新的操作组。 操作组用于定义在发生警报条件时要执行的操作。 在此示例中，创建一个新的操作组，以便在触发警报时接收电子邮件通知。 打开 "**添加操作组**" 窗格，并填写以下详细信息：

   * **操作组名称**-操作组名称在资源组中必须是唯一的。

   * **短名称**-操作组的短名称，此值包含在电子邮件和短信通知中，用于识别哪个操作组是通知的源。

   * 选择要在其中创建此操作组的订阅和资源组。  

   * 提供操作的名称，并选择 "**电子邮件/短信/推送/语音**" 作为 "**操作类型**"。 以下屏幕截图显示了操作类型的详细信息：

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="配置操作类型（如电子邮件通知）以接收警报":::

1. 填写 "**警报规则详细信息**" 部分：

   * 定义规则的名称，提供可选描述、警报的严重性级别，选择是否在创建规则时启用规则，然后选择 "**创建规则警报**" 以创建指标规则警报。

创建警报后，它将在10分钟内处于活动状态。

## <a name="common-alerting-scenarios"></a>常见警报方案

以下是可以使用警报的一些方案：

* 更新 Azure Cosmos 帐户的密钥。
* 当容器、数据库或区域的数据或索引使用情况超过特定字节数时。
* 当规范化的 RU/s 消耗量大于特定百分比时。 规范化 RU 消耗指标提供副本集内的最大吞吐量使用率。 若要了解，请参阅[如何监视规范化 RU/秒](monitor-normalized-request-units.md)。  
* 添加、删除或脱机时的区域。
* 创建、删除或更新数据库或容器时。
* 当数据库的吞吐量或容器发生更改时。

## <a name="next-steps"></a>后续步骤

* 如何监视 Azure Cosmos 容器中[规范化的 RU/s 指标](monitor-normalized-request-units.md)。
* 如何监视 Azure Cosmos DB 中操作的[吞吐量或请求单元使用量](monitor-request-unit-usage.md)。