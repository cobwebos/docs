---
title: 连接到 Azure 事件中心 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Azure 事件中心和 Azure 逻辑应用管理和监视事件
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 86fc1c3542bea1be840041bb73df15631c066c7e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294966"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>使用 Azure 事件中心和 Azure 逻辑应用监视、接收和发送事件 

本文演示如何使用 Azure 事件中心连接器监视和管理从逻辑应用发送到 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)的事件。 通过这样的方式可以创建逻辑应用，从事件中心自动执行检查、发送和接收事件的任务和工作流。 

如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。
如需特定于连接器的技术信息，请参阅 <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">Azure 事件中心连接器参考</a>。

## <a name="prerequisites"></a>先决条件

* [Azure 事件中心命名空间和事件中心](../event-hubs/event-hubs-create.md)

* 要在其中访问事件中心的逻辑应用。 若要通过 Azure 事件中心触发器启动逻辑应用，需要一个[空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>检查权限并获取连接字符串

要使逻辑应用能够访问事件中心，请检查权限并获取事件中心命名空间的连接字符串。

1. 登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。 

2. 转到事件中心命名空间，而不是特定的事件中心。 在命名空间页上，在“设置”下，选择“共享访问策略”。 在“声明”下，检查你是否有该命名空间的“管理”权限。

   ![管理事件中心命名空间的权限](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. 如果想要以后手动输入连接信息，请获取事件中心命名空间的连接字符串。 

   1. 在“策略”下，选择“RootManageSharedAccessKey”。 

   2. 查找主键的连接字符串。 选择复制按钮，并保存连接字符串供以后使用。

      ![复制事件中心命名空间连接字符串](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > 若要确认连接字符串是与事件中心命名空间关联，还是与特定的事件中心关联，请确保该连接字符串不具有 `EntityPath` 参数。 如果找到了该参数，则说明连接字符串适用于特定的事件中心“实体”，不是适用于逻辑应用的正确字符串。

4. 现在继续[添加事件中心触发器](#add-trigger)或[添加事件中心操作](#add-action)。

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>添加事件中心触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

此示例演示在将新事件发送到事件中心时，如何启动逻辑应用工作流。 

1. 在 Azure 门户或 Visual Studio 中创建一个空白的逻辑应用，以便打开逻辑应用设计器。 此示例使用 Azure 门户。

2. 在搜索框中，输入“事件中心”作为筛选器。 在触发器列表中，选择所需的触发器。 

   此示例使用此触发器：“事件中心 - 事件在事件中心可用时”

   ![选择触发器](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. 如果系统提示输入连接详细信息，请[立即创建事件中心连接](#create-connection)。 或者，如果连接已存在，请提供触发器所需的信息。

   1. 从“事件中心名称”列表中，选择要监视的事件中心。

      ![指定事件中心或使用者组](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. 选择希望触发器检查事件中心的时间间隔和频率。
 
   3. （可选）若要选择部分高级的触发器选项，请选择“显示高级选项”。
   
      ![触发高级选项](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | 属性 | 详细信息 | 
      |----------|---------| 
      | 内容类型  | 选择事件的内容类型。 默认为“application/octet-stream”。 |
      | 内容架构 | 对于从事件中心读取的事件，请输入 JSON 格式的内容架构。 |
      | 使用者组名称 | 若要读取事件，请输入事件中心[使用者组名称](../event-hubs/event-hubs-features.md#consumer-groups)。 如果未指定，则使用默认使用者组。 |
      | 最小分区键 | 输入要读取的最小[分区](../event-hubs/event-hubs-features.md#partitions) ID。 默认读取所有分区。 |
      | 最大分区键 | 输入要读取的最大[分区](../event-hubs/event-hubs-features.md#partitions) ID。 默认读取所有分区。 |
      | 最大事件计数 | 输入一个表示最大事件数的值。 触发器返回的事件数至少为 1，至多为此属性指定的事件数。 |
      |||

4. 完成后，请在设计器工具栏上选择“保存”。

5. 现在请继续向逻辑应用添加一个或多个操作，以便完成需对触发器结果执行的任务。

> [!NOTE]
> 所有事件中心触发器都是长轮询触发器，这意味着当触发器触发时，触发器将处理所有事件，然后等待 30 秒，让更多事件出现在事件中心。
> 如果在 30 秒内未收到事件，则会跳过触发器运行。 否则，该触发器将继续读取事件，直到事件中心为空。
> 下一次触发器轮询的发生将基于触发器的属性中指定的重复周期间隔。

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>添加事件中心操作

在 Azure 逻辑应用中，[操作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是指工作流中紧跟在某个触发器或另一操作后面执行的一个步骤。 对于此示例，逻辑应用从事件中心触发器开始，该触发器检查事件中心中的新事件。 

1. 在 Azure 门户或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 此示例使用 Azure 门户。

2. 在该触发器或操作下，选择“新建步骤” > “添加操作”。

   若要在现有步骤之间添加操作，请将鼠标移到连接箭头上方。 
   选择出现的加号 (**+**)，然后选择“添加操作”。

3. 在搜索框中，输入“事件中心”作为筛选器。
从操作列表中选择所需的操作。 

   对于此示例，请选择此操作：“事件中心 - 发送事件”

   ![选择“事件中心 - 发送事件”](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. 如果系统提示输入连接详细信息，请[立即创建事件中心连接](#create-connection)。 或者，如果连接已存在，请提供操作所需的信息。

   | 属性 | 必选 | 说明 | 
   |----------|----------|-------------|
   | 事件中心名称 | 是 | 选择要发送事件的事件中心 | 
   | 事件内容 | 否 | 要发送的事件内容 | 
   | 属性 | 否 | 要发送的应用属性和值 | 
   |||| 

   例如： 

   ![选择事件中心名称并提供事件内容](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. 完成后，请在设计器工具栏上选择“保存”。

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>连接到事件中心

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. 系统提示输入连接信息时，请提供以下详细信息：

   | 属性 | 必选 | 值 | 说明 | 
   |----------|----------|-------|-------------|
   | 连接名称 | 是 | <connection-name> | 为连接创建的名称 |
   | 事件中心命名空间 | 是 | <event-hubs-namespace> | 选择要使用的事件中心命名空间。 | 
   |||||  

   例如：

   ![创建事件中心连接](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   若要手动输入连接字符串，请选择“手动输入连接信息”。 
   了解[如何查找连接字符串](#permissions-connection-string)。

2. 如果尚未选择，请选择要使用的事件中心。 选择“创建”。

   ![创建事件中心连接（第 2 部分）](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. 创建连接后，继续[添加事件中心触发器](#add-trigger)或[添加事件中心操作](#add-action)。

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 Swagger 文件所述），请查看[连接器的参考页](/connectors/eventhubs/)。 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)