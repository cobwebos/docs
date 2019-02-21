---
title: 快速入门：使用 Azure REST API 和 Python 检测图像中的人脸
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，请使用 Azure 人脸 REST API 和 Python 检测图像中的人脸。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: pafarley
ms.openlocfilehash: db7c3da7d9fff0aa604a73e541be19afc0033a57
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309024"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>快速入门：使用人脸 REST API 和 Python 检测图像中的人脸

在本快速入门中，请使用 Azure 人脸 REST API 和 Python 检测图像中的人脸。 此脚本会在图像的人脸、附加性别和年龄信息周围绘制相应的框。

![一位男士和一位女士，在图像中，每一位的面部都绘制了矩形并显示了年龄和性别](../images/labelled-faces-python.png)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 


## <a name="prerequisites"></a>先决条件

- 人脸 API 订阅密钥。 可以从[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=face-api)获取免费试用的订阅密钥。 或者，按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅人脸 API 服务并获取密钥。

## <a name="run-the-jupyter-notebook"></a>运行 Jupyter Notebook

可在 [MyBinder](https://mybinder.org) 上以 Jupyter 笔记本的方式运行本快速入门。 若要启动活页夹，请选择下面的按钮。 然后根据 Notebook 中的说明进行操作。

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="next-steps"></a>后续步骤

接下来，请浏览人脸 API 参考文档，详细了解支持的方案。

> [!div class="nextstepaction"]
> [人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
