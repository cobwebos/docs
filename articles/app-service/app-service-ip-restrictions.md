---
title: 限制客户端 IP - Azure 应用服务 | Microsoft Docs
description: 如何使用 Azure 应用服务的访问限制
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
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357619"
---
# <a name="azure-app-service-static-access-restrictions"></a>Azure App Service 静态访问限制 #

访问限制，可定义允许/拒绝的排序列表允许访问您的应用程序的 IP 地址的优先级。 此允许列表可能包含 IPv4 和 IPv6 地址。 如果存在一个或多个条目，则在列表末尾会存在一个隐式的“拒绝所有”。

访问限制功能适用于所有应用服务托管工作负荷，包括：web 应用、 API 应用、 Linux 应用、 Linux 容器应用和函数。

当向应用程序发出请求时，针对访问限制列表计算从 IP 地址。 如果列表中的规则不允许访问该地址，则服务会以 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

应用服务前端角色，这是你的代码在其中运行的辅助主机上游中实现的访问限制功能。 因此，访问限制实际上是网络 Acl。  

![访问限制流](media/app-service-ip-restrictions/ip-restrictions-flow.png)

时间内，在门户中的访问限制功能已在 IIS 中的 ip 安全功能之上的一个层。 当前的访问限制功能是不同的。 您仍可以配置 ip 安全应用程序 web.config 内，但任何流量到达 IIS 之前，将应用前端的基于的访问限制规则。

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>添加和编辑在门户中的访问限制规则 ##

若要将访问限制规则添加到您的应用程序，使用菜单打开**网络**>**访问限制**，然后单击**配置访问限制**

![应用服务网络选项](media/app-service-ip-restrictions/ip-restrictions.png)  

从访问限制 UI，可以查看为您的应用程序定义的访问限制规则的列表。

![列表的访问限制](media/app-service-ip-restrictions/ip-restrictions-browse.png)

如果规则的配置情况如图所示，则应用只接受来自 131.107.159.0/24 的流量，并会拒绝所有来自其他的 IP 地址的流量。

你可以单击 **[+] 添加**以添加新的访问限制规则。 规则在添加后会立即生效。 规则会从最小的数字开始往上，按优先级顺序强制执行。 即使仅添加了一个规则，一个隐式的“拒绝所有”也会立即生效。

![添加访问权限限制规则](media/app-service-ip-restrictions/ip-restrictions-add.png)

对于 IPv4 和 IPv6 地址，必须在 CIDR 表示法中指定 IP 地址表示法。 若要指定确切的地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 要详细了解 CIDR 表示法，请阅读[无类别域际路由选择](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。  

您可以单击任何行，以编辑现有的访问限制规则。 编辑的内容会立即生效，包括在优先级排序方面的变化。

![编辑访问权限限制规则](media/app-service-ip-restrictions/ip-restrictions-edit.png)

若要删除某个规则，请单击规则上的“...”然后单击“删除”。

![删除访问权限限制规则](media/app-service-ip-restrictions/ip-restrictions-delete.png)

此外可以限制部署访问下一步选项卡中。添加/编辑/删除与每个规则，执行与上面相同的步骤。

![列表的访问限制](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>以编程方式操作的访问限制规则 ##

当前没有 CLI 或 PowerShell 对新的访问限制功能，但可以使用 PUT 操作上应用配置资源管理器中手动设置值。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。

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

## <a name="function-app-ip-restrictions"></a>函数应用 IP 限制

IP 限制是可用于与应用服务计划相同的功能与这两个函数应用。 请注意，启用 IP 限制将禁用任何不允许的 Ip 门户代码编辑器。

[此处详细了解](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)