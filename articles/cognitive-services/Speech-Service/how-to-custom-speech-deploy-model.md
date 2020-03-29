---
title: 部署自定义语音模型 - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文档介绍如何使用自定义语音门户创建并部署终结点。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806107"
---
# <a name="deploy-a-custom-model"></a>部署自定义模型

上传并检查数据、评估准确度以及训练自定义模型以后，即可部署可以与应用、工具和产品配合使用的自定义终结点。 本文档介绍如何使用[自定义语音门户](https://speech.microsoft.com/customspeech)创建并部署终结点。

## <a name="create-a-custom-endpoint"></a>创建自定义终结点

若要新建自定义终结点，请登录到[自定义语音门户](https://speech.microsoft.com/customspeech)，选择页面顶部“自定义语音”菜单中的“部署”。**** 如果是第一次运行，你会注意到表中未列出任何终结点。 创建一个终结点后，即可使用此页面跟踪每个部署的终结点。

接下来，选择“添加终结点”，并输入自定义终结点的**名称**和**说明**。**** 然后选择要与此终结点关联的自定义模型。 也可以通过此页启用日志记录。 可以通过日志记录监视终结点流量。 在禁用的情况下，流量不存储。

![如何部署模型](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> 请勿忘记接受有关使用和定价详细信息的条款。

接下来，选择“创建”。**** 执行此操作后会返回到“部署”**** 页。 表中现在有自定义终结点的对应条目。 终结点的状态显示其当前状态。 使用自定义模型实例化新终结点最长可能需要 30 分钟才能完成。 当部署状态更改为“完成”**** 时，终结点便可供使用。

部署终结点后，其名称将以链接的形式显示。 单击此链接可显示特定于该终结点的信息，例如终结点密钥、终结点 URL 和示例代码。

## <a name="view-logging-data"></a>查看日志记录数据

可以在“终结点”>“详细信息”下下载日志记录数据。****

## <a name="next-steps"></a>后续步骤

* 配合使用自定义终结点和[语音 SDK](speech-sdk.md)

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
