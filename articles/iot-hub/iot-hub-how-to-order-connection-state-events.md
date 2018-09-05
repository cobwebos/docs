---
title: 使用 Azure Cosmos DB 将来自 Azure IoT 中心的设备连接事件排序 | Microsoft Docs
description: 本文介绍如何使用 Azure Cosmos DB 对来自 Azure IoT 中心的设备连接事件进行排序和记录，以保持最新连接状态
services: iot-hub
documentationcenter: ''
author: ash2017
manager: briz
editor: ''
ms.service: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: asrastog
ms.openlocfilehash: dd3c750e93646624e46c46afd871ef75af905bf0
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145375"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 将来自 Azure IoT 中心的设备连接事件排序

借助 Azure 事件网格可以基于应用程序生成事件，并轻松地在业务解决方案中集成 IoT 事件。 本文逐步讲解一种可用于跟踪最新设备连接状态并将其存储在 Cosmos DB 中的设置。 我们将使用“设备已连接”和“设备已断开连接”事件中提供的序号，并在 Cosmos DB 中存储最新状态。 此外，我们将使用一个存储过程，它是针对 Cosmos DB 中的集合执行的应用程序逻辑。

序号是十六进制数的字符串表示形式。 可以使用字符串比较来确定更大的编号。 如果将字符串转换为十六进制，则编号是一个 256 位编号。 序号严格递增，最新事件的编号大于其他事件。 如果经常出现设备连接和断开连接事件，并且你想要确保只使用最新事件来触发下游操作（因为 Azure 事件网格不支持事件排序），则此功能非常有用。

## <a name="prerequisites"></a>先决条件

* 有效的 Azure 帐户。 如果没有帐户，可以[创建一个免费帐户](http://azure.microsoft.com/pricing/free-trial/)。
* 有效的 Azure Cosmos DB SQL API 帐户。 如果尚未创建此帐户，请参阅[创建数据库帐户](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#create-a-database-account)中的演练。
* 数据库中的集合。 请参阅[添加集合](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#add-a-collection)中的演练。
* Azure 中的 IoT 中心。 如果尚未创建 Iot 中心，请参阅 [IoT 中心入门](../iot-hub/iot-hub-csharp-csharp-getstarted.md)中的演练。 

## <a name="create-a-stored-procedure"></a>创建存储过程

首先，创建一个存储过程，并将其设置为运行一个逻辑来比较传入事件的序号，并在数据库中记录每个设备的最新事件。

1. 在 Cosmos DB SQL API 中，选择“数据资源管理器” > “项” > “新建存储过程”。

   ![创建存储过程](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. 输入存储过程 ID，并将以下内容粘贴到“存储过程正文”中。 请注意，此代码应替换存储过程正文中的任何现有代码。 此代码为每个设备 ID 保留一行，并通过标识最高序号来记录该设备 ID 的最新连接状态。 

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};
      
      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. 保存存储过程： 

    ![保存存储过程](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>创建逻辑应用

首先，创建逻辑应用并添加事件网格触发器，以便监视虚拟机的资源组。 

### <a name="create-a-logic-app-resource"></a>创建逻辑应用资源

1. 在 [Azure 门户](https://portal.azure.com)中，选择“新建” > “集成” > “逻辑应用”。

   ![创建逻辑应用](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. 为逻辑应用指定一个在订阅中唯一的名称，然后选择 IoT 中心所在的同一订阅、资源组和位置。 
3. 准备就绪后，请选择“固定到仪表板”，并选择“创建”。

   你现在已为逻辑应用程序创建 Azure 资源。 在 Azure 部署逻辑应用后，逻辑应用设计器会显示针对常用模式的模板，以便你可以更快地入门。

   > [!NOTE] 
   > 选择“固定到仪表板”时，逻辑应用会在逻辑应用设计器中自动打开。 否则，你可以手动查找和打开逻辑应用。

4. 在逻辑应用设计器中的“模板”下选择“空白逻辑应用”，以便从头开始生成逻辑应用。

### <a name="select-a-trigger"></a>选择触发器

触发器是启动逻辑应用的特定事件。 在本教程中，触发工作流的触发器通过 HTTP 接收请求。  

1. 在连接器和触发器搜索栏中，键入 **HTTP**。
2. 选择“请求 - 当收到 HTTP 请求时”作为触发器。 

   ![选择 HTTP 请求触发器](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. 选择“使用示例有效负载生成架构”。 

   ![选择 HTTP 请求触发器](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. 在文本框中粘贴以下示例 JSON 代码，然后选择“完成”：

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

5. 可能会收到一条弹出通知，其中指出，“请记住要在请求中包含设为 application/json 的内容类型标头”。 可以放心忽略此建议，并转到下一部分。 

### <a name="create-a-condition"></a>创建条件

在满足逻辑应用工作流中的特定条件后，条件可帮助运行特定的操作。 一旦满足条件，即可定义所需的操作。 在本教程中，条件是检查 eventType 是“设备已连接”还是“设备已断开连接”。 操作是在数据库中执行存储过程。 

1. 依次选择“新建步骤”、“内置”、“条件”。 

2. 按如下所示填写条件，以便仅对“设备已连接”和“设备已断开连接”事件执行此操作：
  * 选择值：**eventType**
  * 将条件更改为 **ends with**
  * 选择值：**nected**

   ![填写条件](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

3. 如果条件为 true，请单击“添加操作”。
  
   ![为 true 时添加操作](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

4. 搜索 Cosmos DB，并单击“Azure Cosmos DB - 执行存储过程”

   ![搜索 CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

5. 从数据库中选择值，以填充“执行存储过程”窗体。 按如下所示输入分区键值和参数。 

   ![填充逻辑应用操作](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. 保存逻辑应用。 

### <a name="copy-the-http-url"></a>复制 HTTP URL

在退出逻辑应用设计器之前，请复制逻辑应用要侦听的触发器 URL。 稍后要使用此 URL 来配置事件网格。 

1. 单击“当收到 HTTP 请求时”触发器配置框将其展开。 
2. 选择“HTTP POST URL”旁边的复制按钮复制其值。 

   ![复制 HTTP POST URL](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. 保存此 URL，以便在下一部分可以引用它。 

## <a name="configure-subscription-for-iot-hub-events"></a>为 IoT 中心事件配置订阅

本部分将 IoT 中心配置为在发生事件时发布事件。 

1. 在 Azure 门户中导航到 IoT 中心。 
2. 选择“事件”。

   ![打开事件网格详细信息](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. 选择“事件订阅”。 

   ![创建新的事件订阅](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. 使用以下值创建事件订阅： 
   * **事件类型**：取消选中“订阅所有事件类型”，然后从菜单中选择“设备已连接”和“设备已断开连接”。
   * **终结点详细信息**：选择“Webhook”作为“终结点类型”，单击所选终结点并粘贴从逻辑应用中复制的 URL，然后确认选择。

   ![选择终结点 URL](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

   * **事件订阅详细信息**：提供一个描述性的名称，然后选择“事件网格架构”。完成后，窗体应如以下示例所示： 

   ![示例事件订阅窗体](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

5. 选择“创建”保存事件订阅。

## <a name="observe-events"></a>观察事件
设置事件订阅后，让我们通过连接设备进行测试。

### <a name="register-a-device-in-iot-hub"></a>在 IoT 中心注册设备

1. 在 IoT 中心选择“IoT 设备”。 
2. 选择 **添加** 。
3. 对于“设备 ID”，请输入 `Demo-Device-1`。
4. 选择“保存”。 
5. 可以添加具有不同设备 ID 的多个设备。

   ![操作方法屏幕截图](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. 复制“连接字符串 ---主密钥”供稍后使用。

   ![操作方法屏幕截图](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>启动 Raspberry Pi 模拟器

1. 让我们使用 Raspberry Pi Web 模拟器来模拟设备连接。

[启动 Raspberry Pi 模拟器](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-applciation-on-the-raspberry-pi-web-simulator"></a>在 Raspberry Pi Web 模拟器上运行示例应用程序
这会触发“设备已连接”事件。

1. 在代码区域中，将第 15 行的占位符替换为 Azure IoT 中心设备连接字符串。

   ![操作方法屏幕截图](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. 单击“运行”以运行该应用程序。

应看到以下输出，该输出显示传感器数据和发送到 IoT 中心的消息。

   ![操作方法屏幕截图](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   单击“停止”以停止模拟器，并触发“设备已断开连接”事件。

现已运行示例应用程序来收集传感器数据并将其发送到 IoT 中心。 

### <a name="observe-events-in-cosmos-db"></a>在 Cosmos DB 中观察事件

可在 Cosmos DB 文档中查看已执行的存储过程的结果。 下面是结果的大致形式。 请注意，每行包含每个设备的最新设备连接状态

   ![操作方法屏幕截图](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

如果不使用 Azure 门户，也可以使用 Azure CLI 来完成 IoT 中心相关的步骤。 有关详细信息，请参阅有关使用 Azure CLI [创建事件订阅](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription)和[创建 IoT 设备](https://docs.microsoft.com/cli/azure/iot/device)的网页。

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

若要在 Azure 门户中删除 Azure Cosmos DB 帐户，请右键单击该帐户名，然后单击“删除帐户”。 参阅有关[删除 Azure Cosmos DB 帐户](https://docs.microsoft.com/azure/cosmos-db/manage-account#delete)的详细说明。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[通过使用事件网格触发操作来对 IoT 中心事件做出反应](../iot-hub/iot-hub-event-grid.md)
* [请尝试学习 IoT 中心事件教程](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* 了解[事件网格](../event-grid/overview.md)的其他作用


