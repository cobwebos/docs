---
title: 运行状况容器的 Docker 拉取
titleSuffix: Azure Cognitive Services
description: 用于运行状况容器文本分析的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779159"
---
填写并提交[认知服务容器请求表单](https://aka.ms/csgate)以请求访问容器。
通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交表单后，Azure 认知服务团队可以检查它，确保你满足访问专用容器注册表的条件。

> [!IMPORTANT]
> * 在窗体上，必须使用与 Azure 订阅 ID 关联的电子邮件地址。
> * 用于运行容器的 Azure 资源必须已使用已批准的 Azure 订阅 ID 创建。 
> * 检查 "收件箱" 和 "垃圾邮件" 文件夹 () 有关 Microsoft 应用程序状态的更新的电子邮件。

将 docker login 命令与载入电子邮件中提供的凭据结合使用，以连接到认知服务容器的专用容器注册表。


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

使用 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 命令从专用容器注册表下载此容器映像。

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
