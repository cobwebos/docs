---
title: API HTTP 响应代码 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 了解从 QnA Maker API 返回的 HTTP 响应代码。 这将帮助解决任何错误。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 0416b282a869536b0cea591ba39d7a3804697bac
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446524"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API HTTP 响应代码
[管理](https://go.microsoft.com/fwlink/?linkid=2092179)和预测 API 返回 HTTP 响应代码。 响应消息包含特定于某个请求的信息，而 HTTP 响应状态代码是通用的。 

### <a name="management"></a>管理

|代码|说明|
|:--|--|
|2xx|Success|
|400|请求的参数不正确，这意味着所需的参数缺失、格式错误或太大|
|400|请求的正文不正确，这意味着 JSON 缺失、格式错误或太大|
|401|密钥无效|
|403|禁止 - 没有正确的权限|
|404|知识库不存在|
|410|此 API 已弃用，不再可用|
