---
title: 针对语言检测容器的 Docker 请求
titleSuffix: Azure Cognitive Services
description: 适用于语言检测容器的 Docker pull 命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051287"
---
## <a name="pull-the-language-detection-container"></a>拉取语言检测容器

Microsoft 容器注册表中提供了文本分析的容器映像。

| 容器 | 容器注册表/存储库/映像名称 |
|-----------|------------|
| 语言检测 | `mcr.microsoft.com/azure-cognitive-services/language` |

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明, 请参阅 Docker 中心上的[语言检测](https://go.microsoft.com/fwlink/?linkid=2018759)容器。


### <a name="docker-pull-for-the-language-detection-container"></a>针对语言检测容器的 Docker 请求

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
