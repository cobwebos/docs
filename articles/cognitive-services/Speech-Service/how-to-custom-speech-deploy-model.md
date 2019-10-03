---
title: 部署自定义语音语音服务的模型
titleSuffix: Azure Cognitive Services
description: 在本文档中，你将了解如何使用自定义语音门户创建和部署终结点。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: fcd3e5fd6db0d7c18a5f9bfba030cd3d809b452f
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802538"
---
# <a name="deploy-a-custom-model"></a>部署自定义模型

上传和检查数据、评估准确性并训练自定义模型后，可以部署自定义终结点，以便与应用、工具和产品一起使用。 在本文档中，你将了解如何使用[自定义语音门户](https://speech.microsoft.com/customspeech)创建和部署终结点。

## <a name="create-a-custom-endpoint"></a>创建自定义终结点

若要创建新的自定义终结点，请登录到[自定义语音门户](https://speech.microsoft.com/customspeech)，并从页面顶部的 "自定义语音" 菜单中选择 "**部署**"。 如果这是首次运行，你会注意到表中没有列出任何终结点。 创建终结点后，可以使用此页跟踪每个已部署的终结点。

接下来，选择 "**添加终结点**"，然后输入自定义终结点的**名称**和**描述**。 然后选择想要与此终结点关联的自定义模型。 在此页上，您还可以启用日志记录。 日志记录允许你监视终结点流量。 如果禁用，则不会存储流量。

![如何部署模型](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 请不要忘记接受使用条款和定价详细信息。

接下来，选择“创建”。 此操作会将您返回到 "**部署**" 页。 该表现在包含对应于自定义终结点的项。 终结点的状态显示其当前状态。 使用自定义模型来实例化新的终结点最多可能需要30分钟。 部署状态更改为 "**完成**" 时，终结点可供使用。

部署终结点后，其名称将以链接的形式显示。 单击此链接可显示特定于该终结点的信息，例如终结点密钥、终结点 URL 和示例代码。

## <a name="view-logging-data"></a>查看日志记录数据

可在**终结点 > 详细信息**"下下载日志记录数据。

## <a name="next-steps"></a>后续步骤

* 通过[语音 SDK](speech-sdk.md)使用自定义终结点

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
