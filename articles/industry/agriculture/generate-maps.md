---
title: 生成映射
description: 介绍如何在 FarmBeats 中生成映射
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6cdebb8f6abea2b50dd8aad58ccc9fb5e680cba9
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798474"
---
# <a name="generate-maps"></a>生成映射

使用 Azure FarmBeats，可以通过使用卫星图像和传感器数据输入来生成以下地图。 Map 有助于查看场的地理位置，并识别设备的相应位置。

  -  **传感器放置图**-提供有关要使用的传感器的建议，以及在服务器场中放置它们的位置。
  - **卫星索引 map** –显示场的 vegetation 索引和水索引。
  - **土料湿气地图**–通过融合卫星数据和传感器数据来显示泥土湿气分布。

## <a name="sensor-placement-maps"></a>传感器放置图

FarmBeats 传感器放置图可帮助放置土料湿度传感器。 地图输出包含传感器部署的一系列坐标。 这些传感器的输入与卫星图像一起使用，以生成土湿度热度地图。  

此地图是通过在一年中的多个日期对 canopy 进行分段来派生的，甚至是一个 canopy 的组成部分。 您可以删除该位置上不需要的传感器。 此地图适用于指南，你可以根据自己的自定义知识略微改变位置和数字（添加传感器不会回退，但如果减少传感器编号，则可能会有损坏）。  

## <a name="before-you-begin"></a>开始之前  

尝试生成传感器放置图之前，请确保以下各项：

- 场大小必须为多个阿克里。
- 对于所选日期范围，云空闲的 Sentinel 场景数必须大于6。  
- 至少六个云免费的 Sentinel 场景必须具有 NDVI > = 0.3。

    > [!NOTE]
    > Sentinel 仅允许每个用户有两个并发线程。 因此，作业将排队，并可能需要更长的时间才能完成。

### <a name="dependencies-on-sentinel"></a>Sentinel 上的依赖项  

下面是 Sentinel 依赖项：

- 我们依赖于 Sentinel 性能来下载附属映像。 检查 Sentinel 性能状态和维护[活动](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)。
- Sentinel 仅允许每个用户有两个并发[下载线程](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)。
- Precision map 生成将受[Sentinel 覆盖范围和重新访问频率]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)的影响。

## <a name="create-sensor-placement-map"></a>创建传感器放置图
部分详细介绍了创建传感器放置图的过程。

> [!NOTE]
> 你可以从 "**映射**" 页或从 "**场详细信息**" 页上的 "**生成精度映射**" 下拉菜单启动传感器布局。

请执行以下步骤：

1. 在主页上，从左侧导航菜单中转到 "**地图**"。
2. 选择 "**创建映射**"，然后从下拉菜单中选择 "**传感器布局**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. 选择 "**传感器位置**"。
  显示 "传感器布局" 窗口。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 从 "**场**" 下拉菜单中选择场。  
   若要搜索并选择场，可以在下拉列表中滚动，或在文本框中键入场的名称。
5. 如果要为上一年生成地图，请选择 "**推荐**" 选项。
6. 如果要生成自定义日期范围的映射，请选择 "**选择日期范围**"，并提供要生成传感器放置图的开始和结束日期。
7. 选择 "**生成映射**"。
 此时会显示包含作业详细信息的确认消息。

  有关作业状态的信息，请参阅**查看作业**部分。 如果作业状态显示为 "*失败*"，则会在 "*失败*" 状态的工具提示上显示详细的错误消息。 在这种情况下，请重复上面列出的步骤，然后重试。

  如果问题仍然存在，请参阅[故障排除](troubleshoot-project-farmbeats.md)部分，或与[Azure FarmBeats 论坛联系以获得](https://aka.ms/FarmBeatsMSDN)相关日志支持。

### <a name="view-and-download-sensor-placement-map"></a>查看和下载传感器放置图

请执行以下步骤：

1. 在主页上，从左侧导航菜单中转到 "**地图**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择 "**筛选器**"。
  "**筛选器**" 窗口显示搜索条件。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择 "**类型**"、"**日期**" 和 "**名称**"，然后选择 "**应用**" 以搜索要查看的地图。
  创建作业的日期以 type_farmname_YYYY-MM 格式显示。
4. 在页面末尾使用导航栏，滚动浏览可用地图的列表。
5. 选择要查看的地图。 弹出窗口显示所选地图的预览。
6. 选择 "**下载**"，然后下载传感器坐标的 GeoJSON 文件。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>附属索引映射

以下部分介绍了创建和查看附属索引映射所涉及的过程。

> [!NOTE]
> 您可以从 "**映射**" 页或从 "**场详细信息**" 页上的 "**生成精度映射**" 下拉菜单启动附属索引映射。

FarmBeats 使你能够生成 Vegetation 的规范化差异索引（NDVI）、增强的 Vegetation 索引（EVI）和规范化的差水索引（NDWI）映射。 这些索引可帮助确定裁剪当前正在增长、在过去的发展情况，以及基础上的代表水源。


> [!NOTE]
> 生成映射的日期需要一个 Sentinel 映像。


## <a name="create-satellite-indices-map"></a>创建附属索引映射

请执行以下步骤：

1. 在主页上，从左侧导航菜单中转到 "**地图**"。
2. 从下拉菜单中选择 "**创建映射**"，然后选择 "**附属索引**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. 选择 "**附属索引**"。
  将显示 "附属索引" 窗口。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 从下拉菜单中选择场。  
   若要搜索并选择场，可以在下拉列表中滚动或键入场的名称。   
5. 如果要生成上一周的地图，请选择 "**本周**" 选项。
6. 如果要生成自定义日期范围的映射，请选择 "**选择日期范围**"，并提供要生成附属索引映射的开始和结束日期。
7. 选择 "**生成映射**"。
    此时会显示包含作业详细信息的确认消息。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    有关作业状态的信息，请参阅**查看作业**部分。 如果作业状态显示为 "*失败*"，则会在 "*失败*" 状态的工具提示上显示详细的错误消息。 在这种情况下，请重复上面列出的步骤，然后重试。

    如果问题仍然存在，请参阅[故障排除](troubleshoot-project-farmbeats.md)部分，或与[Azure FarmBeats 论坛联系以获得](https://aka.ms/FarmBeatsMSDN)相关日志支持。

### <a name="view-and-download-map"></a>查看和下载地图

请执行以下步骤：

1. 在主页上，从左侧导航菜单中转到 "**地图**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择 "**筛选器**"，"**筛选器**" 窗口将显示搜索条件。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择 "**类型**"、"**日期**" 和 "**名称**"，然后选择 "**应用**" 以搜索要查看的地图。
  创建作业的日期以 type_farmname_YYYY-MM 格式显示。

4. 在页面末尾使用导航栏，滚动浏览可用地图的列表。
5. 对于**场名称**和**日期**的每个组合，可以使用以下三个映射：
    - NDVI
    - EVI
    - NDWI
6. 选择要查看的地图。 弹出窗口显示所选地图的预览。
7. 选择 "**下载**" 下拉菜单以选择下载格式，并将地图下载并存储在计算机上的本地文件夹中。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>获取土料湿气热度地图

土里湿气是在两个土 在您的服务器场中，您可以在任何深度以高分辨率了解土里湿气数据。 若要生成准确且可用的上料湿气热度地图，需要统一部署传感器，其中所有传感器都来自相同的提供程序。 不同的提供程序的测量方式与上料湿度的测量方式不同，而校准的差别也不同。 使用在该深度部署的传感器为特定深度生成热度地图。

### <a name="before-you-begin"></a>开始之前  

在尝试生成土潮湿热度地图之前，请确保以下各项：

- 必须至少部署三个泥土湿度传感器。 Microsoft 建议您在部署传感器并将其与场关联之前，不要尝试创建土里湿气地图。  
- 在过去的120天内，至少有一个云免费的 Sentinel 场景必须可用于从其请求了土料湿气地图的那一天。
- 在服务器场中部署的至少一半传感器必须处于联机状态，并将数据流式传输到数据中心。
- 应使用来自同一个提供程序的传感器度量值生成热度地图。


## <a name="create-soil-moisture-heatmap"></a>创建土料湿气热度地图

请执行以下步骤：

1. 在主页上，从左侧导航菜单中转到 **"映射"** ，以查看 "映射" 页。
2. 从下拉菜单中选择 "**创建地图**" 并选择 "**土料湿气**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. 选择 "**土料湿气**"。
    显示 "土料湿气" 窗口。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 从 "**场**" 下拉菜单中选择场。  
   若要搜索并选择你的场，你可以从下拉列表中滚动，或在 "选择场" 下拉菜单中键入场的名称。
5. 在 "**选择土料湿度传感器度量值**" 下拉菜单中，选择要为其生成地图的土料湿度传感器度量值（深度）。
若要找到传感器测量值，请参阅**传感器**，选择任何泥土湿度传感器。 然后，在 "**传感器属性**" 部分的 "**度量值名称**" 中使用值
6. 如果要为**今天**或**本周**生成，请选择其中一个选项。
7. 如果要生成自定义日期范围，请选择 "**选择日期范围**"，并提供要为其生成土区间湿气地图的开始和结束日期。
8. 选择 "**生成映射**"。
 此时会显示包含作业详细信息的确认消息。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    有关作业状态的信息，请参阅**查看作业**部分。 如果作业状态显示为 "*失败*"，则会在 "*失败*" 状态的工具提示上显示详细的错误消息。 在这种情况下，请重复上面列出的步骤，然后重试。

    如果问题仍然存在，请参阅[故障排除](troubleshoot-project-farmbeats.md)部分，或与[Azure FarmBeats 论坛联系以获得](https://aka.ms/FarmBeatsMSDN)相关日志支持。

### <a name="view-and-download-map"></a>查看和下载地图

请执行以下步骤：

1. 在主页上，从左侧导航菜单中转到 "**地图**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航栏中选择 "**筛选器**"，"**筛选器**" 窗口将显示，您可以从该窗口中搜索地图。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  从下拉菜单中选择 "**类型**"、"**日期**" 和 "**名称**"，然后选择 "**应用**" 以搜索要查看的地图。 创建作业的日期以 type_farmname_YYYY-MM 格式显示。
4. 选择表标题旁边的**排序**图标，按场、日期、创建时间、作业 ID 和作业类型进行排序。
5. 使用页面末尾的导航按钮滚动浏览可用地图的列表。
6. 选择要查看的地图。 弹出窗口显示所选地图的预览。
7. 选择 "**下载**" 下拉菜单以选择下载格式，并将地图下载并存储在计算机的本地文件夹中。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
