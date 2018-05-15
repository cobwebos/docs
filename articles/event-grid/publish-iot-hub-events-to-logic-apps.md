---
title: 使用 IoT 中心事件触发 Azure 逻辑应用 | Microsoft Docs
description: 使用 Azure 事件网格的事件路由服务创建自动化过程，用于根据 IoT 中心事件执行 Azure 逻辑应用操作。
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 4fed42a45f8d291bd3ba1e4fd5d636b7d0b0fbfc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件

使用 Azure 事件网格可以通过在下游业务应用程序中触发操作，对 IoT 中心内的事件做出反应。

本文逐步讲解一个使用 IoT 中心和事件网格的示例配置。 本文结束时，我们会完成设置一个 Azure 逻辑应用。每次将设备添加到 IoT 中心时，该应用就会发送一封通知电子邮件。 

## <a name="prerequisites"></a>先决条件

* 受 Azure 逻辑应用支持的任何电子邮件提供程序（例如 Office 365 Outlook、Outlook.com 或 Gmail）中的电子邮件帐户。 此电子邮件帐户用于发送事件通知。 有关支持的逻辑应用连接器的完整列表，请参阅[连接器概述](https://docs.microsoft.com/connectors/)
* 有效的 Azure 帐户。 如果没有帐户，可以[创建一个免费帐户](http://azure.microsoft.com/pricing/free-trial/)。
* Azure 中的 Iot 中心。 如果尚未创建 Iot 中心，请参阅 [IoT 中心入门](../iot-hub/iot-hub-csharp-csharp-getstarted.md)中的演练。 

## <a name="create-a-logic-app"></a>创建逻辑应用

首先，创建逻辑应用并添加事件网格触发器，以便监视虚拟机的资源组。 

### <a name="create-a-logic-app-resource"></a>创建逻辑应用资源


1. 在 [Azure 门户](https://portal.azure.com)中，选择“新建” > “企业集成” > “逻辑应用”。

   ![创建逻辑应用](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. 为逻辑应用指定一个在订阅中唯一的名称，然后选择 IoT 中心所在的同一订阅、资源组和位置。 
3. 准备就绪后，请选择“固定到仪表板”，并选择“创建”。

   你现在已为逻辑应用程序创建 Azure 资源。 在 Azure 部署逻辑应用后，逻辑应用设计器会显示针对常用模式的模板，以便你可以更快地入门。

   > [!NOTE] 
   > 选择“固定到仪表板”时，逻辑应用会在逻辑应用设计器中自动打开。 否则，你可以手动查找和打开逻辑应用。

4. 在逻辑应用设计器中的“模板”下选择“空白逻辑应用”，以便从头开始生成逻辑应用。

## <a name="select-a-trigger"></a>选择触发器

触发器是启动逻辑应用的特定事件。 在本教程中，触发工作流的触发器通过 HTTP 接收请求。  

1. 在连接器和触发器搜索栏中，键入 **HTTP**。
2. 选择“请求 - 当收到 HTTP 请求时”作为触发器。 

   ![选择 HTTP 请求触发器](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. 选择“使用示例有效负载生成架构”。 

   ![选择 HTTP 请求触发器](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. 在文本框中粘贴以下示例 JSON 代码，然后选择“完成”：

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<IoT hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice",
       "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
       "opType": "DeviceCreated"
     },
     "dataVersion": "",
     "metadataVersion": "1"
   }]
   ```
5. 可能会收到一条弹出通知，其中指出，“请记住要在请求中包含设为 application/json 的内容类型标头”。 可以放心忽略此建议，并转到下一部分。 


### <a name="create-an-action"></a>创建操作

操作是在触发器启动逻辑应用工作流之后发生的任何步骤。 在本教程中，操作是从电子邮件提供程序发送电子邮件通知。 

1. 依次选择“新建步骤”、“添加操作”。 

   ![“新建步骤”、“添加操作”](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. 搜索“电子邮件”。 
3. 根据你的电子邮件提供程序，找到并选择匹配的连接器。 本教程使用 **Office 365 Outlook**。 使用其他电子邮件提供程序时执行的步骤类似。 

   ![选择电子邮件提供程序连接器](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. 选择“发送电子邮件”操作。 
5. 根据提示登录到电子邮件帐户。 
6. 生成电子邮件模板。 
   * **收件人**：输入接收通知电子邮件的电子邮件地址。 本教程使用你可以访问的测试电子邮件帐户。 
   * **主题**和**正文**：编写电子邮件的文本。 在选择器工具中选择 JSON 属性，以包含基于事件数据的动态内容。  

   电子邮件模板可能如以下示例所示：

   ![填写电子邮件信息](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. 保存逻辑应用。 

### <a name="copy-the-http-url"></a>复制 HTTP URL

在退出逻辑应用设计器之前，请复制逻辑应用要侦听的触发器 URL。 稍后要使用此 URL 来配置事件网格。 

1. 单击“当收到 HTTP 请求时”触发器配置框将其展开。 
2. 选择“HTTP POST URL”旁边的复制按钮复制其值。 

   ![复制 HTTP POST URL](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. 保存此 URL，以便在下一部分可以引用它。 

## <a name="publish-an-event-from-iot-hub"></a>从 IoT 中心发布事件

本部分将 IoT 中心配置为在发生事件时发布事件。 

1. 在 Azure 门户中导航到 IoT 中心。 
2. 选择“事件”。

   ![打开事件网格详细信息](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. 选择“事件订阅”。 

   ![创建新的事件订阅](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. 使用以下值创建事件订阅： 
   * **名称**：提供描述性的名称。
   * **订阅所有事件类型**：取消选中该复选框。
   * **事件类型**：选择“DeviceCreated”。
   * **订阅方类型**：选择“Webhook”。
   * **订阅方终结点**：粘贴已从逻辑应用复制的 URL。 

   可在此处保存事件订阅，并接收针对 IoT 中心内创建的每个设备发送的通知。 不过，在本教程中，我们将使用可选字段来筛选特定的设备： 

   * **前缀筛选器**：输入 `devices/Building1_`，筛选建筑物 1 中的设备事件。
   * **后缀筛选器**：输入 `_Temperature`，筛选与温度相关的设备事件。

   完成后，窗体应如以下示例所示： 

   ![示例事件订阅窗体](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. 选择“创建”保存事件订阅。

## <a name="create-a-new-device"></a>创建新设备

创建新设备来触发事件通知电子邮件，以测试逻辑应用。 

1. 在 IoT 中心选择“IoT 设备”。 
2. 选择 **添加** 。
3. 对于“设备 ID”，请输入 `Building1_Floor1_Room1_Temperature`。
4. 选择“保存”。 
5. 可以添加具有不同设备 ID 的多个设备来测试事件订阅筛选器。 尝试以下示例： 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

将一些设备添加到 IoT 中心后，请检查电子邮件，确定哪些设备触发了逻辑应用。 

## <a name="use-the-azure-cli"></a>使用 Azure CLI

如果不使用 Azure 门户，也可以使用 Azure CLI 来完成 IoT 中心相关的步骤。 有关详细信息，请参阅有关使用 Azure CLI [创建事件订阅](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription)和[创建 IoT 设备](https://docs.microsoft.com/cli/azure/iot/device)的网页

## <a name="clean-up-resources"></a>清理资源

本教程使用的资源会在 Azure 订阅中产生费用。 学习完本教程并测试结果后，请禁用或删除不再想要保留的资源。 

如果不希望丢弃针对逻辑应用所执行的操作，可以禁用逻辑应用，但不要将其删除。 

1. 导航到逻辑应用。
2. 在“概述”边栏选项卡上，选择“删除”或“禁用”。 

每个订阅可以包含一个免费 IoT 中心。 如果在本教程中创建了一个免费中心，则不需要将其删除，以免产生费用。

1. 导航到 IoT 中心。 
2. 在“概览”边栏选项卡上，选择“删除”。 

即使保留了 IoT 中心中，你也仍可能想要删除创建的事件订阅。 

1. 在 IoT 中心，选择“事件网格”。
2. 选择要删除的事件订阅。 
3. 选择“删除”。 

## <a name="next-steps"></a>后续步骤

详细了解如何[通过使用事件网格触发操作来对 IoT 中心事件做出反应](../iot-hub/iot-hub-event-grid.md)。

了解[事件网格](overview.md)的其他作用。


