---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/02/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fc1f4152b2e16e1597c018e5af6e0245b075c3b
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255824"
---
## <a name="robots933456-in-logs"></a>日志中的 robots933456

你可能会在容器日志中看到以下消息：

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

可以放心忽略此消息。 `/robots933456.txt` 是一个虚拟 URL 路径，应用服务使用它来检查容器能否为请求提供服务。 404 响应只是指示该路径不存在，但它让应用服务知道容器处于正常状态并已准备就绪，可以响应请求。

