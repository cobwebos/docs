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
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 20920a04c5b85c9eede7d7b249dd6e0548308240
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883400"
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

```
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>导入模块

创建名为 ContentModeratorQS.py 的新 Python 脚本，并添加以下代码来导入 SDK 的必要部分。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=1-10)]

另外，导入“pretty print”函数来处理最终输出。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=12)]


## <a name="initialize-variables"></a>初始化变量

接下来，为内容审查器订阅密钥和终结点 URL 添加变量。 需将 `<your subscription key>` 替换为密钥的值。 可能还需更改 `endpoint_url` 的值，以便使用与订阅密钥对应的区域标识符。 免费试用订阅密钥在 **westus** 区域中生成。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=14-16)]


为简单起见，将直接从脚本中分析文本。 定义文本内容的一个新字符串进行审查：

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=18-21)]


## <a name="query-the-moderator-service"></a>查询审查器服务

使用订阅密钥和终结点 URL 创建 ContentModeratorClient 实例。 然后，使用其成员 TextModerationOperations 实例调用审查 API。 有关如何调用的详细信息，请参阅 **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** 参考文档。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=23-36)]

## <a name="print-the-response"></a>输出响应

最后，检查调用是否成功完成并返回“屏幕”实例。 然后将返回的数据输出到控制台。

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=38-39)]


本快速入门中使用的示例文本的输出如下：

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
