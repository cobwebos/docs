---
title: 语言理解 (LUIS) API HTTP 响应代码 - Azure | Microsoft Docs
titleSuffix: Azure
description: 了解从 LUIS 创作和终结点 API 返回的 HTTP 响应代码
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: v-geberr
ms.openlocfilehash: 9c7381d9dc2ecf302c85c6b4f1f24b24a28d9ebe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365835"
---
# <a name="luis-api-http-response-codes"></a>LUIS API HTTP 响应代码
[创作](https://aka.ms/luis-authoring-apis)和[终结点](https://aka.ms/luis-endpoint-apis) API 返回 HTTP 响应代码。 响应消息包含特定于某个请求的信息，而 HTTP 响应状态代码是通用的。 

## <a name="common-status-codes"></a>常见状态代码
下表列出了[创作](https://aka.ms/luis-authoring-apis)和[终结点](https://aka.ms/luis-endpoint-apis) API 最常见的一些 HTTP 响应状态代码：

|代码|API|说明|
|:--|--|--|
|400|创作、终结点|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|创作、终结点|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|创作|使用的是终结点订阅密钥，而不是创作密钥|
|401|创作、终结点|密钥无效、格式错误或为空|
|401|创作、终结点| 密钥与区域不匹配|
|401|创作|你不是所有者或协作者|
|401|创作|API 调用的顺序无效|
|403|创作、终结点|超出了每月密钥配额总限制|
|409|终结点|应用程序仍在加载|
|410|终结点|需要重新训练和重新发布应用程序|
|414|终结点|查询超过最大字符限制|
|429|创作、终结点|超出速率限制（请求数/秒）|