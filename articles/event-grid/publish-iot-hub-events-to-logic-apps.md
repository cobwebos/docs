---
title: 教程 - 使用 IoT 中心事件触发 Azure 逻辑应用
description: 本教程介绍如何使用 Azure 事件网格的事件路由服务创建自动化过程，用于根据 IoT 中心事件执行 Azure 逻辑应用操作。
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5092aa0b5b23f04af1f49933bca234815f03f454
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604518"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>教程：使用事件网格和逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知

使用 Azure 事件网格可以通过在下游业务应用程序中触发操作，对 IoT 中心内的事件做出反应。

本文逐步讲解一个使用 IoT 中心和事件网格的示例配置。 在本文末尾，我们将设置一个 Azure 逻辑应用，使其在每次将设备连接到 IoT 中心或与其断开连接时，都会发送一封通知电子邮件。 事件网格可用于及时通知关键设备断开连接。 指标和诊断可能需要几分钟（即 20 分钟或更长时间 -- 尽管我们不想给它加数字）才会在日志/警报中显示。 对于关键基础结构，这可能是不可接受的。

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅。 如果没有订阅，可以[创建一个免费 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。

* Azure 逻辑应用支持的任何电子邮件提供程序（例如 Office 365 Outlook 或 Outlook.com）中的电子邮件帐户。 此电子邮件帐户用于发送事件通知。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

可使用门户中的 Azure Cloud Shell 终端快速创建新的 IoT 中心。

1. 登录 [Azure 门户](https://portal.azure.com)。 

1. 在页面右上角选择“Cloud Shell”按钮。

   ![“Cloud Shell”按钮](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. 运行以下命令来创建新资源组：

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. 运行以下命令创建 IoT 中心：

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. 最小化 Cloud Shell 终端。 你将在本教程的后面部分返回到 Shell。

## <a name="create-a-logic-app"></a>创建逻辑应用

接下来创建一个逻辑应用，并添加一个 HTTP 事件网格触发器来处理来自 IoT 中心的请求。 

### <a name="create-a-logic-app-resource"></a>创建逻辑应用资源

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”，然后在搜索框中键入“逻辑应用”并选择“返回”。 从结果中选择“逻辑应用”。

   ![创建逻辑应用](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. 在下一个屏幕上选择“创建”。 

1. 为逻辑应用指定一个在订阅中唯一的名称，然后选择 IoT 中心所在的同一订阅、资源组和位置。 

   ![用于创建逻辑应用的字段](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. 选择“查看 + 创建”。

1. 验证设置，然后选择“创建”。

1. 创建资源后，选择“转到资源”。 

1. 在“逻辑应用设计器”中，下滑页面查看“模板”。 选择“空白逻辑应用”，以便可以从头开始构建逻辑应用。

### <a name="select-a-trigger"></a>选择触发器

触发器是启动逻辑应用的特定事件。 在本教程中，触发工作流的触发器通过 HTTP 接收请求。  

1. 在连接器和触发器搜索栏中，键入 **HTTP**。

1. 滚动浏览结果并选择“请求 - 当收到 HTTP 请求时”作为触发器。 

   ![选择 HTTP 请求触发器](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. 选择“使用示例有效负载生成架构”。 

   ![使用示例有效负载](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. 将“设备已连接事件架构”JSON 粘贴到文本框中，然后选择“完成”：

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   当设备连接到 IoT 中心时发布此事件。

> [!NOTE]
> 可能会收到一条弹出通知，其中指出，“请记住要在请求中包含设为 application/json 的内容类型标头”。 可以放心忽略此建议，并转到下一部分。 

### <a name="create-an-action"></a>创建操作

操作是在触发器启动逻辑应用工作流之后发生的任何步骤。 在本教程中，操作是从电子邮件提供程序发送电子邮件通知。 

1. 选择“新建步骤”。 随即会打开一个窗口，供用户“选择操作”。

1. 搜索“Outlook”。

1. 根据你的电子邮件提供程序，找到并选择匹配的连接器。 本教程使用 Outlook.com。 使用其他电子邮件提供程序时执行的步骤类似。 

   ![选择电子邮件提供程序连接器](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. 选择“发送电子邮件 (V2)”操作。**** 

1. 选择“登录”并登录到你的电子邮件帐户。 选择“是”，允许应用访问你的信息。

1. 生成电子邮件模板。 

   * **收件人**：输入接收通知电子邮件的电子邮件地址。 对于本教程，请使用你可以访问的电子邮件帐户进行测试。 

   * **使用者**：填写主题文本。 单击“主题”文本框时，可以选择要包含的动态内容。 例如，本教程使用 `IoT Hub alert: {eventType}`。 如果未看到“动态内容”，请选择“添加动态内容”超链接，该操作可以开启和关闭此超链接。

   * **正文**：编写电子邮件的文本。 在选择器工具中选择 JSON 属性，以包含基于事件数据的动态内容。 如果未看到“动态内容”，请选择“正文”文本框下的“添加动态内容”超链接 。 如果未看到所需字段，单击“动态内容”屏幕中的“更多”，以包含上一个操作中的字段。

   电子邮件模板可能如以下示例所示：

   ![填写电子邮件信息](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. 在逻辑应用设计器中选择“保存”。  

### <a name="copy-the-http-url"></a>复制 HTTP URL

在退出逻辑应用设计器之前，请复制逻辑应用要侦听的触发器 URL。 稍后要使用此 URL 来配置事件网格。 

1. 单击“当收到 HTTP 请求时”触发器配置框将其展开。 

1. 选择“HTTP POST URL”旁边的复制按钮复制其值。 

   ![复制 HTTP POST URL](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. 保存此 URL，以便在下一部分可以引用它。 

## <a name="configure-subscription-for-iot-hub-events"></a>为 IoT 中心事件配置订阅

本部分将 IoT 中心配置为在发生事件时发布事件。 

1. 在 Azure 门户中导航到 IoT 中心。 为此，可以选择“资源组”，然后选择本教程中的资源组，接着从资源列表中选择 IoT 中心。

1. 选择“事件”。

   ![打开事件网格详细信息](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. 选择“事件订阅”。 

   ![创建新的事件订阅](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. 使用以下值创建事件订阅： 

   1. 在“事件订阅详细信息”部分：
      1. 为事件订阅提供一个名称。 
      2. 对于“事件架构”，请选择“事件网格架构” 。 
   2. 在“主题详细信息”部分：
      1. 确认“主题类型”是否设置为“IoT 中心” 。 
      2. 确认 IoT 中心的名称是否设置为“源资源”字段的值。 
      3. 输入将创建的“系统主题”的名称。 要了解系统主题，请参阅[系统主题的概述](system-topics.md)。
   3. 在“事件类型”部分：
      1. 选择“筛选到事件类型”下拉列表。
      1. 取消选择“设备已创建”和“设备已删除”复选框，仅保留选中“连接已设备”和“设备已断开连接”复选框   。

         ![选择订阅事件类型](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. 在“终结点详细信息”部分： 
       1. 选择“终结点类型”作为“Web Hook” 。
       2. 单击“选择终结点”，粘贴从逻辑应用复制的 URL，然后确认选择。

         ![选择终结点 URL](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         完成后，窗格应如以下示例所示： 

         ![示例事件订阅窗体](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  选择“创建”。

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>模拟新设备连接并发送遥测数据

使用 Azure CLI 快速模拟设备连接来测试逻辑应用。 

1. 选择“Cloud Shell”按钮以重新打开终端。

1. 运行以下命令创建模拟设备标识：
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. 运行以下命令以模拟将设备连接到 IoT 中心并发送遥测数据：

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. 当模拟设备连接到 IoT 中心时，你将收到一封电子邮件，通知你“DeviceConnected”事件。

1. 当模拟完成时，你将收到一封电子邮件，通知你“DeviceDisconnected”事件。 

    ![示例警报邮件](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>清理资源

本教程使用的资源会在 Azure 订阅中产生费用。 学习完本教程并测试结果后，请禁用或删除不再想要保留的资源。 

若要删除在本教程中创建的所有资源，请删除资源组。 

1. 选择“资源组”，然后选择本教程中创建的资源组。

2. 在“资源组”页上，选择“删除资源组”。 系统将提示输入资源组名称，然后你可以将其删除。 同时会删除其中包含的所有资源。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[通过使用事件网格触发操作来对 IoT 中心事件做出反应](../iot-hub/iot-hub-event-grid.md)。
* [了解如何订阅设备已连接和已断开连接事件](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* 了解[事件网格](overview.md)的其他作用。

有关支持的逻辑应用连接器的完整列表，请参阅[连接器概述](/connectors/)。
