---
title: include 文件（设备流预览）
description: include 文件（设备流预览）
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: 53d8df7e2366cfbf2f62e79fc99c8ef2f9b48ba1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830807"
---
此部分介绍如何使用 [Azure 门户](https://portal.azure.com)创建 IoT 中心。

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 选择+“创建资源”，然后选择“物联网”。

3. 在右侧列表中单击“Iot 中心”。 随即显示 IoT 中心创建过程的第一个屏幕。

   ![显示了在 Azure 门户中创建中心的屏幕截图](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-1.png)

   填写字段：

   **订阅**：请选择要用于 IoT 中心的订阅。

   **资源组**：可创建新的资源组或使用现有资源组。 若要新建一个，请单击“新建”，并填写要使用的名称。 若要使用现有资源组，请单击“使用现有资源组”并从下拉列表中选择该组。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../articles/azure-resource-manager/resource-group-portal.md)。

   **区域**：这是要在其中设置中心的区域。 确保选择受支持的区域（例如，美国中部或美国中部 EUAP）。

   **IoT 中心名称**：输入 IoT 中心的名称。 该名称必须全局唯一。 如果输入的名称可用，会显示一个绿色复选标记。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. 单击“下一步:大小和规模”，以便继续创建 IoT 中心。

   ![屏幕截图显示使用 Azure 门户为新的 IoT 中心设置大小和缩放级别](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-2-free.png)


   在此屏幕上，可以采用默认值，只需在底部单击“查看+创建”即可。 

   **定价和缩放层**：确保选择标准（S1、S2、S3）层或免费 (F1) 层中的一个。 也可根据队列大小以及预期在中心会出现的非流式处理工作负荷（例如遥测消息）完成该选择。 例如，免费层适用于测试和评估。 它允许 500 台设备连接到 IoT 中心，并且每天最多传输 8,000 条信息。 每个 Azure 订阅可以在免费层中创建一个 IoT 中心。 

   **IoT 中心单元**：此选择取决于预期在中心会出现的非流式处理工作负荷 - 现在可以选择 1。

   有关其他层选项的详细信息，请参阅[选择合适的 IoT 中心层](../articles/iot-hub/iot-hub-scaling.md)。

5. 单击“查看+创建”可查看选择。 会显示类似于以下的屏幕。

   ![屏幕截图显示用于创建新 IoT 中心的信息](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-3-free.png)

6. 单击“创建”以创建新的 IoT 中心。 创建中心需要几分钟时间。
