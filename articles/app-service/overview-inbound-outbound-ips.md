---
title: 入站/出站 IP 地址
description: 了解如何在 Azure 应用服务中使用入站和出站 IP 地址、这些地址何时更改以及如何查找应用的地址。
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 8fa9fec9219cfd85a8a0b25f50835425766d9043
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050686"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure 应用服务中的入站和出站 IP 地址

[Azure 应用服务](overview.md)是一个多租户服务（[应用服务环境](environment/intro.md)除外）。 不在应用服务环境中（不在[隔离层](https://azure.microsoft.com/pricing/details/app-service/)中）的应用与其他应用共享网络基础结构。 因此，应用的入站和出站 IP 地址可能不同，在某些情况下甚至可能会更改。

[应用服务环境](environment/intro.md)使用专用网络基础结构，因此，应用服务环境中运行的应用将获取静态专用 IP 地址用于入站和出站连接。

## <a name="how-ip-addresses-work-in-app-service"></a>IP 地址在应用服务中的工作方式

应用服务应用在应用服务计划中运行，应用服务计划部署到 Azure 基础结构中的一个部署单元中， (内部称为 web 空间) 。 为每个部署单位分配最多五个虚拟 IP 地址，其中包含一个公用入站 IP 地址和四个出站 IP 地址。 同一部署单元中的所有应用服务计划和在其中运行的应用程序实例共享同一组虚拟 IP 地址。 为了应用服务环境 ([隔离层](https://azure.microsoft.com/pricing/details/app-service/) 中的应用服务计划) ，应用服务计划是部署单元本身，因此，虚拟 IP 地址是专用的。

由于不允许在部署单位之间移动应用服务计划，因此，分配给应用的虚拟 IP 地址通常会保持不变，但会出现例外情况。

## <a name="when-inbound-ip-changes"></a>入站 IP 更改时

不管横向扩展的实例数如何，每个应用只有一个入站 IP 地址。 执行以下操作之一时，入站 IP 地址可能会更改：

- 删除应用并在不同的资源组中重新创建该应用 (部署单位可能会改变) 。
- 删除资源组 _和_ 区域组合中的最后一个应用并重新创建它 (部署单位可能会) 更改。
- 删除基于 IP 的现有 TLS/SSL 绑定，如证书续订期间 (参阅 [续订证书](configure-ssl-certificate.md#renew-certificate)) 。

## <a name="find-the-inbound-ip"></a>找到入站 IP

只需在本地终端中运行以下命令：

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>获取静态入站 IP

有时，你可能需要对应用使用专用静态 IP 地址。 若要获取静态入站 IP 地址，需要[保护自定义域](configure-ssl-bindings.md#secure-a-custom-domain)。 如果并不真正需要使用 TLS 功能来保护应用，甚至可以上传一个自签名证书来实现此绑定。 在基于 IP 的 TLS 绑定中，证书绑定到 IP 地址本身。因此，应用服务会预配一个静态 IP 地址来实现此目的。 

## <a name="when-outbound-ips-change"></a>出站 IP 更改时

不管横向扩展的实例数如何，每个应用在任意给定时间具有指定数目的出站 IP 地址。 从应用服务应用发起的任何出站连接（例如，与后端数据库的连接）使用某个出站 IP 地址作为源 IP 地址。 在运行时随机选择要使用的 IP 地址，因此后端服务必须打开应用程序的所有出站 IP 地址的防火墙。

当您执行下列操作之一时，您的应用程序的出站 IP 地址集会发生更改：

- 删除应用并在不同的资源组中重新创建该应用 (部署单位可能会改变) 。
- 删除资源组 _和_ 区域组合中的最后一个应用并重新创建它 (部署单位可能会) 更改。
- 在较低级别 (**基本**、 **标准**和 **高级**) 以及 **高级 V2** 层之间进行缩放， (IP 地址可能会从设置) 中增加或减少。

无论是哪个定价层，你都可以通过查找 `possibleOutboundIpAddresses` 属性或者在 Azure 门户的“属性”边栏选项卡中的“其他出站 IP 地址”字段中查找你的应用可以使用的所有可能的出站 IP 地址。 请参阅[查找出站 IP](#find-outbound-ips)。

## <a name="find-outbound-ips"></a>查找出站 IP

若要在 Azure 门户中查找应用当前使用的出站 IP 地址，请单击应用左侧导航窗格中的“属性”。 它们列出在“出站 IP 地址”字段中。

在 [Cloud Shell](../cloud-shell/quickstart.md) 中运行以下命令也可以找到相同的信息。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

若要查找你的应用可能使用的所有出站 IP 地址（无论是哪个定价层），请在你的应用的左侧导航栏中单击“属性”。 它们列出在“其他出站 IP 地址”字段中。

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
