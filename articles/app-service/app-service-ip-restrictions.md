---
title: Azure App Service IP 限制 | Microsoft Docs
description: 如何将 IP 限制与 Azure App Service 配合使用
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 7/30/2018
ms.author: ccompy
ms.openlocfilehash: fb26d91ae772c4da1055da80366d6e8c6b80a6ac
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364302"
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 静态 IP 限制 #

通过 IP 限制，可定义允许访问应用的 IP 地址的允许/拒绝列表（按优先级排序）。 此允许列表可能包含 IPv4 和 IPv6 地址。 如果存在一个或多个条目，则在列表末尾会存在一个隐式的“拒绝所有”。 

IP 限制功能适用于所有应用服务托管工作负荷，包括 Web 应用、Api 应用、Linux 应用、Linux 容器应用和 Functions。 

向应用发出请求时，将针对 IP 限制列表评估 FROM IP 地址。 如果列表中的规则不允许访问该地址，则服务会以 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

IP 限制功能是在应用服务前端角色（即代码运行所在的辅助角色主机中的上游）中实现的。 因此 IP 限制是有效的网络 ACL。  

![IP 限制流](media/app-service-ip-restrictions/ip-restrictions-flow.png)

门户中的 IP 限制功能曾经是 IIS 中的 ipSecurity 功能之上的一个层。 而现在的 IP 限制功能是不同的。 现在依然可以在应用程序 web.config 中配置 ipSecurity，但是会在任何流量到达 IIS 之前应用基于前端的 IP 限制规则。

## <a name="adding-and-editing-ip-restriction-rules-in-the-portal"></a>在门户中添加并编辑 IP 限制规则 ##

若要向应用添加 IP 限制规则，请使用菜单打开**网络**>**IP 限制**，然后单击**配置 IP 限制**

![应用服务网络选项](media/app-service-ip-restrictions/ip-restrictions.png)  

从 IP 限制 UI 可以查看为应用定义的 IP 限制规则列表。

![列出 IP 限制](media/app-service-ip-restrictions/ip-restrictions-browse.png)

如果规则的配置情况如图所示，则应用只接受来自 131.107.159.0/24 的流量，并会拒绝所有来自其他的 IP 地址的流量。

可单击 **[+]** 加”以添加新的 IP 限制规则。 规则在添加后会立即生效。 规则会从最小的数字开始往上，按优先级顺序强制执行。 即使仅添加了一个规则，一个隐式的“拒绝所有”也会立即生效。 

![添加 IP 限制规则](media/app-service-ip-restrictions/ip-restrictions-add.png)

对于 IPv4 和 IPv6 地址，必须在 CIDR 表示法中指定 IP 地址表示法。 若要指定确切的地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 要详细了解 CIDR 表示法，请阅读[无类别域际路由选择](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。  

单击任一行，可编辑现有 IP 限制规则。 编辑的内容会立即生效，包括在优先级排序方面的变化。

![编辑 IP 限制规则](media/app-service-ip-restrictions/ip-restrictions-edit.png)

若要删除某个规则，请单击规则上的“...”然后单击“删除”。 

![删除 IP 限制规则](media/app-service-ip-restrictions/ip-restrictions-delete.png)

## <a name="programmatic-manipulation-of-ip-restriction-rules"></a>IP 限制规则的编程操作 ##

新的 IP 限制功能目前没有适用的 CLI 或 PowerShell，但是可以通过 PUT 操作在资源管理器中的应用配置上手动设置值。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。 

此信息在资源管理器中的位置为：

management.azure.com/subscriptions/subscription ID/resourceGroups/resource groups/providers/Microsoft.Web/sites/web app name/config/web?api-version=2018-02-01

前面的示例的 JSON 语法为：

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],
