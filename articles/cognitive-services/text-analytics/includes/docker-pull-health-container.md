---
title: 运行状况容器的 Docker 拉取
titleSuffix: Azure Cognitive Services
description: 用于运行状况容器文本分析的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108894"
---
填写并提交[认知服务容器请求窗体](https://aka.ms/cognitivegate)，请求对容器的访问权限。

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

将 docker login 命令与载入电子邮件中提供的凭据结合使用，以连接到认知服务容器的专用容器注册表。

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从专用容器注册表下载此容器映像。

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
