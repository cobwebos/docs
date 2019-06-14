---
title: 通过 Azure 逻辑应用进行 IoT 远程监视并发送通知 | Microsoft Docs
description: 使用 Azure 逻辑应用在 IoT 中心内监视 IoT 温度，并针对检测到的任何异常自动将电子邮件通知发送到邮箱。
author: robinsh
keywords: IoT 监视, IoT 通知, IoT 温度监视
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719341"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>通过连接 IoT 中心和邮箱的 Azure 逻辑应用进行 IoT 远程监视并发送通知

![端到端关系图](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)可帮助你协调跨一个或多个企业中的本地和云服务以及跨各种协议的工作流。 逻辑应用以触发器开始，后接一个或多个可以使用内置控制机制（例如条件和迭代器）定序的操作。 这种灵活性使得逻辑应用成为 IoT 监视方案的理想 IoT 解决方案。 例如，设备将遥测数据发送到 IoT 中心终结点后，可以启动逻辑应用工作流以便在 Azure 存储 Blob 中存储数据、发送电子邮件警报来警示出现数据异常、在设备报告故障时安排技术人员上门解决，等等。

## <a name="what-you-learn"></a>学习内容

了解如何创建连接 IoT 中心和邮箱的逻辑应用，以监视温度并发送通知。

每当设备上运行的客户端代码将遥测消息发送到 IoT 中心时，都会设置应用程序属性 `temperatureAlert`。 当客户端代码检测到温度超过 30 摄氏度时，会将此属性设置为 `true`；否则将此属性设置为 `false`。

在本主题中，你将在 IoT 中心设置路由，以便在 `temperatureAlert = true` 时将消息发送到服务总线终结点；另外，你将设置一个在消息抵达服务总线终结点时触发并发送电子邮件通知的逻辑应用。

## <a name="what-you-do"></a>准备工作

* 创建服务总线命名空间，并向其添加服务总线队列。
* 将自定义终结点和路由规则添加到 IoT 中心，以将包含温度警报的消息路由到服务总线队列。
* 创建、配置并测试一个逻辑应用，以使用来自服务总线队列的消息，并将通知电子邮件发送到所需的收件人。

## <a name="what-you-need"></a>所需条件

* 完成 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程；例如[将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这包括以下要求：

  * 一个有效的 Azure 订阅。
  * 已在订阅中创建一个 Azure IoT 中心。
  * 设备上正在运行一个可将遥测消息发送到 Azure IoT 中心的客户端应用程序。

## <a name="create-service-bus-namespace-and-queue"></a>创建服务总线命名空间和队列

创建服务总线命名空间和队列。 在本主题的稍后部分，你将在 IoT 中心创建一个路由规则，以将包含温度警报的消息定向到服务总线队列，而某个逻辑应用将在该队列中拾取这些消息，并触发其中的警报来发送通知电子邮件。

### <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

1. 上[Azure 门户](https://portal.azure.com/)，选择 **+ 创建资源** > **集成** > **服务总线**。

1. 在“创建命名空间”窗格中提供以下信息： 

   **名称**：服务总线命名空间的名称。 该命名空间必须在整个 Azure 中唯一。

   **定价层**：从下拉列表中选择“基本”。  基本层对本教程而言就已足够。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：使用 IoT 中心所用的同一位置。

   ![在 Azure 门户中创建服务总线命名空间](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. 选择“创建”  。 等待部署完成，然后转到下一步。

### <a name="add-a-service-bus-queue-to-the-namespace"></a>将服务总线队列添加到命名空间

1. 打开服务总线命名空间。 转到服务总线命名空间的最简单方法是从资源窗格中选择“资源组”，选择你的资源组，然后从资源列表中选择该服务总线命名空间。 

1. 在“服务总线命名空间”窗格中，选择“+ 队列”。  

1. 为队列输入一个名称，然后选择“创建”  。 成功创建队列后，“创建队列”窗格会关闭。 

   ![在 Azure 门户中添加服务总线队列](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. 返回“服务总线命名空间”窗格，在“实体”下，选择“队列”。    从列表中打开服务总线队列，然后选择“共享访问策略” > “+ 添加”。  

1. 为策略输入一个名称，选中“管理”  ，然后选择“创建”  。

   ![在 Azure 门户中添加服务总线队列策略](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>将自定义终结点和路由规则添加到 IoT 中心

将服务总线队列的自定义终结点添加到 IoT 中心，并创建一个消息路由规则，以将包含温度警报的消息定向到该终结点，而逻辑应用将在该终结点中拾取这些消息。 路由规则使用路由查询 `temperatureAlert = "true"`，根据设备上运行的客户端代码设置的 `temperatureAlert` 应用程序属性值来转发消息。 有关详细信息，请参阅[基于消息属性的消息路由查询](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)。

### <a name="add-a-custom-endpoint"></a>添加自定义终结点

1. 打开 IoT 中心 转到 IoT 中心的最简单方法是从资源窗格中选择“资源组”，选择你的资源组，然后从资源列表中选择该 IoT 中心。 

1. 在“消息传送”下，选择“消息路由”。   在“消息路由”窗格中选择“自定义终结点”选项卡，然后选择“+ 添加”。    从下拉列表中选择“服务总线队列”。 

   ![在 Azure 门户中，将终结点添加到 IoT 中心](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. 在“添加服务总线终结点”窗格中输入以下信息： 

   **终结点名称**：终结点的名称。

   **服务总线命名空间**：选择你创建的命名空间。

   **服务总线队列**:选择你创建的队列。

   ![在 Azure 门户中，将终结点添加到 IoT 中心](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. 选择“创建”  。 成功创建终结点后，转到下一步。

### <a name="add-a-routing-rule"></a>添加路由规则

1. 返回“消息路由”窗格，选择“路由”选项卡，然后选择“+ 添加”。   

1. 在“添加路由”窗格中输入以下信息： 

   **名称**：路由规则的名称。

   **终结点**：选择你创建的终结点。

   **数据源**：选择“设备遥测消息”。 

   **路由查询**：输入 `temperatureAlert = "true"` 。

   ![在 Azure 门户中添加路由规则](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. 选择“保存”。  可以关闭“消息路由”窗格。 

## <a name="create-and-configure-a-logic-app"></a>创建并配置逻辑应用

在上一部分，你已设置 IoT 中心，以将包含温度警报的消息路由到服务总线队列。 现在，请设置一个逻辑应用，以监视服务总线队列，并在每次将消息添加到队列时都发送一则电子邮件通知。

### <a name="create-a-logic-app"></a>创建逻辑应用

1. 选择“创建资源” > “集成” > “逻辑应用”。   

1. 输入以下信息：

   **名称**：逻辑应用的名称。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：使用 IoT 中心所用的同一位置。

   ![在 Azure 门户中创建逻辑应用](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. 选择“创建”  。

### <a name="configure-the-logic-app-trigger"></a>配置逻辑应用触发器

1. 打开逻辑应用。 转到逻辑应用的最简单方法是从资源窗格中选择“资源组”，选择你的资源组，然后从资源列表中选择该逻辑应用。  选择逻辑应用时，逻辑应用设计器会打开。

1. 在逻辑应用设计器中，向下滚动到“模板”并选择“空白逻辑应用”。  

   ![在 Azure 门户中，从空白逻辑应用着手](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. 选择“所有”选项卡，然后选择“服务总线”。  

   ![选择“服务总线”，开始在 Azure 门户中创建逻辑应用](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. 在“触发器”下，选择“当一个或多个消息出现在队列中时(自动完成)”。  

   ![在 Azure 门户中选择逻辑应用的触发器](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. 创建服务总线连接。
   1. 输入连接名称，并从列表中选择你的服务总线命名空间。 下一个屏幕将会打开。

      ![在 Azure 门户中为逻辑应用创建服务总线连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. 选择服务总线策略 (RootManageSharedAccessKey)。 然后选择“创建”。 

      ![在 Azure 门户中为逻辑应用创建服务总线连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 在最后一个屏幕上，对于“队列名称”，请从下拉列表中选择创建的队列。  对于“最大消息计数”，请输入 `175`。 

      ![在逻辑应用中，为服务总线连接指定最大消息计数](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. 在逻辑应用设计器顶部的菜单中选择“保存”以保存更改。 

### <a name="configure-the-logic-app-action"></a>配置逻辑应用操作

1. 创建 SMTP 服务连接。

   1. 选择“新建步骤”。  在“选择操作”中，选择“所有”选项卡。  

   1. 在搜索框中键入 `smtp`，在搜索结果中选择“SMTP”服务，然后选择“发送电子邮件”。  

      ![在 Azure 门户中，在逻辑应用中创建 SMTP 连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 输入邮箱的 SMTP 信息，然后选择“创建”。 

      ![在 Azure 门户中，在逻辑应用中输入 SMTP 连接信息](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      为 [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en) 和 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) 获取 SMTP 信息。

      > [!NOTE]
      > 可能需要禁用 SSL 才能建立连接。 如果存在这种情况，并且你想要在建立连接后重新启用 SSL，请参阅本部分末尾的可选步骤。

   1. 在“发送电子邮件”步骤的“添加新参数”下拉列表中，选择“发件人”、“收件人”、“主题”和“正文”。       单击或点击屏幕上的任意位置以关闭选框。

      ![选择 SMTP 连接电子邮件字段](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. 输入“发件人”  和“收件人”  的电子邮件地址，并为“主题”  和“正文”  输入 `High temperature detected`。 如果“从此流中使用的应用和连接器添加动态内容”对话框已打开，请选择“隐藏”将其关闭。   本教程不使用动态内容。

      ![填写 SMTP 连接电子邮件字段](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. 选择“保存”以保存 SMTP 连接。 

1. （可选）如果必须禁用 SSL 才能与电子邮件提供程序建立连接，并且事后你想要重新启用 SSL，请执行以下步骤：

   1. 在“逻辑应用”窗格中的“开发工具”下，选择“API 连接”。   

   1. 从 API 连接列表中选择 SMTP 连接。

   1. 在“SMTP API 连接”窗格中的“常规”下，选择“编辑 API 连接”。   

   1. 在“编辑 API 连接”窗格中选择“启用 SSL?”，重新输入电子邮件帐户的密码，然后选择“保存”。   

      ![在 Azure 门户中编辑逻辑应用中的 SMTP API 连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

逻辑应用现已准备好处理来自服务总线队列的温度警报，并将通知发送到你的电子邮件帐户。

## <a name="test-the-logic-app"></a>测试逻辑应用

1. 在设备上启动客户端应用程序。

1. 如果使用物理设备，请小心地将热源摆放到热传感器的附近，直到温度超过 30 摄氏度。如果使用在线模拟器，客户端代码将随机输出遥测消息，指出温度超过 30 摄氏度。

1. 随后应会开始收到逻辑应用发送的电子邮件通知。

   > [!NOTE]
   > 电子邮件服务提供商可能需要验证发件人身份，以确保是你本人发送的电子邮件。

## <a name="next-steps"></a>后续步骤

逻辑应用已创建成功，它可以连接 IoT 中心和邮箱，以监视温度并发送通知。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
