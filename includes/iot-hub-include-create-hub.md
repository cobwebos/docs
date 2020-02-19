---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cc53f7ae6eb6254eaa05bf643ecfa0188650df95
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461849"
---
此部分介绍如何使用 [Azure 门户](https://portal.azure.com)创建 IoT 中心。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从 Azure 主页中选择“+ 创建资源”按钮，然后在“搜索市场”字段中输入“IoT 中心”。   

1. 在搜索结果中选择“IoT 中心”，然后选择“创建”   。

1. 在“基本信息”选项卡上，按如下所示填写字段： 

   - **订阅**：选择要用于中心的订阅。

   - **资源组**：选择一个资源组或新建一个资源组。 若要新建资源组，请选择“新建”并填写要使用的名称。  若要使用现有的资源组，请选择它。 有关详细信息，请参阅[管理 Azure 资源管理器资源组](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)。

   - **区域**：选择中心所在的区域。 选择最靠近你的位置。 某些功能（如 [IoT 中心设备流](../articles/iot-hub/iot-hub-device-streams-overview.md)）仅适用于特定区域。 对于这些受限功能，你必须选择受支持的区域之一。

   - **IoT 中心名称**：输入中心的名称。 该名称必须全局唯一。 如果输入的名称可用，会显示一个绿色复选标记。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![在 Azure 门户中创建中心](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. 在完成时选择“下一步:  大小和规模”，以继续创建中心。

   ![使用 Azure 门户为新的中心设置大小和规模](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   可在此处接受默认设置。 如果需要，可以修改以下任何字段： 

    - **定价和缩放层**：选择的层。 可以根据你需要的功能数以及每天通过解决方案发送的消息数从多个层级中进行选择。 免费层适用于测试和评估。 允许 500 台设备连接到中心，每天最多可传输 8,000 条消息。 每个 Azure 订阅可以在免费层中创建一个 IoT 中心。 

      如果正在完成 IoT 中心设备流的快速入门，请选择免费层。

    - **IoT 中心单元**：每日每单位允许的消息数取决于中心的定价层。 例如，如果希望中心支持 700,000 条消息引入，请选择两个 S1 层单位。
    有关其他层选项的详细信息，请参阅[选择合适的 IoT 中心层](../articles/iot-hub/iot-hub-scaling.md)。

    - **Azure 安全中心**：启用此功能可为 IoT 和设备添加额外的一层威胁防护。 此选项不可用于免费层的中心。 有关此功能的详细信息，请参阅[适用于 IoT 的 Azure 安全中心](https://docs.microsoft.com/azure/asc-for-iot/)。

    - **高级设置** > **设备到云的分区**：此属性将设备到云消息与这些消息的同步读取器数目相关联。 大多数中心只需要 4 个分区。

1.  选择“下一步:  标记”继续到下一屏幕。

    标记是名称/值对。 可以为多个资源和资源组分配相同的标记，以便对资源进行分类并合并计费。 有关详细信息，请参阅[使用标记来组织 Azure 资源](../articles/azure-resource-manager/management/tag-resources.md)。

    ![使用 Azure 门户为中心分配标记](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  选择“下一步:  查看+创建”可查看选择。 你会看到类似于此屏幕的内容，但其中包含创建中心时选择的值。 

    ![查看用于创建新中心的信息](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  选择“创建”以创建新的中心  。 创建中心需要几分钟时间。
