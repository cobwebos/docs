---
title: 语言检测容器的 Docker 拉
titleSuffix: Azure Cognitive Services
description: 语言检测容器的 Docker 拉取命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966672"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>语言检测容器的 Docker 拉

使用[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)命令从 Microsoft 容器注册表下载容器映像。

有关文本分析容器可用标记的完整说明，请参阅 Docker Hub 上[的语言检测](https://go.microsoft.com/fwlink/?linkid=2018759)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
