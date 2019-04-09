---
title: 通过 Azure 逻辑应用进行 IoT 远程监视并发送通知 | Microsoft Docs
description: 使用 Azure 逻辑应用在 IoT 中心内监视 IoT 温度，并针对检测到的任何异常自动将电子邮件通知发送到邮箱。
author: robinsh
keywords: IoT 监视, IoT 通知, IoT 温度监视
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 5d5b1d1579600767153fcf5ad751e1224631d611
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262510"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>通过连接 IoT 中心和邮箱的 Azure 逻辑应用进行 IoT 远程监视并发送通知

![端到端关系图](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Azure 逻辑应用提供将流程自动化为一系列步骤的方法。 逻辑应用可跨各种服务和协议进行连接。 它以触发器（例如“何时添加帐户”）开始，后面是操作组合，例如“发送推送通知”操作。 此功能使逻辑应用成为其他使用方案之外的用于 IoT 监视（例如时刻警惕出现异常）的完美 IoT 解决方案。

## <a name="what-you-learn"></a>学习内容

了解如何创建连接 IoT 中心和邮箱的逻辑应用，以监视温度并发送通知。 当温度高于 30 C 时，客户端应用程序会在发送到 IoT 中心的消息中标记 `temperatureAlert = "true"`。 该消息将触发逻辑应用以发送电子邮件通知。

## <a name="what-you-do"></a>准备工作

* 创建服务总线命名空间，并向其添加队列。
* 向 IoT 中心添加终结点和路由规则。
* 创建、配置和测试逻辑应用。

## <a name="what-you-need"></a>所需条件

* 已完成教程[设置设备](iot-hub-raspberry-pi-kit-node-get-started.md)，其中涵盖以下要求：

  * 一个有效的 Azure 订阅。
  * 已在订阅中创建一个 Azure IoT 中心。
  * 一个可向 Azure IoT 中心发送消息的客户端应用程序。

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>创建服务总线命名空间，并向其添加队列

### <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

1. 上[Azure 门户](https://portal.azure.com/)，选择**创建资源** > **企业集成** > **服务总线**。

2. 提供以下信息：

   **名称**：服务总线的名称。

   **定价层**：选择**基本** > **选择**。 基本层对本教程而言就已足够。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：使用 IoT 中心所用的同一位置。

3. 选择“创建”。

   ![在 Azure 门户中创建服务总线命名空间](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>添加服务总线队列

1. 打开服务总线命名空间，并选择 **+ 队列**。

1. 输入队列的名称，然后选择**创建**。

1. 打开服务总线队列，然后选择**共享访问策略** > **+ 添加**。

1. 输入的名称的策略，检查**管理**，然后选择**创建**。

   ![在 Azure 门户中添加服务总线队列](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-query-to-your-iot-hub"></a>将终结点和路由查询添加到 IoT 中心

现在添加到 Iot 中心终结点和路由查询。

### <a name="add-an-endpoint"></a>添加终结点

1. 打开 IoT 中心中，选择**终结点** > **+ 添加**。

1. 输入以下信息：

   **名称**：终结点的名称。

   **终结点类型**:选择“服务总线队列”。

   **服务总线命名空间**:选择你创建的命名空间。

   **服务总线队列**：选择你创建的队列。

3. 选择“确定”。

   ![在 Azure 门户中，将终结点添加到 IoT 中心](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>添加路由规则

1. 在 IoT 中心，选择**路由** > **+ 添加**。

2. 输入以下信息：

   **名称**：路由规则的名称。

   **数据源**：选择**DeviceMessages**。

   **终结点**：选择你创建的终结点。

   **查询字符串**：输入 `temperatureAlert = "true"` 。

3. 选择“保存”。

   ![在 Azure 门户中添加路由规则](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>创建并配置逻辑应用

接下来，创建并配置一个逻辑应用。

### <a name="create-a-logic-app"></a>创建逻辑应用

1. 在中[Azure 门户](https://portal.azure.com/)，选择**创建资源** > **企业集成** > **逻辑应用**。

2. 输入以下信息：

   **名称**：逻辑应用的名称。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：使用 IoT 中心所用的同一位置。

3. 选择“创建”。

### <a name="configure-the-logic-app"></a>配置逻辑应用

1. 打开面向逻辑应用设计器打开的逻辑应用。

2. 在逻辑应用设计器中，选择**空白逻辑应用**。

   ![在 Azure 门户中，从空白逻辑应用着手](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

3. 选择“服务总线”。

   ![选择“服务总线”，开始在 Azure 门户中创建逻辑应用](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

4. 选择**服务总线 – 当一个或多个消息出现在队列中 （自动完成）**。

5. 创建服务总线连接。

   1. 输入连接名称。

   2. 选择服务总线命名空间 > 服务总线策略 >**创建**。

      ![在 Azure 门户中为逻辑应用创建服务总线连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   3. 选择**继续**创建服务总线连接后。

   4. 选择你创建的队列，并输入`175`有关**最大消息计数**。

      ![在逻辑应用中，为服务总线连接指定最大消息计数](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   5. 选择"保存"按钮保存所做的更改。

6. 创建 SMTP 服务连接。

   1. 选择“新建步骤” > “添加操作”。

   2. 类型`SMTP`，选择**SMTP**在搜索结果中，服务，然后选择**SMTP-发送电子邮件**。

      ![在 Azure 门户中，在逻辑应用中创建 SMTP 连接](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   3. 输入您的邮箱的 SMTP 信息，然后选择**创建**。

      ![在 Azure 门户中，在逻辑应用中输入 SMTP 连接信息](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      为 [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)、[Gmail](https://support.google.com/a/answer/176600?hl=en) 和 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) 获取 SMTP 信息。

   4. 输入“发件人”和“收件人”的电子邮件地址，并为“主题”和“正文”输入 `High temperature detected`。

   5. 选择“保存”。

保存时，逻辑应用按工作顺序排列。

## <a name="test-the-logic-app"></a>测试逻辑应用

1. 启动在[将 ESP8266 连接到 Azure IoT 中心](iot-hub-arduino-huzzah-esp8266-get-started.md)中部署到设备的客户端应用程序。

2. 将 SensorTag 周围的环境温度升高到 30 C 以上。例如在 SensorTag 周围点上蜡烛。

3. 应收到逻辑应用发送的电子邮件通知。

   > [!NOTE]
   > 电子邮件服务提供商可能需要验证发件人身份，以确保是你本人发送的电子邮件。

## <a name="next-steps"></a>后续步骤

逻辑应用已创建成功，它可以连接 IoT 中心和邮箱，以监视温度并发送通知。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
