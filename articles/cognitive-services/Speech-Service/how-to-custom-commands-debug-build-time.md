---
title: 创作自定义命令应用程序时调试错误（预览）
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在创作自定义命令应用程序时调试错误。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6624c8072c60793771d4f4b9943e15f1b276cd34
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85604686"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>创作自定义命令应用程序时调试错误

本文介绍了在构建自定义命令应用程序时出现错误时如何进行调试。 

## <a name="errors-when-creating-an-application"></a>创建应用程序时出错
自定义命令也会在创建自定义命令应用程序时在[LUIS](https://www.luis.ai/)中创建应用程序。 

[LUIS 限制每个创作资源的500个应用程序](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits)。 如果使用的是已包含500应用程序的创作资源，则创建 LUIS 应用程序可能会失败。 

请确保所选的 LUIS 创作资源包含少于500个应用程序。 否则，可以创建新的 LUIS 创作资源，切换到另一个资源，或尝试清理 LUIS 应用程序。  

## <a name="errors-when-deleting-an-application"></a>删除应用程序时出错
### <a name="cant-delete-luis-application"></a>无法删除 LUIS 应用程序
删除自定义命令应用程序时，自定义命令也可能会尝试删除与自定义命令应用程序关联的 LUIS 应用程序。

如果删除 LUIS 应用程序失败，请切换到你的[LUIS](https://www.luis.ai/)帐户以手动删除它们。

### <a name="toomanyrequests"></a>TooManyRequests
尝试同时删除大量应用程序时，可能会看到 "TooManyRequests" 错误。 这意味着 Azure 会限制你的删除请求。 

请刷新页面，尝试删除更少的应用程序。

## <a name="errors-when-modifying-an-application"></a>修改应用程序时出错

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>无法删除参数或 Web 终结点
不允许在使用参数时将其删除。 请删除任何语音响应中的任何参数引用、示例句子、条件和操作，然后重试。

### <a name="cant-delete-a-web-endpoint"></a>无法删除 Web 终结点
不允许删除正在使用的 Web 终结点。 请在删除 Web 终结点之前删除使用此 Web 终结点的任何**调用 Web 终结点**操作。

## <a name="errors-when-training-an-application"></a>培训应用程序时出现的错误
### <a name="built-in-intents"></a>内置意向
LUIS 内置了 "是/否"。 只有 "是"、"否" 的示例句子将无法进行训练。 

| 关键字 | 变体 | 
| ------- | --------- | 
| 是 | 确定，确定 |
| 否 | 上场、Not | 

### <a name="common-sample-sentences"></a>常见示例句子
自定义命令不允许在不同命令之间共享常见的示例句子。 如果一个命令中的一些示例句子已在另一个命令中定义，则应用程序的定型可能会失败。 

请确保没有在不同命令间共享的常见示例句子。 

若要获得跨不同命令均衡示例句子的最佳实践，请参阅[LUIS 最佳实践](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)。

### <a name="empty-sample-sentences"></a>空示例句子
每个命令都必须至少有1个示例句子。

### <a name="undefined-parameter-in-sample-sentences"></a>示例句子中未定义的参数
在示例句子中使用了一个或多个参数，但未定义。

### <a name="training-takes-too-long"></a>训练时间太长
LUIS 培训旨在快速了解更少的示例。 不要添加太多示例句子。 

如果有多个类似的示例句子，请定义一个参数，将其抽象为一个模式，然后将其添加到示例句子。

例如，可以为下面的示例句子定义参数 {车辆}，并且仅将 "Book a {车辆}" 添加到示例句子。

| 示例句子 | 模式 | 
| ------- | ------- | 
| 预订汽车 | 预订 {车辆} | 
| 预订航班 | 预订 {车辆} |
| 预订出租车 | 预订 {车辆} |

若要获得 LUIS 定型的最佳实践，请参阅[LUIS 最佳实践](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)。

## <a name="cant-update-luis-key"></a>无法更新 LUIS 密钥
### <a name="reassign-to-e0-authoring-resource"></a>重新分配给 E0 创作资源
LUIS 不支持将 LUIS 应用程序重新分配给 E0 创作资源。

如果需要将创作资源从 F0 更改为 E0，或更改为其他 E0 资源，请重新创建应用程序。

### <a name="save-button-is-disabled"></a>已禁用 "保存" 按钮
如果永远不向应用程序分配 LUIS 预测资源，则当你尝试更改创作资源而不添加预测资源时，将禁用 "保存" 按钮。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [请参阅 GitHub 上的示例](https://aka.ms/speech/cc-samples)
