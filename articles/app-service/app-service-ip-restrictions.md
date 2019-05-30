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
ms.date: 05/23/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: be0de7e809565fce4171401760d11ef9de45724e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236120"
---
# <a name="azure-app-service-access-restrictions"></a>Azure 应用服务访问限制 #

使用访问限制可以定义一个按优先级排序的允许/拒绝列表，用于控制在网络中对应用的访问。 该列表可以包含 IP 地址或 Azure 虚拟网络子网。 如果存在一个或多个条目，则在列表末尾会存在一个隐式的“拒绝所有”。

访问限制功能适用于所有应用服务托管工作负荷，包括 Web 应用、API 应用、Linux 应用、Linux 容器应用和 Functions。

向应用发出请求时，将会根据访问限制列表中的 IP 地址规则评估 FROM IP 地址。 如果 FROM 地址位于配置为使用 Microsoft.Web 服务终结点的子网中，则会根据访问限制列表中的虚拟网络规则比较源子网。 如果列表中的规则不允许访问该地址，则服务会以 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

访问限制功能是在应用服务前端角色（即代码运行所在的辅助角色主机中的上游）中实现的。 因此，访问限制是有效的网络 ACL。

限制从 Azure 虚拟网络 (VNet) 访问 Web 应用的功能称为[服务终结点][serviceendpoints]。 使用服务终结点可以限制为从选定的子网对多租户服务进行访问。 必须在网络端以及用于启用该功能的服务中启用该功能。 它并不适将流量限制到应用服务环境中托管的应用。  如果要在应用服务环境中，您可以控制对使用 IP 地址规则对应用程序的访问。

![访问限制流](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>在门户中添加并编辑访问限制规则 ##

若要向应用添加访问限制规则，请使用菜单打开“网络”>“访问限制”，然后单击“配置访问限制”   

![应用服务网络选项](media/app-service-ip-restrictions/access-restrictions.png)  

从访问限制 UI 可以查看为应用定义的访问限制规则列表。

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-browse.png)

该列表将显示应用中的所有当前限制。 如果应用中存在 VNet 限制，该表将显示是否为 Microsoft.Web 启用了服务终结点。 如果应用中未定义限制，则可以从任何位置访问应用。  

可单击“[+] 添加”以添加新的访问限制规则  。 规则在添加后会立即生效。 规则会从最小的数字开始往上，按优先级顺序强制执行。 即使仅添加了一个规则，一个隐式的“拒绝所有”也会立即生效。

![添加 IP 访问限制规则](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

创建规则时，必须选择“允许/拒绝”以及规则的类型。 此时，需要提供优先级值，以及要限制访问的内容。  可以选择性地添加规则的名称和说明。  

若要设置基于 IP 地址的规则，请选择 IPv4 或 IPv6 类型。 对于 IPv4 和 IPv6 地址，必须在 CIDR 表示法中指定 IP 地址表示法。 若要指定确切的地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 要详细了解 CIDR 表示法，请阅读[无类别域际路由选择](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。

![添加 VNet 访问限制规则](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

若要限制对选定子网的访问，请选择“虚拟网络”类型。 在“虚拟网络”的下面，可以选择要允许或拒绝访问的订阅、VNet 和子网。 如果尚未为选定子网的 Microsoft.Web 启用服务终结点，系统会自动启用它，除非你选中了不再询问的相应复选框。 有关何时要在应用而不是子网中启用它，在很大程度上取决于你是否有权在子网中启用服务终结点。 如果需要让其他某人在子网中启用服务终结点，可以选中相应的复选框，在预期将来要在子网中启用服务终结点的情况下，为服务终结点配置应用。 

服务终结点不能用于限制对应用服务环境中运行的应用程序的访问。 您的应用程序在应用服务环境时，可以使用 IP 访问规则向应用来控制访问。 

单击任一行，可编辑现有访问限制规则。 编辑的内容会立即生效，包括在优先级排序方面的变化。

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

编辑规则时，无法更改 IP 地址规则与虚拟网络规则这两种类型。 

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

若要删除某个规则，请单击规则上的“...”然后单击“删除”   。

![删除访问限制规则](media/app-service-ip-restrictions/access-restrictions-delete.png)

### <a name="scm-site"></a>SCM 站点 

除了能够控制对应用的访问以外，还可以限制对应用所用的 scm 站点的访问。 scm 站点是 Web 部署终结点，也是 Kudu 控制台。 对于 scm 站点，可以分配不同于应用的访问限制；也可以对应用和 scm 站点使用相同的设置。 选中相应的框来使用与应用相同的限制时，所有设置都会留空。如果取消选中该框，将应用前面针对 scm 站点指定的所有设置。 

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="blocking-a-single-ip-address"></a>阻止单个 IP 地址 ##

在添加第一个 IP 限制规则时，服务会将添加显式**全部拒绝**规则优先级为 2147483647。 在实践中，显式**全部拒绝**规则将执行的最后一条规则，并且将阻止对显式不允许使用任何 IP 地址访问**允许**规则。

用户要明确阻止的单个 IP 地址或 IP 地址块的方案，但所有内容允许的其他访问权限，有必要添加显式**允许所有**规则。

![块单个 ip 地址](media/app-service-ip-restrictions/block-single-address.png)

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

IP 限制适用于与应用服务计划具有相同功能的两种函数应用。 启用 IP 限制会针对任何不允许的 IP 禁用门户代码编辑器。

[在此处了解更多信息](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)


<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
