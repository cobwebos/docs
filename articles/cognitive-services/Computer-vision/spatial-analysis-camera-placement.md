---
title: 空间分析相机位置
titleSuffix: Azure Cognitive Services
description: 了解如何设置照相机以用于空间分析
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 8e951e6cb18596b19f49bf42179297f656e3fa5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91304282"
---
# <a name="camera-placement-guide"></a>照相机位置指南

本文提供了有关空间分析 (公共预览版) 的相机位置建议。 其中包括一般准则，以及所有包含的操作的高度、角度和点到点距离的具体建议。 

> [!NOTE]
> 本指南适用于 Axis M3045-V 相机。 此照相机将使用分辨率1920x1080、106度水平的视图、59度垂直的视图字段和固定的 2.8 mm 焦点。 以下原则适用于所有相机，但围绕相机高度和相机到点距离的特定准则需要进行调整，以便与其他相机一起使用。 

## <a name="general-guidelines"></a>一般性指导

为空间分析定位照相机时，请考虑以下通用准则：

* **照明高度。** 将照相机放在照明装置下面，以便装置不会阻塞相机。
* **阻碍.** 若要避免阻隔相机视图，请注意两极、告示、搁置、墙壁和现有 LP 相机等障碍。
* **环境背光。** 户外背光影响相机图像质量。 若要避免严重的背光条件，请避免在面向外部的窗口和玻璃门上定向摄像机。
* **本地隐私规则和法规。** 本地法规可能会限制相机可以捕获的内容。 在放置相机之前，请务必了解本地规则和法规。
* **构建结构。** HVAC、灭火器和现有布线可能会限制照相机的硬安装。
* **缆线管理。** 请确保可以将以太网电缆从计划的照相机装入位置路由到通过 Internet (PoE) 开关。

## <a name="height-focal-point-distance-and-angle"></a>高度、点距离和角度

在决定如何安装用于空间分析的照相机时，需要考虑三个事项：
- 相机高度
- 照相机到点距离
- 相机相对于地面平面的角度

如果可能，请务必了解大多数人 (人员遍历方向) 相对于 "照相机" 字段的方向。 此方向对于系统性能非常重要。

![遍历方向的人员图像](./media/spatial-analysis/person-walking-direction.png)

下图显示了人员行走方向的仰角。

![提升和计划视图](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>相机高度

通常，照相机应该从地面装入12-14 英尺。 在此范围内规划你的照相机安装时，请考虑障碍物 (例如：搁置、悬挂光源、悬挂式告示，并显示可能影响相机视图的) ，然后根据需要调整高度。

## <a name="camera-to-focal-point-distance"></a>照相机到点距离

"_相机到点距离_" 是从 " (" 或 "相机" 图像中心到) 到 "地面" 测量的点的线性距离。

![相机到焦点-距离](./media/spatial-analysis/camera-focal-point.png)

此距离在地面平面上测量。

![从地面测量相机到点距离的方式](./media/spatial-analysis/camera-focal-point-floor-plane.png)

如上所述，它如下所示：

![从上面测量相机到点距离的方式](./media/spatial-analysis/camera-focal-point-above.png)

使用下表根据特定的安装高度确定相机与焦点的距离。 这些距离适用于最佳放置。 请注意，该表提供了低于 12 "-14" 建议的指导，因为某些上限可以限制高度。

| 相机高度 | 最小/最大 (的相机到焦距离)  |  
| ------------- | ---------------------------------------- |  
| 8            | 10 "-13"                                  |  
| 万           | 7 '-13 '                                   |  
| 10           | 10 "-17"                                  |  
| 毫米           | 11 "-18"                                  |  
| 超过           | 12 "-22"                                  |  
| 0.2           | 15 "-30"                                  |  

下图模拟相机到焦点距离最近的相机视图。

| 距离                                      | 远离                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![最近的相机到焦距离](./media/spatial-analysis/focal-point-closest.png) | ![最远的相机到焦距离](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>相机角度装入范围

本部分介绍可接受的摄像机角度安装范围。 这些装入范围显示最佳放置的可接受范围。

### <a name="line-configuration"></a>线路配置

下表显示针对 **cognitiveservices account-personcrossingline** 操作配置的照相机的建议。 

| 相机高度 | 照相机到点距离 | 最佳相机装入角度 (最小/最大)  |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| 8            | 900                             | +/-40 °                                     |  
| 万           | 万                            | +/-30 °                                     |  
| 10           | 9                            | +/-20 °                                     |  
| 超过           | 18                            | +/-10 °                                     |  
| 0.2           | 22                            | +/-10 °                                     |  

以下插图使用最左侧的 ( ) 和最右边 (+) 装载角度建议来模拟相机视图，以使用 **cognitiveservices account spatialanalysis-personcrossingline** 执行进入计数。

| 最左侧视图                | 最右边视图                |  
| ---------------------------- | ----------------------------- |  
| ![最左端相机角度](./media/spatial-analysis/camera-angle-left.png) | ![最右边摄像机角度](./media/spatial-analysis/camera-angle-right.png) |  

下图显示了来自鸟瞰视图的相机位置和装入角度。

![鸟瞰视图](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>区域配置

建议你将相机置于10英尺或更高的地面上，以保证涵盖的区域足够大。 

当区域位于墙壁或架子等障碍旁边时，将在可接受的120度角范围内的指定距离内装入相机，如下图所示。

![可接受的摄像机角度](./media/spatial-analysis/camera-angle-acceptable.png)

下图提供了对货位旁边某个区域的左侧和右侧照相机视图的模拟。

| 左视图        | 右视图        |  
| ---------------- | ----------------- |  
| ![左视图](./media/spatial-analysis/end-cap-left.png) | ![右视图](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>队列

**Cognitiveservices account-personcount**、 **cognitiveservices account**和**spatialanalysis** -persondistance-cognitiveservices account-spatialanalysis-personcrossingpolygon 的技能可用于监视队列。 为了最大限度地提高队列数据质量，最大程度地减少了队列中人员的封闭的 retractable，并确保队列位置在一段时间内保持一致。

![Retractable 皮带队列](./media/spatial-analysis/retractable-belt-queue.png)

这种类型的障碍优先于队列构造的不透明屏障，以最大程度地提高系统的见解准确性。

有两种类型的队列：线性和之-字形。

下图显示了针对线性队列的建议：

![线性队列建议](./media/spatial-analysis/camera-angle-linear-queue.png)

下图提供了线性队列的左侧和右侧照相机视图的模拟。 请注意，你可以在队列的另一侧装载相机。

| 左视图                          | 右视图                          |  
| ---------------------------------- | ----------------------------------- |  
| ![线性队列的左角](./media/spatial-analysis/camera-angle-linear-left.png) | ![线性队列的右角](./media/spatial-analysis/camera-angle-linear-right.png) |  

对于之字形队列，最好避免将相机直接放置在队列行方向上，如下图所示。 请注意，图中的四个示例照相机位置均提供了理想视图，在每个方向上都可以接受 +/-15 度。

以下插图模拟了之-字形队列理想位置中放置的相机的视图。

| 查看1        | 视图2        |  
| ------------- | ------------- |  
| ![查看1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![视图2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 视图3 |  查看4 |  
| ---- | ----  |
| ![视图3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![查看4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>随机队列

随机队列行形式有机。 如果队列不在区域定义中超过2-3 人和行形式，则可以接受此队列样式。 如果队列长度通常大于2-3 人，则建议使用 retractable 皮带关卡来帮助指导队列方向，并确保区域定义内的行形式。

## <a name="next-steps"></a>后续步骤

* [部署计算 web 应用程序的人员](spatial-analysis-web-app.md)
* [配置空间分析操作](./spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [区域和线条位置指南](spatial-analysis-zone-line-placement.md)
