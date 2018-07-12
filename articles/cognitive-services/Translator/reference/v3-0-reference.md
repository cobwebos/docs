---
title: Microsoft 文本翻译 API V3.0 参考 | Microsoft Docs
description: Microsoft 文本翻译 API V3.0 的参考文档。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: cfaa9584e833b137b417d9074fbfcf606eb21388
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365834"
---
#<a name="translator-text-api-v30"></a>文本翻译 API v3.0

## <a name="whats-new"></a>新增功能

Microsoft 文本翻译 API 版本 3 提供了基于 JSON 的新型 Web API。 它通过将现有功能合并到更少的操作中来提高可用性和性能，并提供新功能。

 * 音译可将一种语言的文本从一个脚本转换为另一个脚本。
 * 在一个请求中翻译成多种语言。
 * 在一个请求中进行语言检测、翻译和音译。
 * 字典可查找术语的替代翻译，可查找反向翻译以及显示上下文中使用的术语的示例。
 * 更多信息的语言检测结果。

## <a name="base-urls"></a>基 URL

文本 API v3.0 在以下云中可用：

| 说明 | 区域 | 基 URL                                        |
|-------------|--------|-------------------------------------------------|
| Azure       | 全局 | api.cognitive.microsofttranslator.com           |


## <a name="authentication"></a>身份验证

在 Microsoft 认知服务中订阅文本翻译 API 并使用订阅密钥（在 Azure 门户中提供）进行身份验证。 

最简单的方法是使用请求标头 `Ocp-Apim-Subscription-Key` 将 Azure 密钥传递给 Translator 服务。

另一种方法是使用密钥从令牌服务获取授权令牌。 然后，使用 `Authorization` 请求标头将授权令牌传递给 Translator 服务。 若要获取授权令牌，请向以下 URL 发出 `POST` 请求：

| 环境     | 身份验证服务 URL                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

以下是根据给定密钥获取令牌的示例请求：

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'
// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

成功的请求会将编码的访问令牌作为纯文本在响应正文中返回。 有效的令牌在授权中作为持有者令牌传递给 Translator 服务。

```
Authorization: Bearer <Base64-access_token>
```

身份验证令牌的有效期为 10 分钟。 在对 Translator API 进行多次调用时，应重新使用该令牌。 但是，如果程序在很长一段时间内向 Translator API 发出请求，则程序必须定期（例如每 8 分钟）请求一个新的访问令牌。

总而言之，客户端向 Translator API 发出的请求将包括从下表中获取的一个授权标头：

<table width="100%">
  <th width="30%">标头</th>
  <th>说明</th>
  <tr>
    <td>Ocp-Apim-Subscription-Key</td>
    <td>*如果要传递密钥，请与认知服务订阅一起使用*。<br/>该值是订阅文本翻译 API 的 Azure 密钥。</td>
  </tr>
  <tr>
    <td>授权</td>
    <td>*如果要身份验证令牌，请与认知服务订阅一起使用*。<br/>该值是持有者令牌：“持有者 <token>”。</td>
  </tr>
</table> 

## <a name="errors"></a>Errors

标准错误响应是具有名为 `error` 的名称/值对的 JSON 对象。 该值也是具有以下属性的 JSON 对象：

  * `code`：服务器定义的错误代码。

  * `message`：一个字符串，提供人类可读的错误表示形式。

例如，拥有免费试用订阅的客户会在免费配额用尽后收到以下错误：

```
{
  "error": {
    "code":403000,
    "message":"The subscription has exceeded its free quota."
    }
}
```
