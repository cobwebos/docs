---
title: Azure App Service 访问限制
description: 了解如何通过指定访问限制在 Azure App Service 中保护应用。
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 42f25c1b66261ac644f015290bed2c7473acbdaa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280347"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App Service 访问限制 #

访问限制使你能够定义按优先级排序的允许/拒绝列表，该列表控制对你的应用程序的网络访问。 列表可以包括 IP 地址或 Azure 虚拟网络子网。 如果有一个或多个条目，则会在列表的末尾存在一个隐式 "拒绝所有"。

访问限制功能适用于所有应用服务托管的工作负载，包括：web 应用、API 应用、Linux 应用、Linux 容器应用和功能。

向你的应用程序发出请求时，将根据访问限制列表中的 IP 地址规则来评估发件人地址。 如果 "发件人" 地址在配置了 "服务终结点到 Microsoft" 的子网中，则会将源子网与访问限制列表中的虚拟网络规则进行比较。 如果列表中的规则不允许访问该地址，则服务会以 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 状态代码进行答复。

访问限制功能在应用服务前端角色中实现，这些角色是运行代码的辅助角色主机的上游。 因此，访问限制是有效的网络 Acl。

从 Azure 虚拟网络（VNet）限制对 web 应用的访问的能力称为[服务终结点][serviceendpoints]。 使用服务终结点可以限制对选定子网的多租户服务的访问。 必须在网络端和正在启用它的服务上启用它。 它不能用于将流量限制到应用服务环境中托管的应用。 如果你使用的是应用服务环境，则可以使用 IP 地址规则控制对应用程序的访问。

![访问限制流](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>在门户中添加和编辑访问限制规则 ##

若要向应用添加访问限制规则，请使用菜单打开**网络**>**访问限制**，并单击 "**配置访问限制**"

![应用服务网络选项](media/app-service-ip-restrictions/access-restrictions.png)  

从 "访问限制" UI 中，可以查看为应用定义的访问限制规则的列表。

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-browse.png)

此列表将显示应用上的所有当前限制。 如果对应用具有 VNet 限制，则该表将显示是否为 Microsoft 启用了服务终结点。 如果没有为应用定义限制，你的应用可从任何位置进行访问。  

## <a name="adding-ip-address-rules"></a>添加 IP 地址规则

您可以单击 "**添加**" 以添加新的访问限制规则。 规则在添加后会立即生效。 规则会从最小的数字开始往上，按优先级顺序强制执行。 即使仅添加了一个规则，一个隐式的“拒绝所有”也会立即生效。

创建规则时，必须选择 "允许/拒绝"，同时选择规则类型。 还需要提供优先级值和你要限制其访问权限的内容。  您可以根据需要向规则中添加名称和说明。  

![添加 IP 访问限制规则](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

若要设置基于 IP 地址的规则，请选择类型的 IPv4 或 IPv6。 对于 IPv4 和 IPv6 地址，必须在 CIDR 表示法中指定 IP 地址表示法。 若要指定确切的地址，可以使用类似 1.2.3.4/32 的格式，其中前四个八位字节代表自己的 IP 地址，/32 为掩码。 所有地址的 IPv4 CIDR 表示法都为 0.0.0.0/0。 要详细了解 CIDR 表示法，请阅读[无类别域际路由选择](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 

## <a name="service-endpoints"></a>服务终结点

通过服务终结点，你可以限制对所选 Azure 虚拟网络子网的访问。 若要限制对特定子网的访问，请使用一种类型的虚拟网络创建一个限制规则。 你可以选择要允许或拒绝访问的订阅、VNet 和子网。 如果你选择的子网没有为你的 Web.config 启用服务终结点，则会自动为你启用该服务终结点，除非你选中 "不要求执行此操作" 复选框。 如果你有权在子网上启用服务终结点，则需要在应用程序而不是子网的情况下启用它。 如果需要让其他人在子网上启用服务终结点，则可以选中此框，并将应用程序配置为服务终结点，以便以后在子网中启用它。 

![添加 VNet 访问限制规则](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

服务终结点不能用于限制对在应用服务环境中运行的应用程序的访问。 当应用处于应用服务环境时，可以使用 IP 访问规则来控制对应用的访问。 

通过服务终结点，你可以配置应用程序网关或其他 WAF 设备。 还可以配置具有 secure 后端的多层应用程序。 有关某些可能性的详细信息，请参阅[联网功能和应用服务](networking-features.md)以及[应用程序网关与服务终结点的集成](networking/app-gateway-with-service-endpoints.md)。

## <a name="managing-access-restriction-rules"></a>管理访问限制规则

可以单击任意行来编辑现有的访问限制规则。 编辑的内容会立即生效，包括在优先级排序方面的变化。

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

编辑规则时，无法在 IP 地址规则和虚拟网络规则之间更改类型。 

![编辑访问限制规则](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

若要删除某个规则，请单击规则上的“...”然后单击“删除”。

![删除访问限制规则](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>阻止单个 IP 地址 ##

添加第一个 IP 限制规则时，服务将添加优先级为2147483647的显式**拒绝全部**规则。 在实践中，显式 "**全部拒绝**" 规则将是最后一条规则执行的，并且会阻止访问任何未使用**Allow**规则显式允许的 IP 地址。

如果用户想要显式阻止单个 IP 地址或 IP 地址块，但允许其他所有访问权限，则必须添加显式 "**允许所有**" 规则。

![阻止单个 ip 地址](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM 站点 

除了能够控制对应用程序的访问权限外，还可以限制对应用程序使用的 scm 站点的访问。 Scm 站点是 web 部署终结点，也是 Kudu 控制台。 你可以从应用单独分配对 scm 站点的访问限制，或对应用和 scm 站点使用相同的设置。 如果选中此复选框以具有与应用相同的限制，则所有内容都将被遮蔽。如果取消选中此框，则会应用您之前在 scm 站点上的任何设置。 

![列出访问限制](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>访问限制规则的编程操作 ##

[Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/access-restriction?view=azure-cli-latest)和[Azure PowerShell](https://docs.microsoft.com/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0)支持编辑访问限制。 使用 Azure CLI 添加访问限制的示例：

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
使用 Azure PowerShell 添加访问限制的示例：

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

还可使用[azure REST API](https://docs.microsoft.com/rest/api/azure/) PUT 操作在资源管理器或使用 azure 资源管理器模板的应用配置上手动设置值。 例如，可以使用 resources.azure.com 并编辑 ipSecurityRestrictions 块以添加所需的 JSON。

此信息在资源管理器中的位置为：

management.azure.com/subscriptions/subscription ID/resourceGroups/resource groups/providers/Microsoft.Web/sites/web app name/config/web?api-version=2018-02-01

前面的示例的 JSON 语法为：
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-function-app-access-restrictions"></a>Azure Function App 访问限制

访问限制适用于其功能与应用服务计划相同的函数应用。 启用访问限制将对任何不允许的 Ip 禁用门户代码编辑器。

## <a name="next-steps"></a>后续步骤
[Azure Function App 的访问限制](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[应用程序网关与服务终结点的集成](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
