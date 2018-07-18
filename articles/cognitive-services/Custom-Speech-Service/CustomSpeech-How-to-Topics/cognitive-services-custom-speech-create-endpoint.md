---
title: 使用 Azure 上的自定义语音服务创建自定义语音终结点 | Microsoft Docs
description: 了解如何使用认知服务中的自定义语音服务创建自定义语音转文本终结点。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: 99bc275db1f0c1b45b3db440d2e03d0db9ab5cf6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365478"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>创建自定义语音转文本终结点
创建自定义声学模型或语言模型后，可以将它们部署到自定义语音转文本终结点。 

## <a name="create-an-endpoint"></a>创建终结点
若要新建自定义终结点，请选择页面顶部“自定义语音”菜单上的“部署”。 执行此操作后会重定向到“部署”页，其中包含当前自定义终结点的表。 如果尚未创建任何终结点，此表为空。 表标题反映了当前的区域设置。 

若要为其他语言创建部署，请选择“更改区域设置”。 若要详细了解支持的语言，请参阅[自定义语音服务中支持的区域设置](cognitive-services-custom-speech-change-locale.md)。

若要新建终结点，请选择“新建”。 在“创建部署”窗格内的“名称”和“说明”框中，输入自定义部署的信息。

在“订阅”组合框中，选择要使用的订阅。 如果是 S2 订阅，可以选择缩放单元和内容日志记录。 若要详细了解缩放单元和日志记录，请参阅[自定义语音服务计量和配额](../cognitive-services-custom-speech-meters.md)。

下表列出了缩放单元与可用并发请求的对应关系：

| 缩放单元 | 并发请求数 |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

还可以选择是否启用内容日志记录。 也就是说，选择是否存储终结点流量以供 Microsoft 内部使用。 如果未启用，就会取消存储流量。 取消内容日志记录会产生额外成本。 有关详细信息，请参阅[定价信息页](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)。

> [!NOTE]
> 在定价页上，内容日志记录称为“无跟踪”。
>


此外，Microsoft 还粗略估计了成本，以便用户了解对缩放单元和内容日志记录的成本造成的影响。 此为粗略估计，可能与实际成本有差异。

> [!NOTE]
> F0（免费层）订阅不支持这些设置。
>

在“声学模型”和“语言模型”列表中，分别选择所需的声学模型和语言模型。 声学模型和语言模型选项始终包含基本 Microsoft 模型。 基本模型的选择会限制模型组合。 无法组合对话基本模型与搜索和听写基本模型。

![“创建部署”页](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> 请务必选中复选框，从而接受使用条款和定价信息。
>

选择声学模型和语言模型后，选择“创建”。 执行此操作后会返回到“部署”页。 表中现在有新终结点的对应条目。 终结点状态反映了它在创建期间的当前状态。 使用自定义模型实例化新终结点最长可能需要 30 分钟才能完成。 当部署状态为“完成”时，终结点便可供使用。

![“部署”页](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

部署就绪后，部署名称就会变成链接。 选择链接可以转到“部署信息”页，其中显示了自定义终结点 URL，以用于 HTTP 请求或 Microsoft 认知服务语音客户端库（使用 Web 套接字）。

![“部署信息”页](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>后续步骤

有关更多教程，请参阅：
* [使用自定义语音转文本终结点](cognitive-services-custom-speech-use-endpoint.md)
* [创建自定义声学模型](cognitive-services-custom-speech-create-acoustic-model.md)
* [创建自定义语言模型](cognitive-services-custom-speech-create-language-model.md)
