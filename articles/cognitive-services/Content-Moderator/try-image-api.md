---
title: 使用 Azure 内容审查器审查图像 | Microsoft Docs
description: 试用内容审查器 API 控制台中的图像审查工作流。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: fec54826c70ae10e56c68406f629c56639985295
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365487"
---
# <a name="moderate-images-from-the-api-console"></a>在 API 控制台中审查图像

使用 Azure 内容审查器中的[图像审查 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)，可以启动图像内容的扫描和审阅式审查工作流。 此审查作业会扫描你的内容是否有猥亵内容，并将它与自定义和共享黑名单进行比较。

## <a name="use-the-api-console"></a>使用 API 控制台
必须有订阅密钥，才能在联机控制台中试用 API。 订阅密钥位于“设置”选项卡上的“Ocp-Apim-Subscription-Key”框中。 有关详细信息，请参阅[概述](overview.md)。

1.  转到[“图像审查 API 参考”](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)。

  此时，“图像 - 评估”图像审查页打开。

2. 对于“开放 API 测试控制台”，选择与所在位置最相关的区域。 

  ![试用“图像 - 评估”页上的区域选择](images/test-drive-region.png)
  
  此时，“图像 - 评估”API 控制台打开。

3. 在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥。

  ![试用“图像 - 评估”控制台上的订阅密钥输入](images/try-image-api-1.PNG)

4. 在“请求正文”框中，使用默认示例图像，或指定要扫描的图像。 可以二进制位数据的形式提交图像本身，也可以指定图像的可公开访问 URL。 

  对于本示例，在“请求正文”框中使用所提供的路径，再选择“发送”。 

   ![试用“图像 - 评估”控制台上的“请求正文”](images/try-image-api-2.PNG)

  此 URL 处的图像如下：

  ![试用“图像 - 评估”控制台上的示例图像](images/sample-image.jpg) 

5. 选择“发送”。

6. API 不仅返回每个分类的概率分数， 还返回确定图像是否符合条件的结果（“true”或“false”）。 

  ![试用“图像 - 评估”控制台上的概率分数和条件确定](images/try-image-api-3.PNG)

## <a name="face-detection"></a>人脸检测

图像审查 API 可用于定位图像中的人脸。 若有隐私顾虑，并要防止某人脸出现在平台上，就会发现此选项十分有用。 

1.  在[图像审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)的左侧菜单中，选择“图像”下的“查找人脸”。 

  此时，“图像 - 查找人脸”页打开。

2.  对于“开放 API 测试控制台”，选择与所在位置最相关的区域。 

  ![试用“图像 - 查找人脸”页上的区域选择](images/test-drive-region.png)

  此时，“图像 - 查找人脸”API 控制台打开。

3. 指定要扫描的图像。 可以二进制位数据的形式提交图像本身，也可以指定图像的可公开访问 URL。 本示例链接到 CNN 文章中使用的图像。

  ![试用“图像 - 查找人脸”上的示例图像](images/try-image-api-face-image.jpg)

  ![试用“图像 - 查找人脸”上的示例请求](images/try-image-api-face-request.png)

4. 选择“发送”。 在本示例中，API 找到两张人脸，并返回它们在图像中的坐标。

   ![试用“图像 - 查找人脸”上的示例“响应内容”框](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>通过 OCR 功能检测文本

内容审查器 OCR 功能可用于检测图像中的文本。

1. 在[图像审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)的左侧菜单中，选择“图像”下的“OCR”。 

  此时，“图像 - OCR”页打开。

2. 对于“开放 API 测试控制台”，选择与所在位置最相关的区域。 

  ![“图像 - OCR”页上的区域选择](images/test-drive-region.png)

  此时，“图像 - OCR”API 控制台打开。

3. 在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥。

4. 在“请求正文”框中，使用默认示例图像。 这与上一部分中使用的图像相同。

5. 选择“发送”。 提取的文本用 JSON 显示：

  ![“图像 - OCR”示例“响应内容”框](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>后续步骤

若要与应用集成，请在代码中使用 REST API，或从[图像审查 .NET 快速入门](image-moderation-quickstart-dotnet.md)入手。
