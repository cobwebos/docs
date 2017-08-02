---
title: "将 IoT 中心消息保存到 Azure 数据存储 | Microsoft Docs"
description: "使用 Azure 函数应用将 IoT 中心消息保存到 Azure 表存储。 IoT 中心消息包含 IoT 设备发送的传感器数据等信息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 数据存储, iot 传感器数据存储"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: ed867e1e7702d3f178feeab007ac94fb1d67c140
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-table-storage"></a>将包含传感器数据的 IoT 中心消息保存到 Azure 表存储

![端到端关系图](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学习内容

了解如何创建 Azure 存储帐户，以及一个用于在表存储中存储 IoT 中心消息的 Azure 函数应用。

## <a name="what-you-do"></a>准备工作

- 创建 Azure 存储帐户。
- 准备 IoT 中心连接以读取消息。
- 创建并部署 Azure 函数应用。

## <a name="what-you-need"></a>所需条件

- 根据以下要求[设置设备](iot-hub-raspberry-pi-kit-node-get-started.md)：
  - 一个有效的 Azure 订阅
  - 已在订阅中创建一个 IoT 中心 
  - 一个可向 IoT 中心发送消息的正常运行的应用程序

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

1. 在 [Azure 门户](https://portal.azure.com/)中，单击“新建” > “存储” > “存储帐户”。

2. 输入存储帐户所需的信息：

   ![在 Azure 门户中创建存储帐户](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **名称**：存储帐户的名称。 该名称必须全局唯一。

   * **资源组**：使用 IoT 中心所用的同一资源组。

   * **固定仪表板**：选中此选项可以方便地从仪表板访问 IoT 中心。

3. 单击“创建” 。

## <a name="prepare-your-iot-hub-connection-to-read-messages"></a>准备 IoT 中心连接以读取消息

IoT 中心公开一个与事件中心兼容的内置终结点，使应用程序能够读取 IoT 中心消息。 同时，应用程序可以使用使用者组从 IoT 中心读取数据。 在创建用于从 IoT 中心读取数据的 Azure 函数应用之前，请执行以下操作：

- 获取 IoT 中心终结点的连接字符串。
- 为 IoT 中心创建使用者组。

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>获取 IoT 中心终结点的连接字符串

1. 打开 IoT 中心

2. 在“IoT 中心”窗格中的“消息”下面，单击“终结点”。

3. 在右窗格中的“内置终结点”下面，单击“事件”。

4. 在“属性”窗格中，记下以下值：
   - 事件中心兼容终结点
   - 与事件中心兼容的名称

   ![在 Azure 门户中获取 IoT 中心终结点的连接字符串](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

5. 在“IoT 中心”窗格中的“设置”下面，单击“共享访问策略”。

6. 单击“iothubowner”。

7. 记下“主密钥”值。

8. 按如下所示创建 IoT 中心终结点的连接字符串：

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!NOTE]
   > 将 `<Event Hub-compatible endpoint>` 和 `<Primary key>` 替换为前面记下的值。

### <a name="create-a-consumer-group-for-your-iot-hub"></a>为 IoT 中心创建使用者组

1. 打开 IoT 中心

2. 在“IoT 中心”窗格中的“消息”下面，单击“终结点”。

3. 在右窗格中的“内置终结点”下面，单击“事件”。

4. 在“属性”窗格中的“使用者组”下面输入一个名称并记下该名称。

5. 单击“保存” 。

## <a name="create-and-deploy-an-azure-function-app"></a>创建并部署 Azure 函数应用

1. 在 [Azure 门户](https://portal.azure.com/)中，单击“新建” > “计算” > “Function App”。

2. 输入函数应用所需的信息。

   ![在 Azure 门户中创建函数应用](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   * **应用名称**：函数应用的名称。 该名称必须全局唯一。

   * **资源组**：使用 IoT 中心所用的同一资源组。

   * **存储帐户**：创建的存储帐户。

   * **固定仪表板**：选中此选项可以方便地从仪表板访问函数应用。

3. 单击“创建” 。

4. 创建函数应用后，请将其打开。

5. 在该函数应用中，通过执行以下操作创建一个新函数：

   a. 单击“新建函数”。

   b. 为“语言”选择“JavaScript”，为“方案”选择“数据处理”。

   c. 依次单击“创建此函数”、“新建函数”。

   d. 为语言选择“JavaScript”，为方案选择“数据处理”。

   e. 单击“EventHubTrigger JavaScript”模板。

   f. 输入模板所需的信息。

      * **为函数命名**：函数的名称。

      * **事件中心名称**：前面记下的与事件中心兼容的名称。

      * **事件中心连接**：若要添加所创建的 IoT 中心终结点的连接字符串，请单击“新建”。

   g. 单击“创建” 。

6. 执行以下操作配置函数的输出：

   a. 单击“集成” > “新建输出” > “Azure 表存储” > “选择”。

      ![在 Azure 门户中将表存储添加到函数应用](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)

   b. 输入所需的信息。

      * **表参数名称**：指定要在 Azure 函数代码中使用的 `outputTable`。
      
      * **表名称**：使用 `deviceData`。

      * **存储帐户连接**：单击“新建”，选择或输入存储帐户。 如果未显示该存储帐户，请参阅[存储帐户要求](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)。
      
   c. 单击“保存” 。

7. 在“触发器”下面，单击“Azure 事件中心(eventHubMessages)”。

8. 在“事件中心使用者组”下，输入创建的使用者组的名称，然后单击“保存”。

9. 单击“开发”，然后单击“查看文件”。

10. 将 `index.js` 中的代码替换为以下代码：

   ```javascript
   'use strict';

   // This function is triggered each time a message is received in the IoT hub.
   // The message payload is persisted in an Azure storage table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

11. 单击“保存” 。

现已创建函数应用。 它会在表存储中存储 IoT 中心收到的消息。

> [!NOTE]
> 可以使用“运行”按钮测试该函数应用。 单击“运行”，将向 IoT 中心发送测试消息。 收到该消息后，应会触发函数应用的启动并将该消息保存到表存储。 “日志”窗格记录了过程详细信息。

## <a name="verify-your-message-in-your-table-storage"></a>在表存储中验证消息

1. 在设备上运行示例应用程序，将消息发送到 IoT 中心。

2. [下载并安装 Azure 存储资源管理器](http://storageexplorer.com/)。

3. 打开存储资源管理器，单击“添加 Azure 帐户” > “登录”，登录到 Azure 帐户。

4. 单击你的 Azure 订阅 >“存储帐户”> 你的存储帐户 >“表” > “deviceData”。

   此时应会显示设备发送到 IoT 中心的、记录在 `deviceData` 表中的消息。

## <a name="next-steps"></a>后续步骤

现已成功创建 Azure 存储帐户，以及可将 IoT 中心收到的消息存储在表存储中的 Azure 函数应用。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

