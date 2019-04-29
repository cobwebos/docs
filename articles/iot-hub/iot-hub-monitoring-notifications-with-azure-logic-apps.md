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
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126020"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>通过连接 IoT 中心和邮箱的 Azure 逻辑应用进行 IoT 远程监视并发送通知

![端到端关系图](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)可以帮助你安排工作流跨本地和云服务，一个或多个企业中，以及跨各种协议。 逻辑应用以开头后, 跟一个或多个可以使用内置控件，例如条件和迭代器序列化的操作的触发器。 这种灵活性使逻辑应用监视的 IoT 方案的理想 IoT 解决方案。 例如，从 IoT 中心终结点上的设备的遥测数据的到达可以启动逻辑应用工作流以仓库的 Azure 存储 blob 中的数据、 发送电子邮件警报的数据异常，则发出警告，安排技术人员进行的访问，如果设备报告的失败依次类推。

## <a name="what-you-learn"></a>学习内容

了解如何创建连接 IoT 中心和邮箱的逻辑应用，以监视温度并发送通知。

在设备上运行的客户端代码设置的应用程序属性， `temperatureAlert`，请在每个遥测消息发送到 IoT 中心。 当客户端代码检测到的温度高于 30 C 时，它将此属性设置为`true`; 否则为将属性设置为`false`。

在本主题中，您将设置路由的 IoT 中心以发送消息`temperatureAlert = true`向服务总线终结点，并且您设置的逻辑应用程序的消息到达服务总线终结点上的触发器，向你发送电子邮件通知。

## <a name="what-you-do"></a>准备工作

* 创建服务总线命名空间并向其添加服务总线队列。
* 将自定义终结点和路由规则添加到 IoT 中心，将包含到服务总线队列的温度警报将消息路由。
* 创建、 配置和测试逻辑应用以使用来自服务总线队列的消息并将通知电子邮件发送到所需的收件人。

## <a name="what-you-need"></a>所需条件

* 完成[Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程中; 例如， [Raspberry Pi 与 node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这些涵盖以下要求：

  * 一个有效的 Azure 订阅。
  * 已在订阅中创建一个 Azure IoT 中心。
  * 将遥测消息发送到 Azure IoT 中心在设备上运行的客户端应用程序。

## <a name="create-service-bus-namespace-and-queue"></a>创建服务总线命名空间和队列

创建服务总线命名空间和队列。 本主题中稍后在 IoT 中心，将包含到服务总线队列，它们的位置将提取由逻辑应用并触发该发送通知电子邮件的温度警报的直接消息中创建路由规则。

### <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

1. 上[Azure 门户](https://portal.azure.com/)，选择 **+ 创建资源** > **集成** > **服务总线**。

1. 上**创建命名空间**窗格中，提供以下信息：

   **名称**：服务总线命名空间的名称。 命名空间在 Azure 上必须是唯一的。

   **定价层**：选择**基本**从下拉列表。 基本层对本教程而言就已足够。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：使用 IoT 中心所用的同一位置。

   ![在 Azure 门户中创建服务总线命名空间](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. 选择“创建”。 等待部署完成，然后移到下一步。

### <a name="add-a-service-bus-queue-to-the-namespace"></a>将服务总线队列添加到命名空间

1. 打开服务总线命名空间。 若要获取对服务总线命名空间的最简单方法是选择**资源组**从资源窗格中，选择资源组中，然后从资源列表中选择服务总线命名空间。

1. 上**服务总线 Namespace**窗格中，选择 **+ 队列**。

1. 输入队列的名称，然后选择**创建**。 已成功创建队列后，**创建队列**关闭窗格。

   ![在 Azure 门户中添加服务总线队列](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. 重新**服务总线 Namespace**窗格下**实体**，选择**队列**。 从列表中，打开服务总线队列，然后选择**共享访问策略** > **+ 添加**。

1. 输入的名称的策略，检查**管理**，然后选择**创建**。

   ![在 Azure 门户中添加服务总线队列策略](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>将自定义终结点和路由规则添加到 IoT 中心

将服务总线队列的自定义终结点添加到 IoT 中心并创建消息路由规则，以指示包含到该终结点，温度警报的消息，它们将被选取逻辑应用。 路由规则使用的路由查询`temperatureAlert = "true"`，以将值的基础的消息转发`temperatureAlert`设置通过在设备上运行的客户端代码的应用程序属性。 若要了解详细信息，请参阅[消息路由基于消息属性的查询](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)。

### <a name="add-a-custom-endpoint"></a>添加自定义终结点

1. 打开 IoT 中心 若要获取到 IoT 中心的最简单方法是选择**资源组**从资源窗格中，选择资源组中，然后从资源列表中选择 IoT 中心。

1. 下**Messaging**，选择**消息路由**。 上**消息路由**窗格中，选择**自定义终结点**选项卡，然后选择 **+ 添加**。 从下拉列表中，选择**服务总线队列**。

   ![在 Azure 门户中，将终结点添加到 IoT 中心](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. 上**添加服务总线终结点**窗格中，输入以下信息：

   **终结点名称**:终结点的名称。

   **服务总线命名空间**：选择你创建的命名空间。

   **服务总线队列**:选择你创建的队列。

   ![在 Azure 门户中，将终结点添加到 IoT 中心](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. 选择“创建”。 已成功创建终结点后，请继续执行下一步。

### <a name="add-a-routing-rule"></a>添加路由规则

1. 重新**消息路由**窗格中，选择**路由**选项卡，然后选择 **+ 添加**。

1. 上**添加路由**窗格中，输入以下信息：

   **名称**：路由规则的名称。

   **终结点**：选择你创建的终结点。

   **数据源**：选择**设备的遥测消息**。

   **路由查询**：输入 `temperatureAlert = "true"` 。

   ![在 Azure 门户中添加路由规则](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. 选择“保存”。 你可以关闭**消息路由**窗格。

## <a name="create-and-configure-a-logic-app"></a>创建和配置逻辑应用

在上一部分中，您将设置 IoT 中心，将包含到服务总线队列的温度警报将消息路由。 现在，你设置了逻辑应用来监视服务总线队列和每次向队列添加消息时发送电子邮件通知。

### <a name="create-a-logic-app"></a>创建逻辑应用

1. 选择**创建资源** > **集成** > **逻辑应用**。

1. 输入以下信息：

   **名称**：逻辑应用的名称。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：使用 IoT 中心所用的同一位置。

   ![在 Azure 门户中创建逻辑应用](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. 选择“创建”。

### <a name="configure-the-logic-app-trigger"></a>配置逻辑应用触发器

1. 打开逻辑应用。 转到逻辑应用的最简单方法是选择**资源组**从资源窗格中，选择资源组中，然后从资源列表中选择逻辑应用。 选择逻辑应用，逻辑应用设计器就会打开。

1. 在逻辑应用设计器中，向下滚动到**模板**，然后选择**空白逻辑应用**。

   ![在 Azure 门户中，从空白逻辑应用着手](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. 选择**所有**选项卡，然后选择**服务总线**。

   ![选择“服务总线”，开始在 Azure 门户中创建逻辑应用](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. 下**触发器**，选择**队列 （自动完成） 一个或多个消息到达时**。

   ![在 Azure 门户中选择逻辑应用触发器](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. 创建服务总线连接。
   1. 输入连接名称，然后从列表中选择服务总线命名空间。 下一个屏幕将打开。

      ![在 Azure 门户中为逻辑应用创建服务总线连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. 选择服务总线策略 (RootManageSharedAccessKey)。 然后选择**创建**。

      ![在 Azure 门户中为逻辑应用创建服务总线连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 在最后一个屏幕上，对于**队列名称**，选择从下拉列表创建的队列。 输入`175`有关**最大消息计数**。

      ![在逻辑应用中，为服务总线连接指定最大消息计数](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. 选择**保存**上要保存所做的更改在逻辑应用设计器顶部的菜单。

### <a name="configure-the-logic-app-action"></a>配置逻辑应用操作

1. 创建 SMTP 服务连接。

   1. 选择“新建步骤”。 在中**选择操作**，选择**所有**选项卡。

   1. 类型`smtp`在搜索框中，选择**SMTP**在搜索结果中，服务，然后选择**发送电子邮件**。

      ![在 Azure 门户中，在逻辑应用中创建 SMTP 连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 输入您的邮箱的 SMTP 信息，然后选择**创建**。

      ![在 Azure 门户中，在逻辑应用中输入 SMTP 连接信息](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      为 [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en) 和 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) 获取 SMTP 信息。

      > [!NOTE]
      > 您可能需要禁用 SSL 来建立连接。 如果出现这种情况，并且你想要建立的连接后重新启用 SSL，请参阅本部分末尾的可选步骤。

   1. 从**添加新参数**上的下拉列表**发送电子邮件**步骤中，选择**从**，**到**，**主题**并**正文**。 单击或点击任意位置以关闭选择在屏幕上。

      ![选择 SMTP 连接电子邮件字段](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. 输入“发件人”和“收件人”的电子邮件地址，并为“主题”和“正文”输入 `High temperature detected`。 如果**中的应用和从此流中使用的连接器添加动态内容**对话框打开时，选择**隐藏**以将其关闭。 在本教程中不使用动态内容。

      ![填充 SMTP 连接电子邮件域](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. 选择**保存**以保存 SMTP 连接。

1. （可选）如果您必须禁用 SSL 来建立与电子邮件提供商的连接并且想要重新启用它，请按照下列步骤：

   1. 上**逻辑应用**窗格下**开发工具**，选择**API 连接**。

   1. 从 API 连接列表中，选择 SMTP 连接。

   1. 上**smtp API 连接**窗格下**常规**，选择**编辑 API 连接**。

   1. 上**编辑 API 连接**窗格中，选择**启用 SSL？**，重新输入你的电子邮件帐户的密码，然后选择**保存**。

      ![编辑在 Azure 门户中在逻辑应用中的 SMTP API 连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

逻辑应用现在已准备好处理从服务总线队列的温度警报并将通知发送到你的电子邮件帐户。

## <a name="test-the-logic-app"></a>测试逻辑应用

1. 在设备上启动客户端应用程序。

1. 如果您在使用物理设备，请仔细自带附近热度传感器热源直到温度超过 30 度 c。如果您使用联机模拟器，客户端代码将随机输出的遥测消息数超过 30 c。

1. 你应该开始接收逻辑应用发送的电子邮件通知。

   > [!NOTE]
   > 电子邮件服务提供商可能需要验证发件人身份，以确保是你本人发送的电子邮件。

## <a name="next-steps"></a>后续步骤

逻辑应用已创建成功，它可以连接 IoT 中心和邮箱，以监视温度并发送通知。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
