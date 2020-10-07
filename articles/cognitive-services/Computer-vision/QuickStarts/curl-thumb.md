---
title: 快速入门：生成缩略图 - REST、cURL
titleSuffix: Azure Cognitive Services
description: 在该快速入门中，你将使用计算机视觉 API 和 cURL 基于图像生成缩略图。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 1c2ab3f0ef84007d8b9233c45d39fcfbacb5eaab
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87835300"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>快速入门：使用计算机视觉 REST API 和 cURL 生成缩略图

本快速入门将使用计算机视觉 REST API 基于图像生成缩略图。 可以指定所需的高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并围绕该区域生成裁剪坐标。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/) 
* [cURL](https://curl.haxx.se/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="create-and-run-the-sample-command"></a>创建并运行示例命令

要创建和运行示例，请执行以下步骤：

1. 将以下命令复制到文本编辑器中。
1. 必要时在命令中进行如下更改：
    1. 将 `<subscriptionKey>` 的值替换为你的订阅密钥。
    1. 将 `<thumbnailFile>` 的值替换为要保存缩略图的文件的路径和名称。
    1. 将请求 URL 的第一部分 (`westcentralus`) 替换为你自己的终结点 URL 中的文本。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. （可选）将请求正文 (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) 中的图像 URL 更改为要从中生成缩略图的其他图像的 URL。
1. 打开命令提示符窗口。
1. 将文本编辑器中的命令粘贴到命令提示符窗口。
1. 按 Enter 运行此程序。

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>检查响应

成功的响应会将缩略图写入 `<thumbnailFile>` 中指定的文件。 如果请求失败，则响应包含错误代码和消息，以帮助确定出错的地方。 如果请求似乎是成功的，但创建的缩略图不是有效的图像文件，则可能是因为订阅密钥无效。

## <a name="next-steps"></a>后续步骤

探索计算机视觉 API，了解如何分析图像、检测名人和地标、创建缩略图，并提取印刷体文本和手写文本。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
