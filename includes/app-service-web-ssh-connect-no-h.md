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
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549993"
---
若要使开放与容器的直接 SSH 会话，应运行您的应用程序。

将以下 URL 粘贴到浏览器，然后替换\<应用名称 > 你的应用名称：

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

如果你尚不支持身份验证，必须先使用你的 Azure 订阅连接进行身份验证。 完成身份验证以后，可以看到一个浏览器内 shell，可以在其中的容器中运行命令。

![SSH 连接](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
