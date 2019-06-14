---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66138202"
---
若要通过容器打开直接的 SSH 会话，应用应该处于正在运行状态。

将以下 URL 粘贴到浏览器中，将 \<app-name> 替换为应用名称：

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

如果尚未进行身份验证，则需通过要连接的 Azure 订阅进行身份验证。 完成身份验证以后，可以看到一个浏览器内 shell，可以在其中的容器中运行命令。

![SSH 连接](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
