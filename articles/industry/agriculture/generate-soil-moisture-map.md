---
title: 生成土潮湿热度地图
description: 介绍如何在 Azure FarmBeats 中生成土料湿气热度地图
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e11f560c2b101d86d41feb3f6c93cffe7c1a748d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851445"
---
# <a name="generate-soil-moisture-heatmap"></a>生成土潮湿热度地图

土里湿气是在两个土 土里湿气热度地图有助于您了解湿气数据，并在您的场中获得高分辨率。 若要生成准确的可用土料湿气热度地图，需要从同一提供商处统一部署传感器。 不同的提供程序的测量方式与上料湿度的测量方式不同，而校准的差别也不同。 使用在该深度部署的传感器为特定深度生成热度地图。

本文介绍使用 Azure FarmBeats 加速器为你的场生成土料湿气热度地图的过程。 本文介绍如何执行以下操作：

- [创建场](#create-a-farm)
- [将传感器分配到场](#get-soil-moisture-sensor-data-from-partner)
- [生成土潮湿热度地图](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>开始之前

确保以下信息：  

- Azure 订阅。
- Azure FarmBeats 的运行实例。
- 服务器场可使用最少三个泥土湿度传感器。

## <a name="create-a-farm"></a>创建场

场是要为其创建土料湿气热度地图的地理区域。 可以使用[场 API](https://aka.ms/FarmBeatsDatahubSwagger)或[FarmsBeats 加速器 UI](manage-farms.md#create-farms)创建场

## <a name="deploy-sensors"></a>部署传感器

应在服务器场中物理部署土料湿度传感器。 你可以从任何已获批准的合作伙伴（ [Davis](https://www.davisinstruments.com/product/enviromonitor-gateway/) ）和[Teralytic](https://teralytic.com/)购买土潮湿传感器。 应与传感器提供商协调，以便在您的场中完成物理设置。

## <a name="get-soil-moisture-sensor-data-from-partner"></a>从合作伙伴获取土料湿度传感器数据

传感器开始流式传输数据时，会将数据传入到 Azure FarmBeats。 这可以通过合作伙伴应用程序来完成。

例如，如果你已购买 Davis 传感器，你将登录到你的天气链接帐户，并提供所需的凭据以使数据流进入 Azure FarmBeats。 若要获取所需的凭据，请按照[获取传感器数据](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)中的说明进行操作。

输入凭据并在合作伙伴应用程序上选择 "**提交**" 后，可以将数据流入 Azure FarmBeats。

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>将泥土湿气传感器分配到场

将传感器帐户链接到 Azure FarmBeats 后，需要将土潮湿传感器分配给感兴趣的场。

1.  在主页中，从菜单中选择 "**服务器场**"，随即会显示 "**服务器场**" 列表页。
2.  选择**MyFarm** > "**添加设备**"。
3.  将显示 "**添加设备**" 窗口。 选择链接到场的 "土料湿气传感器" 的任何设备。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 选择 "**添加设备**"。     

## <a name="generate-soil-moisture-heatmap"></a>生成土潮湿热度地图

此步骤将创建一个作业或长时间运行的操作，该操作将为你的场生成土料湿气热度地图。

1.  在主页上，从左侧导航菜单中转到 "**场**" 以查看 "服务器场" 页。
2.  选择**MyFarm**。
3.  在**场详细信息**页中，选择 "**生成精度映射**"。
4.  从下拉菜单中，选择 "**土料湿气**"。
5.  在 "**土里湿气**" 窗口中选择 "**本周**"。
6.  在 "**选择土料湿度** **传感器" 度量值**中，输入要用于地图的度量值。
    若要查找传感器度量值，请在 "**传感器**" 中选择任何泥土湿度传感器。 在 "**传感器属性**" 中，使用 "**度量值名称**" 值。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  选择 "**生成映射**"。
    将显示包含作业详细信息的确认消息。 有关详细信息，请参阅作业中的作业状态。

    >[!NOTE]
    > 作业大约需要3到4小时才能完成。

### <a name="download-the-soil-moisture-heatmap"></a>下载土潮湿热度地图

请执行以下步骤：

1. 在 "**作业**" 页上，检查你在上一个过程中创建的作业的**作业状态**。
2. 当作业状态显示为 "*成功*" 时，单击菜单上的 "**映射**"。
3. 按 < "中 moisture_MyFarm_YYYY" > 的格式搜索地图的创建日期。
4. 在 "**名称**" 列中选择一个映射，随即会显示一个弹出窗口，其中包含所选映射的预览。
5. 选择“下载”。 下载地图，并将其存储在计算机的本地文件夹中。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>后续步骤

现在，你已成功生成了土料湿气热度地图，接下来了解如何[生成传感器位置](generate-maps.md#sensor-placement-map)并引入[历史遥测数据](ingest-historical-telemetry-data.md)。 
