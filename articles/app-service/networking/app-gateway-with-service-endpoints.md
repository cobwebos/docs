---
title: 应用程序网关与服务终结点的集成-Azure App Service |Microsoft Docs
description: 描述应用程序网关如何与服务终结点保护 Azure App Service。
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980057"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>应用程序网关与服务终结点的集成
应用服务有三种变体，它需要与 Azure 应用程序网关的集成略有不同的配置。 变体包括常规应用服务（也称为多租户、内部负载均衡器（ILB）应用服务环境（ASE）和外部 ASE。 本文介绍如何使用应用服务（多租户）对其进行配置，并讨论有关 ILB 和外部 ASE 的注意事项。

## <a name="integration-with-app-service-multi-tenant"></a>与应用服务集成（多租户）
应用服务（多租户）具有面向 internet 的公共终结点。 使用[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)可以只允许来自 Azure 虚拟网络中特定子网的流量，并阻止其他所有内容。 在以下方案中，我们将使用此功能来确保应用服务实例只能接收来自特定应用程序网关实例的流量。

![应用程序网关与应用服务的集成](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

除了创建应用服务和应用程序网关外，此配置还包括两个部分。 第一部分是在部署应用程序网关的虚拟网络的子网中启用服务终结点。 服务终结点将确保所有网络流量（将子网发送到应用服务）都将用特定的子网 ID 进行标记。 第二部分是设置特定 web 应用的访问限制，以确保仅允许使用此特定子网 ID 标记的流量。 你可以使用不同的工具配置它，具体取决于首选项。

## <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中，可按照四个步骤设置和配置设置。 如果有现成的资源，可以跳过第一步。
1. 使用应用服务文档中的快速入门之一创建应用服务，例如[.Net Core 快速入门](../../app-service/app-service-web-get-started-dotnet.md)
2. 使用[门户快速入门](../../application-gateway/quick-create-portal.md)创建应用程序网关，但跳过 "添加后端目标" 部分。
3. [在应用程序网关上将应用服务配置为后端](../../application-gateway/configure-web-app-portal.md)，但跳过限制访问部分。
4. 最后，[使用服务终结点创建访问限制](../../app-service/app-service-ip-restrictions.md#service-endpoints)。

你现在可以通过应用程序网关访问应用服务，但如果你尝试直接访问应用服务，则应收到 403 HTTP 错误，指示该网站已停止。

![应用程序网关与应用服务的集成](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
[资源管理器部署模板][template-app-gateway-app-service-complete]将预配一个完整方案。 此方案包含使用服务终结点锁定的应用服务实例和访问限制，仅接收来自应用程序网关的流量。 该模板包含多个智能默认值以及添加到资源名称中的唯一 postfixes。 若要重写这些存储库，必须克隆存储库或下载模板并对其进行编辑。 

若要应用模板，你可以使用模板描述中的 "部署到 Azure" 按钮，也可以使用适当的 PowerShell/CLI。

## <a name="using-azure-command-line-interface"></a>使用 Azure 命令行接口
[Azure CLI 示例](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md)将使用服务终结点和访问限制设置锁定的应用服务，以便仅接收来自应用程序网关的流量。 如果只需要从现有应用程序网关将流量隔离到现有应用服务，则以下命令就足够了。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

在默认配置中，该命令将确保子网中的服务终结点配置和应用服务中的访问限制。

## <a name="considerations-for-ilb-ase"></a>ILB ASE 注意事项
ILB ASE 不会向 internet 公开，因此，实例和应用程序网关之间的流量已隔离到虚拟网络。 以下操作[方法指南](../environment/integrate-with-application-gateway.md)配置 ILB ASE，并将其与使用 Azure 门户的应用程序网关集成。 

如果要确保只有来自应用程序网关子网的流量到达 ASE，可以配置网络安全组（NSG），这会影响 ASE 中的所有 web 应用。 对于 NSG，可以指定子网 IP 范围和（可选）端口（80/443）。 请确保不要覆盖 ASE[所需的 NSG 规则](../environment/network-info.md#network-security-groups)才能正常运行。

若要将流量隔离到单个 web 应用，需要使用基于 ip 的访问限制，因为服务终结点对于 ASE 将不起作用。 IP 地址应是应用程序网关实例的专用 IP。

## <a name="considerations-for-external-ase"></a>外部 ASE 注意事项
外部 ASE 具有面向公众的负载均衡器，如多租户应用服务。 服务终结点不适用于 ASE，这就是你必须使用应用程序网关实例的公共 IP 来使用基于 ip 的访问限制的原因。 若要使用 Azure 门户创建外部 ASE，可以按照本[快速入门](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "适用于完整方案的 Azure 资源管理器模板"

## <a name="considerations-for-kuduscm-site"></a>Kudu/scm 站点的注意事项
Scm 站点（也称为 kudu）是一个管理站点，适用于每个 web 应用。 不能对 scm 站点进行反向代理，并且很可能还需要将其锁定到各个 IP 地址或特定的子网。

如果要使用与主站点相同的访问限制，则可以使用以下命令继承设置。

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

如果要设置对 scm 站点的单独访问限制，可以使用--scm-site 标志（如下所示）来添加访问限制。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>后续步骤
有关应用服务环境的详细信息，请参阅[应用服务环境文档](https://docs.microsoft.com/azure/app-service/environment)。

为了进一步保护您的 web 应用程序，可以在[Azure Web 应用程序防火墙文档](../../web-application-firewall/ag/ag-overview.md)中找到有关应用程序网关的 web 应用程序防火墙的信息。