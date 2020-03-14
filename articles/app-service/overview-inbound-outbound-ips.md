---
title: 入站/出站 IP 地址
description: 了解如何在 Azure App Service 中使用入站和出站 IP 地址，以及如何使用它们来查找应用程序的地址。
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: aebce04fe2f1b055a4d498021dcd25144cd122a9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279203"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure 应用服务中的入站和出站 IP 地址

[Azure 应用服务](overview.md)是一个多租户服务（[应用服务环境](environment/intro.md)除外）。 不在应用服务环境中（不在[隔离层](https://azure.microsoft.com/pricing/details/app-service/)中）的应用与其他应用共享网络基础结构。 因此，应用的入站和出站 IP 地址可能不同，在某些情况下甚至可能会更改。 

[应用服务环境](environment/intro.md)使用专用网络基础结构，因此，应用服务环境中运行的应用将获取静态专用 IP 地址用于入站和出站连接。

## <a name="when-inbound-ip-changes"></a>入站 IP 更改时

不管横向扩展的实例数如何，每个应用只有一个入站 IP 地址。 执行以下操作之一时，入站 IP 地址可能会更改：

- 删除应用，然后在不同的资源组中重新创建它。
- 删除资源组和区域组合中的最后一个应用，然后重新创建它。
- 删除现有的 SSL 绑定，如证书续订期间（请参阅[续订证书](configure-ssl-certificate.md#renew-certificate)）。

## <a name="find-the-inbound-ip"></a>查找入站 IP

只需在本地终端中运行以下命令：

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>获取静态入站 IP

有时，你可能需要对应用使用专用静态 IP 地址。 若要获取静态入站 IP 地址，需要配置[基于 IP 的 SSL 绑定](configure-ssl-bindings.md#secure-a-custom-domain)。 如果并不真正需要使用 SSL 功能来保护应用，甚至可以上传一个自签名证书来实现此绑定。 在基于 IP 的 SSL 绑定中，证书将绑定到 IP 地址本身，因此，应用服务会预配一个静态 IP 地址来实现此目的。 

## <a name="when-outbound-ips-change"></a>出站 IP 更改时

不管横向扩展的实例数如何，每个应用在任意给定时间具有指定数目的出站 IP 地址。 从应用服务应用发起的任何出站连接（例如，与后端数据库的连接）使用某个出站 IP 地址作为源 IP 地址。 无法预先知道给定的应用实例要使用哪个 IP 地址来发起出站连接，因此，后端服务必须向应用的所有出站 IP 地址开放其防火墙。

在较低层（“基本”、“标准”和“高级”）与“高级 V2”层之间缩放应用时，应用的出站 IP 地址集会发生更改。

通过在 Azure 门户的 "属性" 边栏选项卡的 "**属性**" 边栏选项卡中查找 "`possibleOutboundIpAddresses`" 或 "**其他出站 ip 地址**" 字段，你可以查找应用可以使用的所有可能的出站 ip 地址集，而不考虑定价层。 请参阅[查找出站 IP](#find-outbound-ips)。

## <a name="find-outbound-ips"></a>查找出站 IP

若要在 Azure 门户中查找应用当前使用的出站 IP 地址，请单击应用左侧导航窗格中的“属性”。 它们列在 "**出站 IP 地址**" 字段中。

在 [Cloud Shell](../cloud-shell/quickstart.md) 中运行以下命令也可以找到相同的信息。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

若要查找应用的_所有_可能的出站 IP 地址，而不考虑定价层，请单击应用左侧导航栏中的 "**属性**"。 它们列在 "**其他出站 IP 地址**" 字段中。

在 [Cloud Shell](../cloud-shell/quickstart.md) 中运行以下命令也可以找到相同的信息。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>后续步骤

了解如何按源 IP 地址限制入站流量。

> [!div class="nextstepaction"]
> [静态 IP 限制](app-service-ip-restrictions.md)
