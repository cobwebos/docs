---
title: "在逻辑应用中添加 Microsoft Translator | Microsoft Docs"
description: "使用 REST API 参数的 Microsoft Translator 连接器概述"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: add307420b9e16a60b0e57b0ba08188b2509978e


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Microsoft Translator 连接器入门
连接到 Microsoft Translator，翻译文本、检测语言等。 通过 Microsoft Translator，你可以： 

* 根据从 Microsoft Translator 中获取的数据生成你的业务流。 
* 使用翻译文本、检测语言等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，在 Dropbox 中新建文件时，可使用 Microsoft Translator 将文件中的文本翻译为其他语言。

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Microsoft Translator 包括以下操作。 没有任何触发器。

| 触发器 | 操作 |
| --- | --- |
| 无 |<ul><li>检测语言</li><li>文本到语音转换</li><li>翻译文本</li><li>获取语言</li><li>获取语音语言</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。

## <a name="create-a-connection-to-microsoft-translator"></a>创建到 Microsoft Translator 的连接
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API 参考
适用于版本：1.0。

### <a name="detect-language"></a>检测语言
检测给定文本的源语言。  
```GET: /Detect```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |将标识其语言的文本 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="text-to-speech"></a>文本到语音转换
将给定文本转换为语音，以作为声波格式的音频流。  
```GET: /Speak```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要转换的文本 |
| 语言 |字符串 |是 |查询 |无 |用于生成语音的语言代码（示例：“en-us”） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="translate-text"></a>翻译文本
使用 Microsoft Translator 将文本翻译为指定语言。  
```GET: /Translate```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要翻译的文本 |
| languageTo |字符串 |是 |查询 |无 |目标语言代码（示例：“fr”） |
| languageFrom |字符串 |否 |查询 |无 |源语言；如果未提供，Microsoft Translator 将尝试自动检测。 （示例：en） |
| category |字符串 |否 |查询 |常规 |翻译类别（默认值：“general”） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-languages"></a>获取语言
检索 Microsoft Translator 支持的所有语言。  
```GET: /TranslatableLanguages```

此调用没有任何参数。 

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="get-speech-languages"></a>获取语音语言
检索可用于语音合成的语言。  
```GET: /SpeakLanguages``` 

此调用没有任何参数。

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>语言：Microsoft Translator 可翻译语言的语言模型
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 代码 |字符串 |否 |
| Name |字符串 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

返回到 [API 列表](apis-list.md)。

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png



<!--HONumber=Jan17_HO3-->


