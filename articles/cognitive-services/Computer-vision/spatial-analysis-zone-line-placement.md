---
title: 空间分析区域和线路位置
titleSuffix: Azure Cognitive Services
description: 了解如何设置区域和具有空间分析的行
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934440"
---
# <a name="zone-and-line-placement-guide"></a>区域和线条位置指南

本文介绍了如何为空间分析操作定义区域和线路，以便在空间中准确分析人们的变动。 这适用于所有操作。 

区域和行使用 JSON SPACEANALYSIS_CONFIG 参数进行定义。 有关详细信息，请参阅 [空间分析操作](spatial-analysis-operations.md) 一文。

## <a name="guidelines-for-drawing-zones"></a>区域绘图指南

请记住，每个空间都不同;需要更新位置或大小，具体取决于你的需求。

如果要查看照相机视图的特定部分，请创建最大的区域，涵盖感兴趣的特定楼层区，但不包括你不感兴趣的其他区域。 这会增加收集的数据的准确性，并防止不想跟踪的区域出现误报。放置多边形的角时要小心，并确保它们不在要跟踪的区域之外。  

### <a name="example-of-a-well-shaped-zone"></a>合理区域的示例

区域应该足够大，以适应每个边缘的三个人，并专注于感兴趣的领域。 空间分析将识别其垫脚置于区域中的人员，因此，在二维图像上绘制区域时，请将该区域想象成地毯上的地毯。

![合理的区域](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>不是合理的区域的示例

下面的示例显示了不良的区域。 在这些示例中，感兴趣的区域是指在 *游戏时间* 显示前的空间。

**区域不在地面上。**

![形状不良的区域](./media/spatial-analysis/zone-not-on-floor.png) 

**区域太小。**

![区域太小](./media/spatial-analysis/zone-too-small.png)

**区域不会完全捕获显示范围。**

![区域未完全捕获围绕端帽的区域](./media/spatial-analysis/zone-bad-capture.png)

**区域太接近相机图像的边缘，并且不会捕获正确的显示。**

![区域太接近相机图像的边缘，无法捕获正确的显示](./media/spatial-analysis/zone-edge.png)

**区域部分被货位阻止，因此人员和地面并不完全可见。**

![区域部分被阻止，因此人员不完全可见](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>合理的行示例

该行的长度应足以容纳整个进入。 空间分析将识别其英尺跨越线条的人员，因此，当在二维图像上绘制线条时，假设你要将它们绘制为位于地面上的线条。 

如果可能，请扩展行的宽度，而不是实际的进入。 如果此方法不会导致额外的交叉 (如下面的图像中所示，当线条位于墙上时) ，然后扩展它。

![合理的线条](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>不是合理的线条的示例

下面的示例显示了定义不佳的行。

**行不会涵盖地面上的整个进入方式。**

![线条不覆盖地面上的整个进入方式](./media/spatial-analysis/zone-line-bad-coverage.png)

**行太大，并且没有全部覆盖。**

![线路太高，并且没有全部覆盖门](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>后续步骤

* [部署计算 web 应用程序的人员](spatial-analysis-web-app.md)
* [配置空间分析操作](./spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [照相机位置指南](spatial-analysis-camera-placement.md)
