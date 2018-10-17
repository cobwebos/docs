---
title: 计算机视觉快速入门摘要
titleSuffix: Azure Cognitive Services
description: 在这些快速入门中，你使用计算机视觉 API 来分析图像、创建缩略图并提取印刷体和手写文本。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 89502fe49f5eb4b9692d7ee4233c7c46d645b3fb
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576878"
---
# <a name="quickstart-summary"></a>快速入门：摘要

这些快速入门提供的信息和代码示例有助于你使用计算机视觉服务完成快速入门。

这些示例对计算机视觉 API 进行直接的 HTTP 调用。 请参阅使用计算机视觉客户端库的示例的“SDK 快速入门”部分，这些库提供的便捷方法用于包装 HTTP 调用。

若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

可以使用计算机视觉服务来完成以下任务：

* 分析远程图像
* 分析本地图像
* 检测名人和特征点（域模型）
* 智能生成缩略图
* 从图像中检测并提取印刷体文本 (OCR)
* 检测图像并从中提取手写文本

每个示例中的代码类似。 但是，它们突出显示的计算机视觉功能不同，用于交换数据和服务的技术也不同，例如：

* “生成缩略图”会在响应正文中将图像以字节数组的形式返回。
* “分析本地图像”要求将图像以字节数组的形式包括在请求中。
* “提取手写文本”要求通过两个调用来检索文本。

## <a name="summary"></a>摘要

| 快速入门               | 请求参数                          | 响应          |
| ------------------------ | ------------------------------------------- | ----------------  |
| 分析远程图像   | visualFeatures=Categories,Description,Color | JSON 字符串       |
| 分析本地图像    | data=image_data (byte array)                | JSON 字符串       |
| 检测名人       | model=celebrities                           | JSON 字符串       |
| 生成缩略图     | width=200&height=150&smartCropping=true     | Byte Array        |
| 提取印刷体文本     | language=unk&detectOrientation=true         | JSON 字符串       |
| 提取手写的文本 | handwriting=true                            | URL、JSON 字符串  |

## <a name="next-steps"></a>后续步骤

探索用于分析图像、检测名人和地标，创建缩略图以及提取印刷体文本和手写文本的计算机视觉 API。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
