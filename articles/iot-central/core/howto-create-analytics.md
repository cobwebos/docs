---
title: 在 Azure IoT Central 应用程序中分析设备数据 | Microsoft Docs
description: 在 Azure IoT Central 应用程序中分析设备数据。
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 35c085c74cb6713f52d551a188758a7427a93576
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954027"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>如何使用分析功能来分析设备数据

*本文适用于操作员、构建者和管理员。*

Azure IoT Central 提供各种分析功能对来自设备的大量数据进行分析。 若要开始，请访问左窗格中的 "**分析**"。

## <a name="querying-your-data"></a>查询数据

若要开始，需要选择一个**设备集**，添加一个**筛选器**（可选），然后选择一个**时间段**。 完成后，选择 "**显示结果**"，开始可视化数据。

* **设备集：** [设备集](howto-use-device-sets.md)是用户定义的设备组。 例如，奥克兰市的所有冷冻机，或所有版本 2.0 的风力涡轮机。

* **筛选器：** 可以选择性地在搜索中添加筛选器，以专注于分析数据。 一次最多可以添加 10 个筛选器。 例如，在奥克兰市的所有冷冻机中，找到温度超过 60 度的冷冻机。
* **时间段：** 默认会检索过去 10 分钟的数据。 可将此值更改为预定义的时间范围之一，或者选择自定义的时间段。

  ![Analytics 查询](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>可视化数据

查询数据后，可以开始将数据可视化。 可以显示/隐藏度量、更改数据的聚合方式，以及按不同的设备属性进一步拆分数据。  

* **拆分依据：** 按设备属性拆分数据可以进一步向下钻取到数据。 例如，可按设备 ID 或位置拆分结果。

* **度量：** 一次可以选择显示/隐藏设备报告的最多 10 个不同遥测项。 度量是指温度和湿度等属性。

* **聚合：** 默认按平均值聚合数据，但可以根据需要更改为其他数据聚合方式。

   ![Analytics 可视化效果拆分依据](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>与数据交互

您可以通过多种方式来更改查询结果，以满足您的可视化需求。 您可以在关系图视图和网格视图之间进行切换，放大和缩小，刷新数据集，以及更改行的显示方式。

* **显示网格：** 你的结果以表格格式提供，可让你查看每个数据点的特定值。 此视图也符合易访问性标准。
* **显示图表：** 结果以线条格式显示，可帮助您识别向上或向下趋势和异常。

  ![显示分析数据的网格视图](media/howto-create-analytics/analytics-showgrid.png)

缩放可让你在数据上回家。 如果你在结果集中发现了一个想要重点关注的时间段，可将光标移到想要放大的区域，然后使用可用的控件来执行以下操作之一：

* **放大：** 选择一个时间段后，将启用 "放大" 并使你能够放大到你的数据。
* **缩小：** 使用此控件可在上次缩放大小的基础上缩小一个级别。 例如，如果您已经放大了三次数据，则缩小会使您一次返回一个步骤。
* **缩放重置：** 执行不同级别的缩放后，可以使用缩放重置控件还原到原始结果集。

  ![对数据执行缩放](media/howto-create-analytics/analytics-zoom.png)

可根据需要更改线条样式。 有四个选项：

* **行：** 每个数据点之间的平线。
* **光滑：** 每个点之间的曲线。
* **步骤：** 图中每个点之间的直线是一个步骤。
* **散点图：** 所有点都绘制在图表上，但不会连接它们。

  ![Analytics 中提供的不同线条类型](media/howto-create-analytics/analytics-linetypes.png)

最后，通过选择以下三种模式之一，可以在 Y 轴上排列数据：

* **堆积：** 将每个度量的图堆积在一起，每个图都有自己的 Y 轴。 在选择多个度量的情况下，如果需要以独立的视图来呈现这些度量，则可使用堆积图。
* **非堆积：** 使用一个 Y 轴为每个度量值绘图，但 Y 轴的值会随突出显示的度量值而更改。 如果需要将多个度量值重叠在一起，并且需要跨这些度量值查看同一时间范围的模式，则可使用非堆积图。
* **共享 Y 轴：** 所有图共享同一 Y 轴，轴的值不会更改。 如果需要在使用拆分方式对数据进行切分时查看单个度量值，则可使用共享 Y 轴图。

  ![使用不同的可视化模式在 Y 轴上排列数据](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>后续步骤

了解如何创建适用于 Azure IoT Central 应用程序的自定义分析后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [准备和连接 Node.js 应用程序](howto-connect-nodejs.md)