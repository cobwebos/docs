---
title: "Azure App Service IP 限制 | Microsoft Docs"
description: "如何将 IP 限制与 Azure App Service 配合使用"
author: btardif
manager: stefsch
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 551f34436dfeac14bb6e8676dd7c9b10d30a3af3
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 静态 IP 限制 #

通过 IP 限制，可定义允许访问应用的 IP 地址的列表。 允许列表可包括单个 IP 地址或由子网掩码定义的 IP 地址范围。

从客户端生成应用请求时，将根据允许列表评估 IP 地址。 如果 IP 地址不在该列表中，则应用会以 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

在应用在运行时使用的 web.config 中定义 IP 限制（更准确地说，在 applicationHost.config 的一组允许的 IP 地址中插入限制，因此，如果你也在 web.config 文件中添加一组允许的 IP 地址，则这些 IP 地址优先）。 在某些情况下，可能会在 HTTP 管道中的 IP 限制逻辑之前执行一些模块。 在此情况下，请求会失败并显示不同的 HTTP 错误代码。

IP 限制是在分配给应用的同一应用服务计划实例上进行评估的。

若要向应用添加 IP 限制规则，请使用菜单打开**网络**>**IP 限制**，然后单击**配置 IP 限制**

![IP 限制](media/app-service-ip-restrictions/ip-restrictions.png)

从此处可以查看为应用定义的 IP 限制规则列表。

![列出 IP 限制](media/app-service-ip-restrictions/browse-ip-restrictions.png)

可单击**[+]** 加”以添加新的 IP 限制规则。

![添加 IP 限制](media/app-service-ip-restrictions/add-ip-restrictions.png)
