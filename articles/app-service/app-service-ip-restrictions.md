---
title: 限制访问-Azure 应用服务 |Microsoft Docs
description: 如何将访问限制与 Azure 应用服务配合使用
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
ms.date: 04/22/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: de898a7ebb9611f469f42bb23774b3b0a0c2410d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541671"
---
# <a name="azure-app-service-access-restrictions"></a>Azure 应用服务访问限制 #

访问限制，可以定义控制对您的应用程序的网络访问的优先级有序允许/拒绝列表。 列表可以包含 IP 地址或 Azure 虚拟网络子网。 当存在一个或多个条目时，没有然后隐式"全部拒绝"位于列表的末尾。

访问限制功能适用于所有应用服务托管的工作将加载包括;web 应用、 API 应用、 Linux 应用、 Linux 容器应用和函数。

当向应用程序发出请求时，针对你访问限制列表中的 IP 地址规则计算发件人地址。 如果发件人地址是使用 Microsoft.Web 的服务终结点配置的子网中，源子网进行比较的访问限制列表中的虚拟网络规则。 如果列表中的规则不允许访问该地址，则服务会以 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

访问限制功能是在应用服务前端角色，这是你的代码在其中运行的辅助主机上游中实现的。 因此，访问限制实际上是网络 Acl。

访问限于你的 web 应用从 Azure 虚拟网络 (VNet) 的功能称为[服务终结点][serviceendpoints]。 服务终结点，可以从所选子网限制对多租户服务的访问。 必须在网络端以及启用了该服务上启用它。 它并不适将流量限制到应用服务环境中托管的应用。  如果要在应用服务环境中，您可以控制对使用 IP 地址规则对应用程序的访问。

![访问限制流](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>在门户中添加并编辑访问限制规则 ##

若要向应用添加访问限制规则，请使用菜单打开“网络”>“访问限制”，然后单击“配置访问限制”

![应用服务网络选项](media/app-service-ip-restrictions/access-restrictions.png)  

从访问限制 UI 可以查看为应用定义的访问限制规则列表。

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-browse.png)

该列表将显示所有位于您的应用程序的当前限制。 如果您的应用程序中有一个 VNet 的限制，表将显示如果为 Microsoft.Web 启用服务终结点。 如果你的应用没有定义的限制，您的应用程序将可从任何位置访问。  

可单击“[+] 添加”以添加新的访问限制规则。 规则在添加后会立即生效。 规则会从最小的数字开始往上，按优先级顺序强制执行。 即使仅添加了一个规则，一个隐式的“拒绝所有”也会立即生效。

![添加 IP 访问限制规则](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

创建规则时，必须选择允许/拒绝以及规则的类型。 您还将要求提供优先级值和要限制访问权限。  可以选择添加到规则名称和描述。  

若要设置的 IP 地址基于规则，选择一种类型的 IPv4 或 IPv6。 对于 IPv4 和 IPv6 地址，必须在 CIDR 表示法中指定 IP 地址表示法。 若要指定确切的地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 要详细了解 CIDR 表示法，请阅读[无类别域际路由选择](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

![添加 VNet 访问限制规则](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

若要限制对所选子网的访问，选择虚拟网络的类型。 在下面您将能够选择订阅、 VNet 和子网你想要允许或拒绝访问与。 如果服务终结点不具有 Microsoft.Web 为启用所选的子网，它将自动启用，除非选中复选框不询问执行该操作。 你会想要启用该应用，但不能是子网的这种情况很大程度上与如果您有权启用服务终结点的子网上，或不相关。 如果您需要先获取其他人来启用服务终结点的子网上，可以选中复选框，并让你为预期的更高版本的子网上启用服务终结点配置的应用。 

服务终结点不能用于限制对应用服务环境中运行的应用程序的访问。 您的应用程序在应用服务环境时，可以使用 IP 访问规则向应用来控制访问。 

单击任一行，可编辑现有访问限制规则。 编辑的内容会立即生效，包括在优先级排序方面的变化。

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

如果编辑规则，则无法更改 IP 地址规则与虚拟网络规则之间的类型。 

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

若要删除某个规则，请单击规则上的“...”然后单击“删除”。

![删除访问限制规则](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>SCM 站点 

除了能够控制对您的应用程序的访问外，还可以到 scm 站点使用您的应用程序的限制访问。 Scm 站点是 web 部署终结点，还可在 Kudu 控制台。 可以单独从应用分配到 scm 站点的访问限制或使用相同的应用程序和 scm 站点设置。 选中框以获得与您的应用程序相同的限制，所有内容是留空。如果取消选中的框，应用必须前面 scm 站点的任何设置。 

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>访问限制规则的编程操作 ##

新的访问限制功能目前没有适用的 CLI 或 PowerShell，但是可以通过 PUT 操作在资源管理器中的应用配置上手动设置值。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。

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

IP 限制是可用于与应用服务计划相同的功能与这两个函数应用。 启用 IP 限制，则将禁用任何不允许的 Ip 门户代码编辑器。

[在此处了解更多信息](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
