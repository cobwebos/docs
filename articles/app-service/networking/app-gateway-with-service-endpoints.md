---
title: 应用程序网关与服务终结点的集成 - Azure 应用服务 | Microsoft Docs
description: 介绍应用程序网关如何与使用服务终结点保护的 Azure 应用服务集成。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "74980057"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>应用程序网关与服务终结点的集成
应用服务有三种变体，需要对它们采用略微不同的配置，才能使其与 Azure 应用程序网关集成。 这些变体包括普通应用服务（也称为多租户）、内部负载均衡器 (ILB) 应用服务环境 (ASE) 和外部 ASE。 本文逐步介绍如何使用应用服务（多租户）对其进行配置，并讨论有关 ILB 和外部 ASE 的注意事项。

## <a name="integration-with-app-service-multi-tenant"></a>与应用服务（多租户）集成
应用服务（多租户）具有面向 Internet 的公共终结点。 使用[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)可以只允许来自 Azure 虚拟网络中特定子网的流量，并阻止其他所有流量。 在以下方案中，我们将使用此功能来确保应用服务实例只能接收来自特定应用程序网关实例的流量。

![应用程序网关与应用服务的集成](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

除了创建应用服务和应用程序网关以外，此配置还包括两个部分。 第一个部分是在部署应用程序网关的虚拟网络子网中启用服务终结点。 服务终结点确保所有离开子网发往应用服务的所有网络流量通过特定的子网 ID 进行标记。 第二个部分是设置特定 Web 应用的访问限制，以确保仅允许通过此特定子网 ID 标记的流量。 可以根据偏好使用不同的工具完成此项配置。

## <a name="using-azure-portal"></a>使用 Azure 门户
在 Azure 门户中，可以通过四个步骤来预配和配置设置。 如果已有现有的资源，则可以跳过前几个步骤。
1. 参考应用服务文档中的某篇快速入门（例如 [.Net Core 快速入门](../../app-service/app-service-web-get-started-dotnet.md)）创建应用服务
2. 参考[门户快速入门](../../application-gateway/quick-create-portal.md)创建应用程序网关，但请跳过“添加后端目标”部分。
3. [将应用服务配置为应用程序网关中的后端](../../application-gateway/configure-web-app-portal.md)，但请跳过“限制访问”部分。
4. 最后，[使用服务终结点创建访问限制](../../app-service/app-service-ip-restrictions.md#service-endpoints)。

现在，可以通过应用程序网关访问应用服务，但如果尝试直接访问应用服务，将会收到 403 HTTP 错误，表示网站已停止。

![应用程序网关与应用服务的集成](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
[资源管理器部署模板][template-app-gateway-app-service-complete]将预配完整方案。 此方案包括使用服务终结点和访问限制锁定的应用服务实例，以便仅从应用程序网关接收流量。 该模板包含许多智能默认值，以及添加到资源名称的唯一后缀（以简化模板）。 若要替代这些内容，必须克隆存储库，或下载模板并对其进行编辑。 

若要应用模板，可以使用模板说明中的“部署到 Azure”按钮，或者使用相应的 PowerShell/CLI。

## <a name="using-azure-command-line-interface"></a>使用 Azure 命令行接口
[Azure CLI 示例](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md)将预配使用服务终结点和访问限制锁定的应用服务，以便仅从应用程序网关接收流量。 如果只需从现有的应用程序网关将流量隔离到现有应用服务，则以下命令就已足够。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

在默认配置中，该命令将确保在子网中设置服务终结点配置，并在应用服务中设置访问限制。

## <a name="considerations-for-ilb-ase"></a>ILB ASE 的注意事项
ILB ASE 不会向 Internet 公开，因此，实例与应用程序网关之间的流量已隔离到虚拟网络。 以下[操作指南](../environment/integrate-with-application-gateway.md)使用 Azure 门户配置 ILB ASE 并将其与应用程序网关集成。 

若要确保只有来自应用程序网关子网的流量抵达 ASE，可以配置一个影响 ASE 中所有 Web 应用的网络安全组 (NSG)。 对于 NSG，可以指定子网 IP 范围，并选择性地指定端口 (80/443)。 切勿替代[所需的 NSG 规则](../environment/network-info.md#network-security-groups)，使 ASE 正常运行。

若要将流量隔离到单个 Web 应用，需要使用基于 IP 的访问限制，因为服务终结点不适用于 ASE。 IP 地址应该是应用程序网关实例的专用 IP。

## <a name="considerations-for-external-ase"></a>外部 ASE 的注意事项
与多租户应用服务一样，外部 ASE 具有面向公众的负载均衡器。 服务终结点不适用于 ASE，正因如此，必须使用应用程序网关实例的公共 IP 来实施基于 IP 的访问限制。 若要使用 Azure 门户创建外部 ASE，可以遵循[此快速入门](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "用于完整方案的 Azure 资源管理器模板"

## <a name="considerations-for-kuduscm-site"></a>kudu/scm 站点的注意事项
scm 站点（也称为 kudu）是每个 Web 应用都包含的管理站点。 无法反向代理 scm 站点。你很有可能需要将其锁定到单个 IP 地址或特定的子网。

若要使用与主站点相同的访问限制，可以使用以下命令继承设置。

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

若要单独对 scm 站点设置访问限制，可以使用 --scm-site 标志添加访问限制，如下所示。

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>后续步骤
有关应用服务环境的详细信息，请参阅[应用服务环境文档](https://docs.microsoft.com/azure/app-service/environment)。

若要进一步保护 Web 应用，请参阅 [Azure Web 应用程序防火墙文档](../../web-application-firewall/ag/ag-overview.md)中有关应用程序网关上的 Web 应用程序防火墙的信息。