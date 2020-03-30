---
title: 生成地图
description: 本文介绍如何在 Azure FarmBeats 中生成地图。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271767"
---
# <a name="generate-maps"></a>生成地图

使用 Azure FarmBeats，可以使用卫星图像和传感器数据输入生成以下地图。 地图可帮助您查看服务器场的地理位置，并确定设备的适当位置。

  - **传感器放置图**：提供有关要使用的传感器数以及将其放置在服务器场上的位置的建议。
  - **卫星索引图**：显示农场的植被指数和水指数。
  - **土壤水分热图**：通过融合卫星数据和传感器数据来显示土壤水分分布。

## <a name="sensor-placement-map"></a>传感器放置图

FarmBeats 传感器放置图可帮助您放置土壤湿度传感器。 地图输出由传感器部署的坐标列表组成。 这些传感器的输入与卫星图像一起使用，以生成土壤水分热图。

此地图是通过分割树冠派生的，这些树冠在一年中多个日期中可见。 即使是裸露的土壤和建筑物也是树冠的一部分。 您可以移除该位置不需要的传感器。 此地图用于指导，您可以根据您的自定义知识稍微更改位置和数字。 添加传感器不会使土壤水分热图结果倒退，但如果减少传感器数量，热图精度可能会下降。

## <a name="before-you-begin"></a>开始之前

在尝试生成传感器放置贴图之前，满足以下先决条件：

- 农场面积必须超过一英亩。
- 所选日期范围的无云哨兵场景数必须超过 6 个。
- 至少有六个无云哨兵场景的标准化差异植被索引 （NDVI） 大于或等于 0.3。

    > [!NOTE]
    > Sentinel 只允许每个用户使用两个并发线程。 因此，作业会排队，可能需要更长时间才能完成。

### <a name="dependencies-on-sentinel"></a>对哨兵的依赖

以下依赖项与 Sentinel 相关：

- 我们依靠哨兵性能来下载卫星图像。 检查哨兵性能状态和维护[活动](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)。
- Sentinel 只允许每个用户仅[下载两个并发线程](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)。
- 精确地图生成受[哨兵覆盖和重访频率]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)的影响。

## <a name="create-a-sensor-placement-map"></a>创建传感器放置贴图
本节详细介绍了创建传感器放置贴图的过程。

> [!NOTE]
> 可以从 **"地图"** 页或"**生成精度地图"** 下拉菜单""""""""""""**移动详细信息"** 页上启动传感器放置地图。

执行以下步骤。

1. 在主页上，从左侧导航菜单转到 **"地图**"。
2. 选择 **"创建地图**"，然后从下拉菜单中选择 **"传感器放置**"。

    ![选择传感器放置](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. 选择 **"传感器放置**"后，将显示 **"传感器放置"** 窗口。

    ![传感器放置窗口](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. 从 **"服务器场**"下拉菜单中选择服务器场。
   要搜索和选择服务器场，可以在下拉列表中滚动或在文本框中输入服务器场的名称。
5. 要生成去年地图，请选择 **"推荐**"。
6. 要为自定义日期范围生成地图，请选择 **"选择日期范围"** 选项。 输入要为其生成传感器放置贴图的开始日期和结束日期。
7. 选择 **"生成地图**"。
 将显示一条带有作业详细信息的确认消息。

  有关作业状态的信息，请参阅 **"查看作业**"部分。 如果作业状态显示 *"失败"，* 则 *"失败"* 状态的工具提示上将显示一条详细的错误消息。 在这种情况下，请重复前面的步骤，然后重试。

  如果问题仍然存在，请参阅[疑难解答](troubleshoot-azure-farmbeats.md)部分或与 Azure [FarmBeats 论坛联系，以寻求](https://aka.ms/FarmBeatsMSDN)相关日志的支持。

### <a name="view-and-download-a-sensor-placement-map"></a>查看和下载传感器放置地图

执行以下步骤。

1. 在主页上，从左侧导航菜单转到 **"地图**"。

    ![从左侧导航菜单中选择"地图"](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航中选择 **"筛选器**"。
  **"筛选器"** 窗口显示搜索条件。

    ![过滤器窗口](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择 **"键入**"、"**日期**"和 **"名称"** 值。 然后选择 **"应用"** 以搜索要查看的地图。
  创建作业的日期以type_farmname_YYYY-MM-DD 格式显示。
4. 使用页面末尾的导航栏滚动浏览可用的地图列表。
5. 选择要查看的地图。 弹出窗口显示所选地图的预览。
6. 选择 **"下载**"，然后下载传感器坐标的 GeoJSON 文件。

    ![传感器放置贴图预览](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>卫星指数地图

以下各节介绍创建和查看卫星索引地图所涉及的过程。

> [!NOTE]
> 可以从 **"地图"** 页或"**生成精度地图**"下拉菜单""""""""""""""**计划详细信息"** 页上启动卫星索引地图。

FarmBeats 使您能够为农场生成 NDVI、增强植被指数 （EVI） 和标准化差差水指数 （NDWI） 地图。 这些指数有助于确定作物目前如何生长，或在过去增长，以及具有代表性的地面水位。


> [!NOTE]
> 生成地图的天数需要哨兵图像。


## <a name="create-a-satellite-indices-map"></a>创建卫星索引地图

执行以下步骤。

1. 在主页上，从左侧导航菜单转到 **"地图**"。
2. 选择 **"创建地图**"，然后从下拉菜单中选择 **"卫星索引**"。

    ![从下拉菜单中选择卫星索引](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. 选择 **"卫星索引**"后，将显示 **"卫星索引"** 窗口。

    ![卫星索引窗口](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 从下拉菜单中选择服务器场。
   要搜索和选择服务器场，可以在下拉列表中滚动或输入服务器场的名称。   
5. 要生成上周的地图，请选择 **"本周**"。
6. 要为自定义日期范围生成地图，请选择 **"选择日期范围"** 选项。 输入要为其生成卫星索引地图的开始日期和结束日期。
7. 选择 **"生成地图**"。
    将显示一条带有作业详细信息的确认消息。

    ![卫星索引地图确认消息](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    有关作业状态的信息，请参阅 **"查看作业**"部分。 如果作业状态显示 *"失败"，* 则 *"失败"* 状态的工具提示上将显示一条详细的错误消息。 在这种情况下，请重复前面的步骤，然后重试。

    如果问题仍然存在，请参阅[疑难解答](troubleshoot-azure-farmbeats.md)部分或与 Azure [FarmBeats 论坛联系，以寻求](https://aka.ms/FarmBeatsMSDN)相关日志的支持。

### <a name="view-and-download-a-map"></a>查看和下载地图

执行以下步骤。

1. 在主页上，从左侧导航菜单转到 **"地图**"。

    ![选择地图](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航中选择 **"筛选器**"。 **"筛选器"** 窗口显示搜索条件。

    ![筛选器窗口显示搜索条件](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 从下拉菜单中选择 **"键入**"、"**日期**"和 **"名称"** 值。 然后选择 **"应用"** 以搜索要查看的地图。
  创建作业的日期以type_farmname_YYYY-MM-DD 格式显示。

4. 使用页面末尾的导航栏滚动浏览可用的地图列表。
5. 对于**服务器场名称**和**日期**的每个组合，可以使用以下三个映射：
    - Ndvi
    - EVI
    - NDWI
6. 选择要查看的地图。 弹出窗口显示所选地图的预览。
7. 选择"从下拉菜单**下载**"以选择下载格式。 地图将下载并存储在计算机上的本地文件夹中。

    ![选定的卫星索引地图预览](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>土壤水分热图

土壤水分是土壤颗粒之间的空间中持有的水。土壤水分热图可帮助您了解任何深度的土壤水分数据，在农场内以高分辨率了解土壤水分数据。 要生成准确且可用的土壤水分热图，需要统一部署传感器。 所有传感器必须来自同一供应商。 不同的供应商在土壤湿度的测量方式上存在差异，以及校准的差异。 使用部署于该深度的传感器，为特定深度生成热图。

### <a name="before-you-begin"></a>开始之前

在尝试生成土壤水分热图之前，满足以下先决条件：

- 必须至少部署三个土壤水分传感器。 在部署传感器并与服务器场关联之前，不要尝试创建土壤水分热图。
- 生成土壤水分热图受哨兵的路径覆盖范围、云覆盖和云阴影的影响。 从要求土壤水分热图的那天起，至少至少一个无云哨兵场景必须在过去 120 天内可用。
- 服务器场上部署的至少一半的传感器必须处于联机状态，并且数据必须流式传输到数据库。
- 热图必须使用来自同一提供程序的传感器度量来生成。


## <a name="create-a-soil-moisture-heatmap"></a>创建土壤水分热图

执行以下步骤。

1. 在主页上，从左侧导航菜单转到 **"地图**"以查看 **"地图"** 页。
2. 选择 **"创建地图**"，然后从下拉菜单中选择 **"土壤水分**"。

    ![从下拉菜单中选择土壤水分](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. 选择**土壤水分**后，将显示 **"土壤水分"** 窗口。

    ![土壤水分窗口](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. 从 **"服务器场**"下拉菜单中选择服务器场。
   要搜索和选择服务器场，可以从下拉列表中滚动或在 **"选择服务器场**下拉菜单"中输入服务器场的名称。
5. 在 **"选择土壤水分传感器测量**下拉菜单"中，选择要为其生成地图的土壤水分传感器度量（深度）。
要查找传感器测量值，请转到**传感器**，然后选择任何土壤湿度传感器。 然后，在 **"传感器属性"** 部分下，使用**度量值名称**中的值。
6. 要生成 **"今天**"或"**本周"** 的地图，请选择其中一个选项。
7. 要为自定义日期范围生成地图，请选择 **"选择日期范围"** 选项。 输入要为其生成土壤水分热图的开始日期和结束日期。
8. 选择 **"生成地图**"。
 将显示一条带有作业详细信息的确认消息。

   ![土壤水分图确认消息](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    有关作业状态的信息，请参阅 **"查看作业**"部分。 如果作业状态显示 *"失败"，* 则 *"失败"* 状态的工具提示上将显示一条详细的错误消息。 在这种情况下，请重复前面的步骤，然后重试。

    如果问题仍然存在，请参阅[疑难解答](troubleshoot-azure-farmbeats.md)部分或与 Azure [FarmBeats 论坛联系，以寻求](https://aka.ms/FarmBeatsMSDN)相关日志的支持。

### <a name="view-and-download-a-map"></a>查看和下载地图

执行以下步骤。

1. 在主页上，从左侧导航菜单转到 **"地图**"。

    ![转到地图](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 从窗口的左侧导航中选择 **"筛选器**"。 **"筛选"** 窗口显示可以从中搜索地图。

    ![从左侧导航中选择"筛选器"](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  从下拉菜单中选择 **"键入**"、"**日期**"和 **"名称"** 值。 然后选择 **"应用"** 以搜索要查看的地图。 创建作业的日期以type_farmname_YYYY-MM-DD 格式显示。
4. 选择表标题旁边的**排序**图标，以便根据 **"农场**"、**日期**、**创建时间**、**作业 ID**和**作业类型**进行排序。
5. 使用页面末尾的导航按钮滚动浏览可用的地图列表。
6. 选择要查看的地图。 弹出窗口显示所选地图的预览。
7. 选择"从下拉菜单**下载**"以选择下载格式。 地图将下载并存储在指定的文件夹中。

    ![土壤水分热图预览](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
