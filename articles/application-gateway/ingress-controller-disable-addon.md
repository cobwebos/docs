---
title: 为 Azure Kubernetes 服务群集禁用并重新启用应用程序网关入口控制器加载项
description: 本文介绍如何为 AKS 群集禁用和重新启用 AGIC 加载项
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807951"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>为 AKS 群集禁用并重新启用 AGIC 加载项
部署为 AKS 加载项的应用程序网关入口控制器 (AGIC) 允许你在 Azure CLI 中通过一行命令来启用和禁用该加载项。 禁用 AGIC 加载项时，应用程序网关的生命周期将有所不同，具体取决于应用程序网关是由 AGIC 加载项创建的，还是独立于 AGIC 加载项进行部署的。 如果你禁用了 AGIC 加载项，则可运行相同的命令来重新启用它，或者使用现有的 AKS 群集和应用程序网关来启用它。

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>禁用具有关联的应用程序网关的 AGIC 加载项 
如果在你首次设置所有内容时，AGIC 加载项自动部署了应用程序网关，则禁用 AGIC 加载项默认情况下会根据几个条件删除应用程序网关。 当你禁用 AGIC 加载项时，它将查找两个条件来确定是否应删除关联的应用程序网关：
- 与 AGIC 加载项关联的应用程序网关是否部署在 MC_* 节点资源组中？ 
- 与 AGIC 加载项关联的应用程序网关是否具有“created-by: ingress-appgw”标记？ AGIC 使用该标记来确定应用程序网关是否是由该加载项部署的。 

如果同时满足这两个条件，则 AGIC 加载项在被禁用时将删除其创建的应用程序网关；但是，它不会删除通过其/在其中部署了应用程序网关的公共 IP 或子网。 如果不满足第一个条件，则应用程序网关是否具有“created-by: ingress-appgw”标记将无关紧要 - 禁用该加载项不会删除应用程序网关。 同样，如果不满足第二个条件（即，应用程序网关没有该标记），则禁用加载项不会删除 MC_ * 节点资源组中的应用程序网关。 

> [!TIP] 
> 如果你不希望在禁用该加载项时删除应用程序网关，但它同时满足这两个条件，则请删除“created-by: ingress-appgw”标记，以防加载项删除你的应用程序网关。 

若要禁用 AGIC 加载项，请运行以下命令： 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>在现有的应用程序网关和 AKS 群集上启用 AGIC 加载项
如果你禁用了 AGIC 加载项并需要重新启用该加载项，或者想要使用现有应用程序网关和 AKS 群集启用该加载项，则请运行以下命令：

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>后续步骤
若要更详细地了解如何使用现有应用程序网关和 AKS 群集来启用 AGIC 加载项，请参阅 [AGIC 加载项的“棕色地带”部署](tutorial-ingress-controller-add-on-existing.md)。