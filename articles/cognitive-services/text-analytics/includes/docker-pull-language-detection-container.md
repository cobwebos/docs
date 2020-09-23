---
title: 针对语言检测容器的 Docker 请求
titleSuffix: Azure Cognitive Services
description: 适用于语言检测容器的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906124"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>针对语言检测容器的 Docker 请求

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明，请参阅 Docker 中心上的 [语言检测](https://go.microsoft.com/fwlink/?linkid=2018759) 容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
