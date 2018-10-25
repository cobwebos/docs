---
title: API HTTP 响应代码 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 了解从 QnA Maker API 返回的 HTTP 响应代码
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 96782d32817e4989c02e0ed098d7772c80aa26c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079663"
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