---
title: API HTTP 响应代码 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 了解从 QnA Maker API 返回的 HTTP 响应代码。 这将帮助解决任何错误。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9eb5f1aefc8a36727e837af79f553108f624fc70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207958"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API HTTP 响应代码
[管理](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)和预测 API 返回 HTTP 响应代码。 响应消息包含特定于某个请求的信息，而 HTTP 响应状态代码是通用的。 

### <a name="management"></a>管理

|代码|说明|
|:--|--|
|2xx|Success|
|400|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|密钥无效|
|403|禁止 - 没有正确的权限|
|404|知识库不存在|
