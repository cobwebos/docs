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
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822814"
---
若要通过容器打开直接的 SSH 会话，应用应该处于正在运行状态。

将以下 URL 粘贴到浏览器中，将 `<app-name>` 替换为应用名称：

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

如果尚未进行身份验证，则需通过要连接的 Azure 订阅进行身份验证。 完成身份验证以后，可以看到一个浏览器内 shell，可以在其中的容器中运行命令。

![SSH 连接](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
