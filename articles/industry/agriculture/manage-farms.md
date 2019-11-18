---
title: 管理场
description: 描述如何管理服务器场
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 929672dceabc7d9b591f05ef3f4f716f2117e09c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128674"
---
# <a name="manage-farms"></a>管理场

你可以在 Azure FarmBeats 中管理你的场。 本文提供了有关如何创建场、安装设备、传感器和无人机的信息，这些信息可帮助你管理服务器场。

## <a name="create-farms"></a>创建场

请执行以下步骤：

1. 登录到场加速器，会显示 "**场**" 页面。
    如果已在订阅中创建了场，则 "**服务器场**" 页将显示该列表。

    下面是示例图像：

    ![项目场节拍](./media/create-farms/create-farm-main-page-1.png)


2. 选择 "**创建场**" 并提供**名称**、**裁剪**和**地址**。
3. 在 "**定义场边界**" （必填字段）中，选择 "**标记映射**" 或 "**粘贴 GeoJSON 代码**"。

下面是定义场边界的两种方法：

1. **标记地图**：使用地图控制工具绘制和标记场的边界。 若要标记边界，![项目场节拍](./media/create-farms/pencil-icon-1.png) 并标记确切的边界。

    ![项目场节拍](./media/create-farms/create-farm-mark-on-map-1.png)

2. **粘贴 GeoJson 代码**： GeoJson 是一种用于对地理数据结构进行编码的格式，使用 JAVASCRIPT 对象表示法（JSON）。 此选项显示一个文本框，可以在其中输入 GeoJSON 字符串来标记场边界。 还可以从 GeoJSON.io 创建 GeoJSON 代码。
使用工具提示来帮助填写信息。

    ![项目场节拍](./media/create-farms/create-new-farm-1.png)

4.  选择 "**提交**" 以创建场。 新场随即创建并显示在 "**服务器场**" 页中。

## <a name="view-farm"></a>查看场

"场列表" 页将显示已创建的场的列表。 选择要查看其列表的场：

 - **设备计数**-显示在场中部署的设备的数量和状态。
 - **Map** -在场与在场中部署的设备的地图。
 - **遥测**-显示在场中部署的传感器的遥测数据。
 - **最新的精度地图**-显示最新的卫星索引地图（EVI、NDWI）、土潮湿热度地图和传感器放置图。

## <a name="edit-farm"></a>编辑场

"**服务器场**" 页显示已创建的场的列表。

1.  选择场以查看和编辑场。
2.  选择 "**编辑场**" 以编辑场信息。 在 "**服务器场详细信息**" 窗口中，您可以编辑**名称**、**裁剪**、**地址**和定义**场边界**字段。

    ![项目场节拍](./media/create-farms/edit-farm-1.png)

3. 选择 "**提交**" 以保存编辑的详细信息。

## <a name="delete-farm"></a>删除场

"**服务器场**" 页显示已创建的服务器场列表。 使用以下步骤删除场：

1.  从列表中选择要删除场详细信息的场。
2.  选择 "**删除场**" 以删除场。

    ![项目场节拍](./media/create-farms/delete-farm-1.png)

    > [!NOTE]
    > 删除场时，不会删除与该场关联的设备和映射。 与设备和地图关联的任何场信息都不相关。 你可以继续从 FarmBeats 服务查看设备、遥测和映射。


## <a name="next-steps"></a>后续步骤

现在，你已创建了场，接下来了解如何将[传感器数据](get-sensor-data-from-sensor-partner.md)流入你的场。
