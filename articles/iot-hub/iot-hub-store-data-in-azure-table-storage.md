---
title: 将 IoT 中心消息保存到 Azure 数据存储 | Microsoft Docs
description: 使用 IoT 中心消息路由将 IoT 中心消息保存到 Azure Blob 存储中。 IoT 中心消息包含 IoT 设备发送的传感器数据等信息。
author: rangv
manager: ''
keywords: iot 数据存储, iot 传感器数据存储
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 678c538a5d672826f74235d4ac415fccf5de13fe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635672"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>将包含传感器数据的 IoT 中心消息保存到 Azure Blob 存储

![端到端关系图](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学习内容

了解如何创建 Azure 存储帐户，以及 Azure 函数应用，以在 blob 存储中存储 IoT 中心消息。

## <a name="what-you-do"></a>准备工作

- 创建 Azure 存储帐户。
- 准备 IoT 中心，将消息路由到存储。

## <a name="what-you-need"></a>所需条件

- 根据以下要求[设置设备](iot-hub-raspberry-pi-kit-node-get-started.md)：
  - 一个有效的 Azure 订阅
  - 已在订阅中创建一个 IoT 中心 
  - 一个可向 IoT 中心发送消息的正常运行的应用程序

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

1. 在 [Azure 门户](https://portal.azure.com/)中，单击“创建资源” > “存储” > “存储帐户” > “创建”。

2. 输入存储帐户所需的信息：

   ![在 Azure 门户中创建存储帐户](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **名称**：存储帐户的名称。 该名称必须全局唯一。

   * **资源组**：使用 IoT 中心所用的同一资源组。

   * **固定仪表板**：选中此选项可以方便地从仪表板访问 IoT 中心。

3. 单击“创建”。

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>准备 IoT 中心，将消息路由到存储

IoT 中心以本机方式支持将消息以 blob 形式路由到 Azure 存储。 若要了解有关 Azure IoT 中心自定义终结点的详细信息，可以参考[内置 IoT 中心终结点列表](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints)。

### <a name="add-storage-as-a-custom-endpoint"></a>将存储添加为自定义终结点

在 Azure 门户中导航到 IoT 中心。 单击“终结点” > “添加”。 命名该终结点并选择“Azure 存储容器”作为终结点类型。 使用选取器选择在上一节中创建的存储帐户。 创建存储容器并选择它，然后单击“确定”。

  ![在 IoT 中心中创建自定义终结点](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>添加路由，将数据路由到存储

单击“路由” > “添加”，输入路由的名称。 选择“设备消息”作为数据源，选择刚刚创建的存储终结点作为路由中的终结点。 输入 `true` 作为查询字符串，然后单击“保存”。

  ![在 IoT 中心中创建路由](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>添加热路径遥测的路由（可选）

IoT 中心默认将未匹配任何其他路由的所有消息路由到内置终结点。 由于所有遥测消息现在均与将消息路由到存储的规则匹配，所以你需要添加另一个路由，用于将消息写入到内置终结点。 无需支付额外费用，即可将消息路由到多个终结点。

> [!NOTE]
> 如果未对遥测消息执行其他处理，则可以跳过此步骤。

从“路由”窗格单击“添加”，然后输入路由的名称。 选择“设备消息”作为数据源，选择“事件”作为终结点。 输入 `true` 作为查询字符串，然后单击“保存”。

  ![在 IoT 中心中创建热路径路由](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>在存储容器中验证消息

1. 在设备上运行示例应用程序，将消息发送到 IoT 中心。

2. [下载并安装 Azure 存储资源管理器](http://storageexplorer.com/)。

3. 打开存储资源管理器，单击“添加 Azure 帐户” > “登录”，登录到 Azure 帐户。

4. 单击 Azure 订阅 >“存储帐户”> 你的存储帐户 >“ Blob 容器”> 你的容器。

   此时会显示设备发送到 IoT 中心的、记录在 blob 容器中的消息。

## <a name="next-steps"></a>后续步骤

你已成功创建 Azure 存储帐户，并在该存储帐户下将消息从 IoT 中心路由到了 Blob 容器中。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
