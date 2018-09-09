---
title: 计算机视觉 API cURL 快速入门 - 获取缩略图 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 本快速入门将在认知服务中使用计算机视觉和 cURL 从图像生成缩略图。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4056f05f3f4bf97761f683b5f3a9053666d4ea26
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43768824"
---
# <a name="quickstart-generate-a-thumbnail---rest-curl"></a>快速入门：生成缩略图 - REST、cURL

本快速入门使用计算机视觉从图像生成缩略图。

## <a name="prerequisites"></a>先决条件

若要使用计算机视觉，需要订阅密钥；请参阅[获取订阅密钥](../Vision-API-How-to-Topics/HowToSubscribe.md)。

## <a name="get-thumbnail-request"></a>Get Thumbnail 请求

使用 [Get Thumbnail 方法](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)，可以生成图像的缩略图。 可以指定高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并基于该区域生成裁剪坐标。

若要运行此示例，请执行以下步骤：

1. 将以下代码复制到编辑器中。
1. 将 `<Subscription Key>` 替换为有效订阅密钥。
1. 将 `<File>` 替换为保存缩略图所需的路径和文件名。
1. 如有必要，请更改请求 URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) 以使用获得订阅密钥的位置。
1. （可选）更改要分析的图像 (`{\"url\":\"...`)。
1. 在安装了 cURL 的计算机上打开命令窗口。
1. 将代码粘贴到窗口中并运行命令。

>[!NOTE]
>在 REST 调用中，必须使用用于获取订阅密钥的位置。 例如，如果从 westus 获取了订阅密钥，请将下方 URL 中的“westcentralus”替换为“westus”。

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -o <File> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
```

## <a name="get-thumbnail-response"></a>Get Thumbnail 响应

成功的响应会将缩略图写入 `<File>`。 如果请求失败，则响应包含错误代码和消息，用于确定出错的地方。

## <a name="next-steps"></a>后续步骤

探索用于分析图像、检测名人和特征点，创建缩略图以及提取打印文本和手写文本的计算机视觉 API。 若要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
