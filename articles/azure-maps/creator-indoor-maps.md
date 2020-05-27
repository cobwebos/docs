---
title: 在 Azure Maps Creator 中使用室内定位
description: 本文介绍适用于 Azure Maps Creator 服务的概念。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: fb0293c66662c86723c31baebeed42dd42d33413
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677813"
---
# <a name="creator-for-indoor-maps"></a>用于室内定位的 Creator

本文介绍适用于 Azure Maps Creator 的概念和工具。 建议你在开始使用 Azure Maps Creator API 和 SDK 之前阅读本文。

可以使用 Creator，通过基于室内定位数据的定位功能来开发应用程序。 本文介绍上传、转换、创建和使用定位数据的过程。 下图演示了整个工作流。

![Creator 定位数据工作流](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>创建 Azure Maps Creator

若要使用 Creator 服务，必须在 Azure Maps 帐户中创建 Azure Maps Creator。 有关如何在 Azure Maps 中创建 Azure Maps Creator 的信息，请参阅[管理 Azure Maps Creator](how-to-manage-creator.md)。

## <a name="upload-a-drawing-package"></a>上传绘图包

Creator 通过转换上传的绘图包收集室内定位数据。 此绘图包表示构造或已重构的设施。 有关绘图包要求的信息，请参阅[绘图包要求](drawing-requirements.md)。

使用 [Azure Maps 数据上传 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) 上传绘图包。  成功上传后，数据上传 API 将返回用户数据标识符 (`udid`)。 将在下一步使用 `udid`，以将上传的包转换为室内定位数据。

## <a name="convert-a-drawing-package"></a>转换绘图包

[Azure Maps 转换服务](https://docs.microsoft.com/rest/api/maps/conversion)可以将上传的绘图包转换为室内定位数据。 转换服务还将验证包。 验证问题分为两类：错误和警告。 如果检测到任何错误，转换过程将失败。 如果检测到警告，转换将成功。 尽管如此，仍建议你审阅并解决所有警告。 警告表示转换的一部分被忽略或自动修复。 如果不解决警告，可能会导致后续进程出错。 有关详细信息，请参阅[绘图包警告和错误](drawing-conversion-error-codes.md)。

当发生错误时，转换服务会提供指向 [Azure Maps 绘图错误可视化工具](drawing-error-visualizer.md)独立 Web 应用程序的链接。 可以使用“绘图错误可视化工具”来检查在转换过程中出现的[绘图包警告和错误](drawing-conversion-error-codes.md)。 修复错误后，可以尝试上传和转换包。

## <a name="create-indoor-map-data"></a>创建室内定位数据

Azure Maps Creator 提供三个服务：

* [数据集服务](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)。
使用数据集服务，基于转换后的绘图包数据创建数据集。
* [图块集服务](https://docs.microsoft.com/rest/api/maps/tileset/createpreview)。
使用图块集服务可以创建数据集的基于向量的表示形式。 应用程序可以使用图块集来呈现基于视觉对象图块的数据集视图。
* [功能状态服务](https://docs.microsoft.com/rest/api/maps/featurestate)。使用功能状态服务来支持动态定位样式。 动态定位样式允许应用程序在 IoT 系统提供的空间上反映实时事件。

### <a name="datasets"></a>数据集

数据集是室内定位功能的集合。 室内定位功能表示在转换的绘图包中定义的设施。 使用[数据集服务](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)创建数据集后，可以创建任意数量的[图块集](#tilesets)或[功能状态集](#feature-statesets)。

[数据集服务](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)允许开发人员随时向现有数据集添加或删除设备。 有关如何使用 API 更新现有数据集的详细信息，请参阅[数据集服务](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)中的追加选项。 有关如何更新数据集的示例，请参阅[数据维护](#data-maintenance)。

### <a name="tilesets"></a>图块集

图块集是表示一组统一网格图块的矢量数据的集合。 开发人员可以使用[图块集服务](https://docs.microsoft.com/rest/api/maps/tileset/createpreview)从数据集创建图块集。

若要反映不同的内容阶段，可以从同一数据集创建多个图块集。 例如，你可以创建一个带有家具和设备的图块集，再创建一个不带家具和设备的图块集。  可以选择生成包含最新数据更新的图块集，也可以生成不包含最新数据更新的图块集。

除了矢量数据以外，图块集还提供用于定位呈现优化的元数据。 例如，图块集元数据包含图块集的最小和最大缩放级别。 该元数据还提供了定义图块集地理范围的边界框。 使用边界框，应用程序可以编程方式设置正确的中心点。 有关图块集元数据的详细信息，请参阅[图块集列表 API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview)。

创建图块集后，可以通过 [Render V2 服务](#render-v2-service)检索它。

如果图块集已过时且不再有用，可以删除图块集。 有关如何删除图块集的详细信息，请参阅[数据维护](#data-maintenance)。

>[!NOTE]
>图块集独立于它从中创建的数据集。 如果从数据集创建图块集，然后更新该数据集，则图块集将不会更新。 若要反映数据集中的更改，必须创建新的图块集。 同样，如果删除图块集，不会影响数据集。

### <a name="feature-statesets"></a>功能状态集

功能状态集是分配给数据集功能（如房间或设备）的动态属性（状态）的集合。 状态的一个示例可以是温度或占用量。 每个状态均为一个键/值对，其中包含属性名称、值和上次更新的时间戳。

通过[功能状态服务](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview)，你可以创建和管理数据集的功能状态集。 状态集是由一个或多个状态定义的。 每个功能（如房间）都可以附加一个状态。

状态集中每个状态的值都可以由 IoT 设备或其他应用程序更新或检索。  例如，使用[功能状态更新 API](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview)，测量空间占用量的设备可以系统地发布房间状态的变化。

应用程序可以使用功能状态集，根据其当前状态和相应的定位样式，在设施中动态呈现功能。 若要详细了解如何使用功能状态集对渲染图中的功能进行样式化，请参阅[室内 Web SDK 模块](#indoor-maps-module)。

>[!NOTE]
>与图块集一样，更改数据集并不会影响现有功能状态集，删除功能状态集对它附加到的数据集也没有任何影响。

## <a name="using-indoor-maps"></a>使用室内定位

### <a name="render-v2-service"></a>Render V2 服务

Azure Maps [Render V2 服务 - 获取定位图块 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 已扩展为支持 Creator 图块集。

[Render V2 服务 - 获取定位状态图块 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 允许应用程序请求图块集。 然后，可以将图块集集成到定位控件或 SDK。 有关使用 Render V2 服务的定位控件的示例，请参阅[室内定位模块](#indoor-maps-module)。

### <a name="web-feature-service-api"></a>Web 功能服务 API

可以使用 [Web 功能服务 (WFS) API](https://docs.microsoft.com/rest/api/maps/wfs) 来查询数据集。 WFS 遵循[开放地理空间信息联盟 API 功能](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)。 利用 WFS API，你可以查询数据集自带的功能。 例如，你可以使用 WFS 来查找给定设施和楼层的所有中型会议室。

### <a name="indoor-maps-module"></a>室内定位模块

[Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/) 包括室内定位模块。 此模块为 Azure Maps 定位控件库提供扩展功能。 室内定位模块呈现在 Creator 中创建的室内定位。 它集成小组件（如楼层选取器），从而帮助用户可视化不同的楼层。

室内定位模块使你能够创建将室内定位数据与其他 [Azure Maps 服务](https://docs.microsoft.com/azure/azure-maps/)相集成的 Web 应用程序。 最常见的应用程序设置可能包括从其他定位（如道路、图像、天气和运输）将知识添加到室内定位。

室内定位模块还支持动态定位样式。 有关如何在应用程序中实现功能状态集动态样式的分步演练，请参阅[如何使用室内定位模块](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Azure Maps 集成

开始开发面向室内定位的解决方案时，可以找到集成现有 Azure Maps 功能的方法。 例如，可以通过在 Creator 室内定位中使用 [Azure Maps 地理围栏 API](https://docs.microsoft.com/rest/api/maps/spatial/postgeofence) 来实现资产跟踪或安全方案。 例如，可以使用地理围栏 API，来确定工作人员是进入还是离开了特定的室内区域。 有关如何使用 IoT 遥测连接 Azure Maps 的详细信息，请参阅[此处](tutorial-iot-hub-maps.md)。

### <a name="data-maintenance"></a>数据维护

 通过 Azure Maps Creator 列出、更新和删除 API，你可以列出、更新和删除数据集、图块集和功能状态集。

>[!NOTE]
>每次查看项列表并决定删除它们时，必须考虑该删除对所有从属 API 或应用程序的影响。 例如，如果应通过 [Render V2 - 获取定位图块 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 删除应用程序当前正在使用的图块集，删除该图块集将导致应用程序无法呈现图块集。

### <a name="example-updating-a-dataset"></a>示例：更新数据集

下面的示例演示如何更新数据集、创建新的图块集并删除旧的图块集。

1. 按照[上传绘图包](#upload-a-drawing-package)和[转换绘图包](#convert-a-drawing-package)部分中的步骤，上传并转换新的绘图包。

2. 使用[数据集创建 API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) 将转换后的数据追加到现有数据集。

3. 使用[图块集创建 API](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) 从更新的数据集生成新的图块集。 保存步骤 4 的新图块集 ID。

4. 更新应用程序中的图块集标识符，以启用更新的校园数据集的可视化效果。 如果不再使用旧的图块集，可将其删除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：创建 Creator 室内定位](tutorial-creator-indoor-maps.md)
