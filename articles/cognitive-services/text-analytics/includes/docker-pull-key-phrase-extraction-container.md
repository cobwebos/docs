---
title: 针对关键短语提取容器的 Docker 请求
titleSuffix: Azure Cognitive Services
description: 适用于关键短语提取容器的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906083"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>针对关键短语提取容器的 Docker 请求

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明，请参阅 Docker 中心上的 [关键短语提取](https://go.microsoft.com/fwlink/?linkid=2018757) 容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
