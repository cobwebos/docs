---
title: 快速入门：使用 Python 分析文本内容 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 如何使用适用于 Python 的内容审查器 SDK 分析文本内容中是否存在各种令人反感的材料
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb0e44f83e2101a7b21e7b7ec6fdc75974c6d6d8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333609"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>快速入门：使用 Python 分析文本内容中是否存在令人反感的材料

本文中的信息和代码示例有助于你完成适用于 Python 的内容审查器 SDK 的使用入门。 本文将介绍如何对文本内容执行基于字词的筛选和分类，以便审查其中是否存在可能会令人反感的材料。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件
- 内容审查器的订阅密钥。 按照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅内容审查器并获取密钥。
- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具

## <a name="get-the-content-moderator-sdk"></a>获取内容审查器 SDK

打开命令提示符并运行以下命令以安装内容审查器 Python SDK：

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>导入模块

创建名为 ContentModeratorQS.py 的新 Python 脚本，并添加以下代码来导入 SDK 的必要部分  。 包含了 pretty print 模块，以便更容易地读取 JSON 响应。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>初始化变量

接下来，为内容审查器订阅密钥和终结点 URL 添加变量。 将名称 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 添加到环境变量中，并将订阅密钥作为其值。 对于基本终结点 URL，请将 `CONTENT_MODERATOR_ENDPOINT` 添加到环境变量，并将特定于区域的 URL 作为其值，例如 `https://westus.api.cognitive.microsoft.com`。 免费试用订阅密钥在 **westus** 区域中生成。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

将审查文件中的多行文本字符串。 将 [content_moderator_text_moderation.txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) 文件包含到本地根文件夹中，并将其文件名添加到变量中：

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>查询审查器服务

使用订阅密钥和终结点 URL 创建 ContentModeratorClient 实例  。 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

然后，使用客户端及其成员 **TextModerationOperations** 实例通过函数 `screen_text` 来调用审查 API。 有关如何调用的详细信息，请参阅 **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** 参考文档。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>检查输出的响应

运行示例并确认响应。 成功完成后，将返回 **Screen** 实例。 成功的结果如下所示：

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你开发了一个简单的 Python 脚本，该脚本使用内容审查器服务返回给定文本示例的相关信息。 接下来，请详细了解不同标记和分类的含义，以便确定所需数据以及应用处理该数据的方式。

> [!div class="nextstepaction"]
> [文本审查指南](text-moderation-api.md)
