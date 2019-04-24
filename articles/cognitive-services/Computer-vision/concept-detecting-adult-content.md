---
title: 描述成人和不雅内容 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 使用计算机视觉 API 检测图像中的成人和不雅内容的相关概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368331"
---
# <a name="detect-adult-and-racy-content"></a>检测成人和不雅内容

计算机视觉可以检测图像中的成人素材，以便开发人员可以限制此类图像显示在他们的软件中。 应用内容标记并使用评分（介于 0 和 1 之间），以便开发人员可以根据自己的偏好来解释结果。 

> [!NOTE]
> [Azure 内容审查器](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview)服务也提供此功能。 有关更严格的内容审核方案（例如，文本审核和人工审核工作流）的解决方案，请参阅此替代方案。

## <a name="content-flag-definitions"></a>内容标记定义

“成人”图像的定义为实质上淫秽并且通常描绘裸体和性行为的图像。 

“不雅”图像的定义为实际具有性暗示且往往包含露骨的描绘性内容（比“成人”图像少）的图像。 

## <a name="identify-adult-and-racy-content"></a>标识成人和不雅内容

[分析](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API。

分析图像方法在方法的 JSON 响应中返回两个布尔属性（`isAdultContent` 和 `isRacyContent`），分别表示成人和不雅内容。 该方法还返回 `adultScore` 和 `racyScore` 这两个属性，它们分别表示用于识别成人和不雅内容的置信度分数。

## <a name="next-steps"></a>后续步骤

了解关于[检测特定于域的内容](concept-detecting-domain-content.md)和[检测人脸](concept-detecting-faces.md)的概念。
