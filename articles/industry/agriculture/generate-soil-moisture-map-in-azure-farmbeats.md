---
title: 生成土壤水分热图
description: 描述如何在 Azure 农场节拍中生成土壤水分热图
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a2115e9c1601c86cce8857c10baf12b91cc2b997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482564"
---
# <a name="generate-soil-moisture-heatmap"></a>生成土壤水分热图

土壤水分是土壤颗粒之间的空间中持有的水。土壤水分热图可帮助您了解任何深度和农场内高分辨率的水分数据。 要生成准确且可用的土壤湿度热图，需要统一部署来自同一供应商的传感器。 不同的供应商在测量土壤水分的方式上存在差异，同时校准也存在差异。 使用部署于该深度的传感器为特定深度生成热图。

本文介绍使用 Azure FarmBeats 加速器为服务器场生成土壤水分热图的过程。 本文介绍如何执行以下操作：

- [创建服务器场](#create-a-farm)
- [将传感器分配给服务器场](#get-soil-moisture-sensor-data-from-partner)
- [生成土壤水分热图](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>开始之前

确保以下信息：  

- Azure 订阅。
- Azure 农场节拍的运行实例。
- 农场至少提供三个土壤水分传感器。

## <a name="create-a-farm"></a>创建场

农场是一个感兴趣的地理区域，您要为此创建土壤水分热图。 您可以使用[服务器场 API](https://aka.ms/FarmBeatsDatahubSwagger)或在[服务器场节拍加速器 UI](manage-farms-in-azure-farmbeats.md#create-farms)中创建服务器场

## <a name="deploy-sensors"></a>部署传感器

您应该在农场上物理部署土壤湿度传感器。 你可以从我们批准的任何合作伙伴 -[戴维斯仪器](https://www.davisinstruments.com/product/enviromonitor-gateway/)和[太溶物](https://teralytic.com/)购买土壤水分传感器。 您应该与传感器提供程序协调，以在服务器场上执行物理设置。

## <a name="get-soil-moisture-sensor-data-from-partner"></a>从合作伙伴处获取土壤水分传感器数据

当传感器开始流式传输数据时，数据将启用到 Azure FarmBeats 中。 这可以从合作伙伴应用程序完成。

例如，如果您购买了 Davis 传感器，您将登录到天气链接帐户，并提供所需的凭据以启用数据流式传输到 Azure FarmBeats 中。 要获取所需的凭据，请按照[获取传感器数据](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)中的说明进行操作。

输入凭据并在合作伙伴应用程序中选择 **"提交"** 后，可以将数据流入 Azure FarmBeats 中。

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>将土壤水分传感器分配给农场

将传感器帐户链接到 Azure FarmBeats 后，需要将土壤湿度传感器分配给感兴趣的服务器场。

1.  在主页中，从菜单中选择 **"服务器场**"，将显示 **"服务器场"** 列表页。
2.  选择 **"我的农场** > **添加设备**"。
3.  将显示"**添加设备"** 窗口。 选择与农场土壤湿度传感器链接的任何设备。

    ![FarmBeats 项目](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 选择 **"添加设备**"。     

## <a name="generate-soil-moisture-heatmap"></a>生成土壤水分热图

此步骤是创建作业或长时间运行的操作，为您的农场生成土壤水分热图。

1.  在主页上，从左侧导航菜单转到 **"服务器场"** 以查看服务器场页面。
2.  选择 **"我的农场**"。
3.  在 **"服务器场详细信息"** 页中，选择 **"生成精度映射**"。
4.  从下拉菜单中，选择**土壤水分**。
5.  在**土壤湿度**窗口中，选择**本周**。
6.  在 **"选择土壤水分****传感器度量"中**，输入要用于地图的度量值。
    要查找传感器测量值，请在**传感器**中选择任何土壤湿度传感器。 在 **"传感器属性**"中，使用**度量值名称**值。

    ![FarmBeats 项目](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  选择 **"生成地图**"。
    将显示一条带有作业详细信息的确认消息。 有关详细信息，请参阅作业中的作业状态。

    >[!NOTE]
    > 这项工作大约需要三到四个小时才能完成。

### <a name="download-the-soil-moisture-heatmap"></a>下载土壤水分热图

请使用以下步骤：

1. 在 **"作业"** 页中，检查上次过程中创建的作业的**作业状态**。
2. 当作业状态显示 **"成功"** 时，请在菜单上选择 **"地图**"。
3. 在以土壤-moisture_MyFarm_YYYY-MM-DD><创建地图的当天搜索地图。
4. 在 **"名称"** 列中选择地图，将显示一个弹出窗口，并预览所选地图。
5. 选择“下载”****。 地图将下载并存储到计算机的本地文件夹。

    ![FarmBeats 项目](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>后续步骤

现在，您已成功生成土壤水分热图，了解如何[生成传感器放置](generate-maps-in-azure-farmbeats.md#sensor-placement-map)和[引入历史遥测数据](ingest-historical-telemetry-data-in-azure-farmbeats.md)。 
