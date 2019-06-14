---
title: Azure IoT 中心的实时传感器数据可视化 - Power BI | Microsoft Docs
description: 使用 Power BI 可视化从传感器收集的温度和湿度数据并将其发送到 Azure IoT 中心。
author: robinsh
keywords: 实时数据可视化, 即时数据可视化, 传感器数据可视化
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065959"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>使用 Power BI 可视化 Azure IoT 中心的实时传感器数据

![端到端关系图](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学习内容

了解如何可视化 Azure IoT 中心通过 Power BI 接收的实时传感器数据。 如果你想要尝试可视化 IoT 中心的 web 应用中的数据，请参阅[使用 web 应用可视化 Azure IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-web-apps.md)。

## <a name="what-you-do"></a>准备工作

* 添加一个使用者组，让 IoT 中心做好数据访问准备。

* 创建、配置和运行流分析作业，以便将数据从 IoT 中心传输到 Power BI 帐户。

* 创建并发布实现数据可视化的 Power BI 报表。

## <a name="what-you-need"></a>所需条件

* 完成 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程；例如[将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这些文章介绍了以下要求：
  
  * 一个有效的 Azure 订阅。
  * 已在订阅中创建一个 Azure IoT 中心。
  * 一个可向 Azure IoT 中心发送消息的客户端应用程序。

* 一个 Power BI 帐户。 （[免费试用 Power BI](https://powerbi.microsoft.com/)）

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>创建、配置和运行流分析作业

让我们首先创建一个流分析作业。 创建作业后，可以定义输入、输出和用于检索数据的查询。

### <a name="create-a-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “物联网” > “流分析作业”。   

2. 为作业输入以下信息。

   **作业名称**：作业的名称。 该名称必须全局唯一。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：与资源组使用同一位置。

   ![在 Azure 中创建流分析作业](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. 选择“创建”  。

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

1. 打开流分析作业。

2. 在“作业拓扑”下选择“输入”。  

3. 在中**输入**窗格中，选择**添加流输入**，然后选择**IoT 中心**从下拉列表。 在新输入窗格，输入以下信息：

   **输入别名**：输入的输入的唯一别名。

   **从你的订阅提供 IoT 中心**:选择此单选按钮。

   **订阅**：选择本教程使用的 Azure 订阅。

   **IoT 中心**：选择要用于本教程中的 IoT 中心。

   **终结点**：选择“消息传送”  。

   **共享访问策略名称**：选择你想 Stream Analytics 作业使用 IoT 中心的共享的访问策略的名称。 对于本教程中，你可以选择*服务*。 *服务*默认情况下，新的 IoT 中心上创建策略，并授予发送和接收云端终结点公开的 IoT 中心上的权限。 若要了解详细信息，请参阅[访问控制和权限](iot-hub-devguide-security.md#access-control-and-permissions)。

   **共享的访问策略密钥**:此字段是基于所选的共享的访问策略名称自动填充。

   **使用者组**：选择以前创建的使用者组。

   将所有其他字段保留其默认值。

   ![向 Azure 中的流分析作业添加输入](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. 选择“保存”。 

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下选择“输出”。  

2. 在中**输出**窗格中，选择**添加**并**Power BI**。

3. 上**Power BI-新的输出**窗格中，选择**Authorize**并按照提示登录到你的 Power BI 帐户。

4. 你已登录到 Power BI 后，输入以下信息：

   **输出别名**：用于输出的唯一别名。

   **组工作区**：选择目标组工作区。

   **数据集名称**：输入数据集名称。

   **表名称**：输入表名称。

   ![向 Azure 中的流分析作业添加输出](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. 选择“保存”。 

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

1. 在“作业拓扑”下选择“查询”。  

2. 将 `[YourInputAlias]` 替换为作业的输入别名。

3. 将 `[YourOutputAlias]` 替换为作业的输出别名。

   ![向 Azure 中的流分析作业添加查询](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. 选择“保存”。 

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在 Stream Analytics 作业中，选择**概述**，然后选择**启动** > **现在** > **启动**。 成功启动作业后，作业状态将从“已停止”  更改为“正在运行”  。

![在 Azure 中运行流分析作业](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>创建并发布实现数据可视化的 Power BI 报表

1. 确保示例应用程序正在设备上运行。 如果没有，请参考[设置设备](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)中的教程。

2. 登录到 [Power BI](https://powerbi.microsoft.com/en-us/) 帐户。

3. 选择你使用的工作区**我的工作区**。

4. 选择“数据集”。 

   此时应当会看到你在为流分析作业创建输出时指定的数据集。

5. 对于你创建的数据集，请选择**添加报表**（数据集名称右侧的第一个图标）。

   ![创建 Microsoft Power BI 报表](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. 创建折线图，显示某段时间的实时温度。

   1. 上**可视化效果**窗格中的报表创建页面上，选择折线图图标以添加折线图。

   2. 在“字段”窗格中展开一个表，该表是在为流分析作业创建输出时指定的。 

   3. 将 **EventEnqueuedUtcTime** 拖至“可视化效果”窗格中的“轴”。  

   4. 将“温度”拖至“值”。  

      已创建一个折线图。 X 轴显示 UTC 时区的日期和时间。 Y 轴显示来自传感器的温度。

      ![向 Microsoft Power BI 报表添加温度折线图](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. 创建另一个折线图，显示某段时间的实时湿度。 为此，请执行上述相同步骤，将 **EventEnqueuedUtcTime** 置于 X 轴，将“湿度”置于 Y 轴。 

   ![向 Microsoft Power BI 报表添加湿度折线图](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. 选择“保存”以保存报表。 

9. 选择**报表**左窗格中，然后选择刚才报表创建。

10. 选择**文件** > **发布到 web**。

11. 选择**创建嵌入代码**，然后选择**发布**。

要提供可以与报表访问的任何人共享的报表链接和可用于将报表集成到博客或网站中的代码片段。

![发布 Microsoft Power BI 报表](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft 还提供 [Power BI 移动应用](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/)，用于在移动设备上查看 Power BI 仪表板和报表并进行交互。

## <a name="next-steps"></a>后续步骤

现已成功使用 Power BI 可视化 Azure IoT 中心的实时传感器数据。

另一种方法可视化 Azure IoT 中心的数据，请参阅[使用 web 应用可视化 Azure IoT 中心的实时传感器数据](iot-hub-live-data-visualization-in-web-apps.md)。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
