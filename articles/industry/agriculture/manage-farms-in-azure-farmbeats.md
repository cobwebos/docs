---
title: 管理服务器场
description: 描述如何管理服务器场
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271715"
---
# <a name="manage-farms"></a>管理场

您可以在 Azure FarmBeats 中管理服务器场。 本文提供有关如何创建服务器场、安装设备、传感器和无人机的信息，以帮助您管理服务器场。

## <a name="create-farms"></a>创建服务器场

请使用以下步骤：

1. 登录到服务器场加速器，将显示 **"服务器场"** 页面。
    "**服务器场"** 页显示服务器场的列表，以防它们已在订阅中创建。

    下面是示例图像：

    ![FarmBeats 项目](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. 选择 **"创建服务器场**"并提供**名称**、**裁剪**和**地址**。
3. 在**定义服务器场边界**中，（必填字段）选择 **"标记"** 或 **"粘贴 GeoJSON 代码**"。

以下是定义服务器场边界的两种方法：

1. **在地图上标记**：使用地图控制工具绘制和标记服务器场的边界。 要标记边界，![项目服务器场节拍](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png)并标记确切的边界。

    ![FarmBeats 项目](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **粘贴 GeoJson 代码**： GeoJSON 是一种使用 JavaScript 对象表示法 （JSON） 编码地理数据结构的格式。 此选项显示一个文本框，其中可以输入 GeoJSON 字符串来标记服务器场边界。 您还可以从GeoJSON.io创建 GeoJSON 代码。
使用工具提示帮助填写信息。

    ![FarmBeats 项目](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  选择 **"提交**"以创建服务器场。 将创建新服务器场，并在 **"服务器场"** 页中显示。

## <a name="view-farm"></a>查看服务器场

"服务器场"列表页显示已创建服务器场的列表。 选择服务器场以查看以下列表：

 - **设备计数**= 显示在服务器场中部署的设备的数量和状态。
 - **映射**– 使用服务器场中部署的设备绘制服务器场的地图。
 - **遥测**= 显示服务器场中部署的传感器的遥测数据。
 - **最新精度图**– 显示最新的卫星指数图（EVI、NDWI）、土壤水分热图和传感器放置图。

## <a name="edit-farm"></a>编辑服务器场

"**服务器场"** 页显示已创建服务器场的列表。

1.  选择要查看和编辑服务器场的服务器场。
2.  选择 **"编辑服务器场**"以编辑服务器场信息。 在 **"服务器场详细信息"** 窗口中，可以编辑**名称**、**裁剪、****地址**和定义**服务器场边界**字段。

    ![FarmBeats 项目](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. 选择 **"提交"** 以保存已编辑的详细信息。

## <a name="delete-farm"></a>删除服务器场

"**服务器场"** 页显示创建的服务器场的列表。 使用以下步骤删除服务器场：

1.  从列表中选择服务器场以删除服务器场详细信息。
2.  选择 **"删除服务器场**"以删除服务器场。

    ![FarmBeats 项目](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > 删除服务器场时，不会删除与服务器场关联的设备和映射。 与设备和地图关联的任何服务器场信息都将不相关。 您可以继续从 FarmBeats 服务查看设备、遥测和地图。


## <a name="next-steps"></a>后续步骤

现在，您已经创建了服务器场，请了解如何[使传感器数据](get-sensor-data-from-sensor-partner.md)流入服务器场。
