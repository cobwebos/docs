---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70130064"
---
作为最低要求，我们建议在初始训练集中每个标记使用至少 30 张图像。 此外还需要收集一些额外的图像，以便在训练模型后测试模型。

为了有效地训练模型，请使用具有视觉多样性的图像。 选择在以下方面有所不同的图像：
* 照相机角度
* 照明
* background
* 视觉样式
* 个人/分组主题
* size
* type

此外，请确保所有训练图像满足以下条件：
* .jpg、.png、.bmp 或 .gif 格式
* 大小不超过 6 MB （预测图像不超过 4 MB）
* 最短的边不小于 256 像素；任何小于此像素的图像将通过自定义影像服务自动纵向扩展
