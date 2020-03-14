---
title: 生成地图
description: 本文介绍如何在 Azure FarmBeats 中生成地图。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271767"
---
# <a name="generate-maps"></a>生成地图

使用 Azure FarmBeats，可以通过使用卫星图像和传感器数据输入来生成以下地图。 Maps 有助于查看场的地理位置，并识别设备的相应位置。

  - **传感器布局**：提供有关要使用的传感器的建议，以及在服务器场中放置它们的位置。
  - **卫星索引映射**：显示场的 vegetation 索引和水索引。
  - **土料湿气热度地图**：通过融合卫星数据和传感器数据来显示泥土湿气分布。

## <a name="sensor-placement-map"></a>传感器放置图

FarmBeats 传感器放置图可帮助放置土料湿度传感器。 地图输出包含传感器部署的一系列坐标。 这些传感器的输入与卫星图像一起使用，以生成土湿度热度地图。

此地图是通过将 canopy 划分为一年中的多个日期来进行的。 即使是 canopy 的一部分， 您可以删除该位置上不需要的传感器。 此地图适用于指南，你可以根据自己的自定义知识略微改变位置和数量。 添加传感器不会回退土湿度热度地图的结果，但如果传感器号降低，就有可能有损坏的热度地图准确性。

## <a name="before-you-begin"></a>开始之前

在尝试生成传感器放置图之前，请满足以下先决条件：

- 场大小必须为多个阿克里。
- 对于所选日期范围，云空闲的 Sentinel 场景数必须大于6。
- 至少六个无云的 Sentinel 状态必须具有大于或等于0.3 的规范化差异 Vegetation Index （NDVI）。

    > [!NOTE]
    > Sentinel 仅允许每个用户有两个并发线程。 因此，作业将排队等待，并且可能需要更长的时间才能完成。

### <a name="dependencies-on-sentinel"></a>Sentinel 上的依赖项

以下依赖项与 Sentinel 相关：

- 我们依赖于 Sentinel 性能来下载附属映像。 检查 Sentinel 性能状态和维护[活动](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)。
- Sentinel 仅允许每个用户有两个并发[下载线程](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)。
- 精确映射生成受[Sentinel 覆盖范围和重新访问频率]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)的影响。

## <a name="create-a-sensor-placement-map"></a>创建传感器放置图
本部分详细介绍了创建传感器放置图的过程。

> [!NOTE]
> 你可以从 "**映射**" 页或从 "**场详细信息**" 页上的 "**生成精度映射**" 下拉菜单启动传感器布局映射。

执行以下步骤。

1. 在主页上，从左侧导航菜单中转到 "**地图**"。
2. 选择 "**创建映射**"，然后从下拉菜单中选择 "**传感器位置**"。

    ![选择传感器位置](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. 选择 "**传感器布局**" 后，将显示 "**传感器布局**" 窗口。

    ![传感器布局窗口](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 从 "**场**" 下拉菜单中选择场。
   若要搜索并选择场，可以在下拉列表中滚动，或在文本框中输入场的名称。
5. 若要为上一年生成地图，请选择 "**推荐**"。
6. 若要生成自定义日期范围的映射，请选择 "**选择日期范围**" 选项。 输入要为其生成传感器放置图的开始日期和结束日期。
7. 选择 "**生成映射**"。
 此时会显示包含作业详细信息的确认消息。

  有关作业状态的信息，请参阅**查看作业**部分。 如果作业状态显示 "*失败*"，则会在 "*失败*" 状态的工具提示上出现详细的错误消息。 在这种情况下，请重复前面的步骤，然后重试。

  如果问题仍然存在，请参阅[故障排除](troubleshoot-azure-farmbeats.md)部分，或与[Azure FarmBeats 论坛联系以获得](https://aka.ms/FarmBeatsMSDN)相关日志支持。

### <a name="view-and-download-a-sensor-placement-map"></a>查看和下载传感器放置图

执行以下步骤。

1. 在主页上，从左侧导航菜单中转到 "**地图**"。

    ![从左侧导航菜单中选择 "映射"](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择 "**筛选器**"。
  "**筛选器**" 窗口显示搜索条件。

    ![筛选器窗口](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择 "**类型**"、"**日期**" 和 "**名称**" 值。 然后选择 "**应用**" 以搜索要查看的地图。
  创建作业的日期以格式显示，格式为 type_farmname_YYYY-MM-DD。
4. 在页面末尾使用导航栏，滚动浏览可用地图的列表。
5. 选择要查看的地图。 弹出窗口显示所选地图的预览。
6. 选择 "**下载**"，然后下载传感器坐标的 GeoJSON 文件。

    ![传感器位置地图预览](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>附属索引映射

以下部分介绍了创建和查看附属索引映射所涉及的过程。

> [!NOTE]
> 您可以从 "**映射**" 页或从 "**场详细信息**" 页上的 "**生成精度映射**" 下拉菜单启动附属索引映射。

FarmBeats 使你能够为场生成 NDVI、增强的 Vegetation 索引（EVI）和规范化的差水索引（NDWI）映射。 这些索引可帮助确定裁剪当前正在增长、在过去的发展情况，以及基础上的代表水源。


> [!NOTE]
> 生成映射的日期需要一个 Sentinel 映像。


## <a name="create-a-satellite-indices-map"></a>创建附属索引映射

执行以下步骤。

1. 在主页上，从左侧导航菜单中转到 "**地图**"。
2. 选择 "**创建映射**"，然后从下拉菜单中选择 "**附属索引**"。

    ![从下拉菜单中选择 "附属索引"](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. 选择 "**附属**索引" 后，将显示 "**附属索引**" 窗口。

    ![附属索引窗口](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 从下拉菜单中选择场。
   若要搜索并选择场，可以在下拉列表中滚动或输入场的名称。   
5. 若要为最后一周生成地图，请选择 "**本周**"。
6. 若要生成自定义日期范围的映射，请选择 "**选择日期范围**" 选项。 输入要为其生成卫星索引映射的开始日期和结束日期。
7. 选择 "**生成映射**"。
    此时会显示包含作业详细信息的确认消息。

    ![卫星索引映射确认消息](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    有关作业状态的信息，请参阅**查看作业**部分。 如果作业状态显示 "*失败*"，则会在 "*失败*" 状态的工具提示上出现详细的错误消息。 在这种情况下，请重复前面的步骤，然后重试。

    如果问题仍然存在，请参阅[故障排除](troubleshoot-azure-farmbeats.md)部分，或与[Azure FarmBeats 论坛联系以获得](https://aka.ms/FarmBeatsMSDN)相关日志支持。

### <a name="view-and-download-a-map"></a>查看和下载地图

执行以下步骤。

1. 在主页上，从左侧导航菜单中转到 "**地图**"。

    ![选择映射](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择 "**筛选器**"。 "**筛选器**" 窗口显示搜索条件。

    !["筛选器" 窗口显示搜索条件](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择 "**类型**"、"**日期**" 和 "**名称**" 值。 然后选择 "**应用**" 以搜索要查看的地图。
  创建作业的日期以格式显示，格式为 type_farmname_YYYY-MM-DD。

4. 在页面末尾使用导航栏，滚动浏览可用地图的列表。
5. 对于**场名称**和**日期**的每个组合，可以使用以下三个映射：
    - NDVI
    - EVI
    - NDWI
6. 选择要查看的地图。 弹出窗口显示所选地图的预览。
7. 从下拉菜单中选择 "**下载**" 以选择下载格式。 映射会下载并存储在计算机上的本地文件夹中。

    ![选择的卫星索引地图预览](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>土料湿气热度地图

土料湿气指的是在两个上料粒子之间的空格。 在您的场中，您可以在任何深度以高分辨率了解土里湿气数据。 若要生成准确且可用的土料湿气热度地图，需要对传感器进行统一部署。 所有传感器都必须来自同一个提供程序。 不同的提供程序的测量方式与上料湿气的测量方式不同，而校准的差别也不同。 使用在该深度部署的传感器为特定深度生成热度地图。

### <a name="before-you-begin"></a>开始之前

在尝试生成土潮湿热度地图之前，应满足以下先决条件：

- 必须至少部署三个泥土湿度传感器。 在传感器部署并与场关联之前，不要尝试创建土潮湿热度地图。
- 如果生成的是泥土湿气热度地图，则会受到 Sentinel 的路径范围、云覆盖和云阴影的影响。 在过去的120天内，必须有至少一个云免费的 Sentinel 场景可用于请求的热度地图。
- 在服务器场中部署的至少一半传感器必须处于联机状态，并将数据流式传输到 datahub。
- 必须使用同一提供程序中的传感器度量值生成热度地图。


## <a name="create-a-soil-moisture-heatmap"></a>创建土潮湿热度地图

执行以下步骤。

1. 在主页上，从左侧导航菜单中转到 **"映射"** ，以查看 "**映射**" 页。
2. 选择 "**创建地图**"，然后从下拉菜单中选择 "**土料湿气**"。

    ![从下拉菜单中选择 "土料湿气"](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. 选择 "**土料" 湿气**后，出现 "**土料湿气**" 窗口。

    ![土料湿气窗口](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 从 "**场**" 下拉菜单中选择场。
   若要搜索并选择你的场，你可以从下拉列表中滚动，或在 "**选择场**" 下拉菜单中输入场的名称。
5. 在 "**选择土料湿度传感器度量值**" 下拉菜单上，选择要为其生成地图的 "土料湿度传感器" 度量值（深度）。
若要找到传感器测量值，请参阅**传感器**，并选择任何泥土湿度传感器。 然后，在 "**传感器属性**" 部分的 "度量值**名称**" 中使用值。
6. 若要生成**当前**或**本周**的地图，请选择其中一个选项。
7. 若要生成自定义日期范围的映射，请选择 "**选择日期范围**" 选项。 输入要为其生成土料湿度热度地图的开始和结束日期。
8. 选择 "**生成映射**"。
 此时会显示包含作业详细信息的确认消息。

   ![土料湿气地图确认消息](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    有关作业状态的信息，请参阅**查看作业**部分。 如果作业状态显示 "*失败*"，则会在 "*失败*" 状态的工具提示上出现详细的错误消息。 在这种情况下，请重复前面的步骤，然后重试。

    如果问题仍然存在，请参阅[故障排除](troubleshoot-azure-farmbeats.md)部分，或与[Azure FarmBeats 论坛联系以获得](https://aka.ms/FarmBeatsMSDN)相关日志支持。

### <a name="view-and-download-a-map"></a>查看和下载地图

执行以下步骤。

1. 在主页上，从左侧导航菜单中转到 "**地图**"。

    ![中转到地图](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择 "**筛选器**"。 此时将显示 "**筛选器**" 窗口，您可以在其中搜索地图。

    ![从左侧导航栏中选择 "筛选器"](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  从下拉菜单中选择 "**类型**"、"**日期**" 和 "**名称**" 值。 然后选择 "**应用**" 以搜索要查看的地图。 创建作业的日期以格式显示，格式为 type_farmname_YYYY-MM-DD。
4. 选择表标题旁边的**排序**图标，按**场**、**日期**、**创建时间**、**作业 ID**和**作业类型**进行排序。
5. 使用页面末尾的导航按钮，滚动浏览可用地图的列表。
6. 选择要查看的地图。 弹出窗口显示所选地图的预览。
7. 从下拉菜单中选择 "**下载**" 以选择下载格式。 映射将下载并存储在指定的文件夹中。

    ![土潮湿热度地图预览](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
