---
title: 使用 Azure 上的语音服务创建自定义语音终结点 |Microsoft Docs
description: 了解如何创建自定义语音到文本终结点使用 Azure 的语音服务。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539020"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>创建自定义语音转文本终结点

创建自定义声学模型或语言模型后，可以将它们部署到自定义语音转文本终结点。

## <a name="create-an-endpoint"></a>创建终结点
若要新建自定义终结点，请选择页面顶部“自定义语音”菜单上的“终结点”。 执行此操作后会重定向到“终结点”页，其中包含当前自定义终结点的表。 如果尚未创建任何终结点，此表为空。 表标题反映了当前的区域设置。

若要为其他语言创建部署，请选择“更改区域设置”。 有关支持的语言的详细信息。

若要新建终结点，请选择“新建”。 在“创建终结点”窗格内的“名称”和“说明”框中，输入自定义部署的信息。

在“订阅”组合框中，选择要使用的订阅。 如果它是 S0（付费）订阅，则可以选择并发请求。

还可以选择是否启用内容日志记录。 也就是说，选择是否存储终结点流量。 如果未启用，就会取消存储流量。 有关所有记录的内容，可以在“终结点”->“详细信息”视图下方找到下载链接

在“声学模型”和“语言模型”列表中，分别选择所需的声学模型和语言模型。 声学模型和语言模型选项始终包含基本 Microsoft 模型。 基本模型的选择会限制模型组合。 无法组合对话基本模型与搜索和听写基本模型。

> [!NOTE]
> 请务必选中复选框，从而接受使用条款和定价信息。
>

选择声学模型和语言模型后，选择“创建”。 执行此操作后会返回到“部署”页。 表中现在有新终结点的对应条目。 终结点状态反映了它在创建期间的当前状态。 使用自定义模型实例化新终结点最长可能需要 30 分钟才能完成。 当部署状态为“完成”时，终结点便可供使用。

部署就绪后，终结点名称就会变成链接。 选择链接可以转到“终结点信息”页，其中显示了自定义终结点 URL，以用于 HTTP 请求或 Microsoft 认知服务语音客户端库（使用 Web 套接字）。

## <a name="next-steps"></a>后续步骤

有关更多教程，请参阅：
- [获取语音服务试用订阅](https://azure.microsoft.com/try/cognitive-services/)
- [创建自定义声学模型](how-to-customize-acoustic-models.md)
- [创建自定义语言模型](how-to-customize-language-model.md)
