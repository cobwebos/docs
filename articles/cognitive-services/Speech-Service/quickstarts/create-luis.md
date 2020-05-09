---
title: 快速入门：创建 LUIS 密钥
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何创建 LUIS 应用程序和获取密钥。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: df12b51020083489d431d0ebcd7eb506ef97caa2
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400849"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>快速入门：获取 LUIS 终结点密钥

## <a name="prerequisites"></a>先决条件

在开始阅读本教程之前，请务必准备好以下各项：

* 一个 LUIS 帐户。 可以通过 [LUIS 门户](https://www.luis.ai/home)免费创建一个帐户。

## <a name="luis-and-speech"></a>LUIS 和语音

LUIS 与语音服务集成，可从语音中识别意向。 不需要语音服务订阅，只需要 LUIS。

LUIS 使用三种密钥：

|密钥类型|目的|
|--------|-------|
|创作|用于以编程方式创建和修改 LUIS 应用|
|入门|仅允许使用纯文本测试 LUIS 应用程序|
|端点 |授权访问特定的 LUIS 应用|

对于本教程，需要使用终结点密钥类型。 本教程使用一个示例家庭自动化 LUIS 应用，可以遵循[使用预生成的家庭自动化应用](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app)快速入门来创建该应用。 如果你已创建自己的 LUIS 应用，可以改用该应用。

当你创建 LUIS 应用时，LUIS 会自动生成一个初学者密钥，让你使用文本查询测试该应用。 此密钥不会启用语音服务集成，因此不适用于本教程。 在 Azure 仪表板中创建 LUIS 资源并将其分配给 LUIS 应用。 在本教程中，可以使用免费订阅层。

在 Azure 仪表板中创建 LUIS 资源之后，请登录到 [LUIS 门户](https://www.luis.ai/home)，在“我的应用”页上选择自己的应用程序，然后切换到应用的“管理”页。   最后，在侧栏中选择“密钥和终结点”。 

![LUIS 门户密钥和终结点设置](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

在“密钥和终结点”设置页上： 

1. 向下滚动到“资源和密钥”部分，选择“分配资源”。  
1. 在“将密钥分配到应用”对话框中进行以下更改： 

   * 在“租户”下选择“Microsoft”。  
   * 在“订阅名称”下，选择包含所要使用的 LUIS 资源的 Azure 订阅。 
   * 在“密钥”下，选择要在应用中使用的 LUIS 资源。 

   片刻之后，新订阅将显示在页面底部的表格中。

1. 选择密钥旁边的图标将其复制到剪贴板。 （可以使用其中的任一密钥。）

![LUIS 应用订阅密钥](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [识别意向](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
