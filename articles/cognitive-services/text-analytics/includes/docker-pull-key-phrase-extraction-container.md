---
title: 针对关键短语提取容器的 Docker 请求
titleSuffix: Azure Cognitive Services
description: 适用于关键短语提取容器的 Docker pull 命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051313"
---
## <a name="pull-the-key-phrase-extraction-container"></a>拉取关键短语提取容器

Microsoft 容器注册表中提供了文本分析的容器映像。

| 容器 | 容器注册表/存储库/映像名称 |
|-----------|------------|
| 关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器的可用标记的完整说明, 请参阅 Docker 中心上的[关键短语提取](https://go.microsoft.com/fwlink/?linkid=2018757)容器。

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>针对关键短语提取容器的 Docker 请求

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
