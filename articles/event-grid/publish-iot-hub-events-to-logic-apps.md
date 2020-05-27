---
title: 教程 - 使用 IoT 中心事件触发 Azure 逻辑应用
description: 本教程介绍如何使用 Azure 事件网格的事件路由服务创建自动化过程，用于根据 IoT 中心事件执行 Azure 逻辑应用操作。
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: robinsh
ms.openlocfilehash: 0b1870af6316713590eec59aee2af94ce34b7e1a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722552"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>教程：使用事件网格和逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知

使用 Azure 事件网格可以通过在下游业务应用程序中触发操作，对 IoT 中心内的事件做出反应。

本文逐步讲解一个使用 IoT 中心和事件网格的示例配置。 本文结束时，我们会完成设置一个 Azure 逻辑应用。每次将设备添加到 IoT 中心时，该应用就会发送一封通知电子邮件。 

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅。 如果没有订阅，可以[创建一个免费 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。

* Azure 逻辑应用支持的任何电子邮件提供程序（例如 Office 365 Outlook、Outlook.com 或 Gmail）中的电子邮件帐户。 此电子邮件帐户用于发送事件通知。 有关支持的逻辑应用连接器的完整列表，请参阅[连接器概述](https://docs.microsoft.com/connectors/)。

  > [!IMPORTANT]
  > 使用 Gmail 之前，请检查你是否有 G-Suite 商业帐户（带自定义域的电子邮件地址）或 Gmail 用户帐户（带 @gmail.com 或 @googlemail.com 的电子邮件地址）。 在逻辑应用中，只有 G-Suite 商业帐户可以不受限制地将 Gmail 连接器与其他连接器一起使用。 如果有 Gmail 用户帐户，则只能将 Gmail 连接器与 Google 批准的特定服务一起使用，也可以[创建用于身份验证的 Google 客户端应用](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application)。 有关详细信息，请参阅 [Azure 逻辑应用中 Google 连接器的数据安全和隐私策略](../connectors/connectors-google-data-security-privacy-policy.md)。

* Azure 中的 IoT 中心。 如果尚未创建 Iot 中心，请参阅 [IoT 中心入门](../iot-hub/iot-hub-csharp-csharp-getstarted.md)中的演练。

## <a name="create-a-logic-app"></a>创建逻辑应用

首先，创建逻辑应用并添加事件网格触发器，以便监视虚拟机的资源组。 

### <a name="create-a-logic-app-resource"></a>创建逻辑应用资源

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”，然后在搜索框中键入“逻辑应用”并选择“返回”  。 从结果中选择“逻辑应用”  。

   ![创建逻辑应用](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. 在下一个屏幕上选择“创建”  。 

1. 为逻辑应用指定一个在订阅中唯一的名称，然后选择 IoT 中心所在的同一订阅、资源组和位置。 

   ![用于创建逻辑应用的字段](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. 选择“创建”  。

1. 创建资源后，导航到你的逻辑应用。 为此，请选择“资源组”，然后选择本教程中创建的资源组  。 然后在资源列表中查找逻辑应用并选择。 

1. 在“逻辑应用设计器”中，下滑页面查看“模板”  。 选择“空白逻辑应用”，以便可以从头开始构建逻辑应用  。

### <a name="select-a-trigger"></a>选择触发器

触发器是启动逻辑应用的特定事件。 在本教程中，触发工作流的触发器通过 HTTP 接收请求。  

1. 在连接器和触发器搜索栏中，键入 **HTTP**。

1. 选择“请求 - 当收到 HTTP 请求时”作为触发器。  

   ![选择 HTTP 请求触发器](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. 选择“使用示例有效负载生成架构”。  

   ![选择 HTTP 请求触发器](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. 在文本框中粘贴以下示例 JSON 代码，然后选择“完成”： 

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
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
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

1. 可能会收到一条弹出通知，其中指出，“请记住要在请求中包含设为 application/json 的内容类型标头”。  可以放心忽略此建议，并转到下一部分。 

### <a name="create-an-action"></a>创建操作

操作是在触发器启动逻辑应用工作流之后发生的任何步骤。 在本教程中，操作是从电子邮件提供程序发送电子邮件通知。 

1. 选择“新建步骤”。  随即会打开一个窗口，供用户“选择操作”  。

1. 搜索“电子邮件”。 

1. 根据你的电子邮件提供程序，找到并选择匹配的连接器。 本教程使用 **Office 365 Outlook**。 使用其他电子邮件提供程序时执行的步骤类似。 

   ![选择电子邮件提供程序连接器](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

1. 选择“发送电子邮件”操作。  

1. 根据提示登录到电子邮件帐户。 

1. 生成电子邮件模板。 

   * **收件人**：输入接收通知电子邮件的电子邮件地址。 本教程使用你可以访问的测试电子邮件帐户。 

   * **使用者**：填写主题文本。 单击“主题”文本框时，可以选择要包含的动态内容。 例如，本教程使用 `IoT Hub alert: {event Type}`。 如果未看到“动态内容”，请选择“添加动态内容”超链接，该操作可以开启和关闭此超链接  。

   * **正文**：编写电子邮件的文本。 在选择器工具中选择 JSON 属性，以包含基于事件数据的动态内容。 如果未看到“动态内容”，请选择“正文”文本框下的“添加动态内容”超链接   。 如果未看到所需字段，单击“动态内容”屏幕中的“更多”，以包含上一个操作中的字段  。

   电子邮件模板可能如以下示例所示：

   ![填写电子邮件信息](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. 保存逻辑应用。 

### <a name="copy-the-http-url"></a>复制 HTTP URL

在退出逻辑应用设计器之前，请复制逻辑应用要侦听的触发器 URL。 稍后要使用此 URL 来配置事件网格。 

1. 单击“当收到 HTTP 请求时”触发器配置框将其展开。  

1. 选择“HTTP POST URL”旁边的复制按钮复制其值。  

   ![复制 HTTP POST URL](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. 保存此 URL，以便在下一部分可以引用它。 

## <a name="configure-subscription-for-iot-hub-events"></a>为 IoT 中心事件配置订阅

本部分将 IoT 中心配置为在发生事件时发布事件。 

1. 在 Azure 门户中导航到 IoT 中心。 为此，可以选择“资源组”，然后选择本教程中的资源组，接着从资源列表中选择 IoT 中心  。

2. 选择“事件”  。

   ![打开事件网格详细信息](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. 选择“事件订阅”。  

   ![创建新的事件订阅](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. 使用以下值创建事件订阅： 

   * **事件订阅详细信息**：提供一个说明性的名称，然后选择“事件网格架构”  。

   * **事件类型**：在“筛选事件类型”中，取消选中除“创建的设备”以外的所有选项   。

       ![订阅事件类型](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)

   * **终结点详细信息**：选择 Web Hook 作为“终结点类型”，然后选择“选择终结点”并粘贴从逻辑应用中复制的 URL，然后确认选择   。

     ![选择终结点 URL](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

   完成后，窗格应如以下示例所示： 

    ![示例事件订阅窗体](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. 可在此处保存事件订阅，并接收针对 IoT 中心内创建的每个设备发送的通知。 不过，在本教程中，我们将使用可选字段来筛选特定的设备。 在窗格顶部，选择“筛选器”  。

6. 选择“添加新筛选器”  。 在字段中填写以下值：

   * **密钥**：选择 `Subject`。

   * **操作员**：选择 `String begins with`。

   * **值**：输入 `devices/Building1_`，筛选建筑物 1 中的设备事件。
  
   在另一个筛选器中添加以下值：

   * **密钥**：选择 `Subject`。

   * **操作员**：选择 `String ends with`。

   * **值**：输入 `_Temperature`，筛选与温度相关的设备事件。

   事件订阅的“筛选器”选项卡现在应如下图所示  ：

   ![向事件订阅添加筛选器](./media/publish-iot-hub-events-to-logic-apps/event-subscription-filters.png)

7. 选择“创建”保存事件订阅。 

## <a name="create-a-new-device"></a>创建新设备

创建新设备来触发事件通知电子邮件，以测试逻辑应用。 

1. 在 IoT 中心选择“IoT 设备”。  

2. 选择“新建”  。

3. 对于“设备 ID”，请输入 `Building1_Floor1_Room1_Light`。 

4. 选择“保存”。  

5. 可以添加具有不同设备 ID 的多个设备来测试事件订阅筛选器。 尝试以下示例： 

   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

   如果添加了四个示例，IoT 设备列表应如下图所示：

   ![IoT 中心设备列表](./media/publish-iot-hub-events-to-logic-apps/iot-hub-device-list.png)

6. 将一些设备添加到 IoT 中心后，请检查电子邮件，确定哪些设备触发了逻辑应用。 

## <a name="use-the-azure-cli"></a>使用 Azure CLI

如果不使用 Azure 门户，也可以使用 Azure CLI 来完成 IoT 中心相关的步骤。 有关详细信息，请参阅有关使用 Azure CLI [创建事件订阅](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription)和[创建 IoT 设备](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity)的网页。

## <a name="clean-up-resources"></a>清理资源

本教程使用的资源会在 Azure 订阅中产生费用。 学习完本教程并测试结果后，请禁用或删除不再想要保留的资源。 

若要删除在本教程中创建的所有资源，请删除资源组。 

1. 选择“资源组”，然后选择本教程中创建的资源组  。

2. 在“资源组”页上，选择“删除资源组”  。 系统将提示输入资源组名称，然后你可以将其删除。 同时会删除其中包含的所有资源。

如果不想删除所有资源，可以逐个进行管理。 

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

* 详细了解如何[通过使用事件网格触发操作来对 IoT 中心事件做出反应](../iot-hub/iot-hub-event-grid.md)。
* [了解如何订阅设备已连接和已断开连接事件](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* 了解[事件网格](overview.md)的其他作用。