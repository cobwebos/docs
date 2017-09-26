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
ms.date: 09/12/2017
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 83cdc42d412f646ddf1ecd1b65bf9aa46983b26b
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 静态 IP 限制 #

通过 IP 限制，可定义要阻止访问应用的 IP 地址的列表。 阻止列表可包括单个 IP 地址或由子网掩码定义的 IP 地址范围。

从客户端生成应用请求时，将对照阻止列表评估 IP 地址。 如果有匹配，应用将回复 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码。

IP 限制是在分配给应用的同一应用服务计划实例上进行评估的。

若要向应用添加 IP 限制规则，请使用菜单打开“网络”>“IP 限制”，然后单击“配置 IP 限制”

![IP 限制](media/app-service-ip-restrictions/ip-restrictions.png)

从此处可以查看为应用定义的 IP 限制规则列表。

![列出 IP 限制](media/app-service-ip-restrictions/browse-ip-restrictions.png)

可单击“[+] 添加”以添加新的 IP 限制规则。

![添加 IP 限制](media/app-service-ip-restrictions/add-ip-restrictions.png)

