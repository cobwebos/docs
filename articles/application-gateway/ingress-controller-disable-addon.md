---
title: 禁用并重新启用适用于 Azure Kubernetes Service 群集的应用程序网关入口控制器外接程序
description: 本文提供了有关如何禁用和重新启用 AKS 群集的 AGIC 外接程序的信息
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807951"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>禁用并重新启用 AKS 群集的 AGIC 外接程序
部署为 AKS 外接程序的应用程序网关入口控制器（AGIC）使你可以在 Azure CLI 中使用一行来启用和禁用外接程序。 禁用 AGIC 外接程序时，应用程序网关的生命周期将有所不同，具体取决于应用程序网关是由 AGIC 外接程序创建的，还是独立于 AGIC 外接程序进行部署。 如果你禁用了 AGIC 外接程序，或者使用现有的 AKS 群集和应用程序网关启用 AGIC 外接程序，则可以运行相同的命令来重新启用它。

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>禁用关联应用程序网关的 AGIC 外接程序 
如果在首次设置所有内容时，AGIC 外接程序自动部署了应用程序网关，则默认情况下禁用 AGIC 外接程序会根据几个条件删除应用程序网关。 AGIC 外接程序在两个条件中进行查找，以确定在禁用时是否应删除关联的应用程序网关：
- AGIC 加载项是否与 MC_ * 节点资源组中部署的应用程序网关相关联？ 
- AGIC 外接程序的应用程序网关是否与 "创建者： appgw-rg" 标记相关联？ AGIC 使用标记来确定应用程序网关是否是由外接程序部署的。 

如果同时满足这两个条件，AGIC 外接程序将删除在禁用外接程序时创建的应用程序网关;但是，它不会删除在其中部署了应用程序网关的公共 IP 或子网。 如果未满足第一个条件，则如果应用程序网关具有 "创建者： appgw-rg" 标记，则不重要，禁用外接程序不会删除应用程序网关。 同样，如果未满足第二个条件（即，应用程序网关缺少该标记），则禁用外接程序不会删除 MC_ * 节点资源组中的应用程序网关。 

> [!TIP] 
> 如果你不希望在禁用外接程序时删除应用程序网关，但它同时满足这两个条件，请删除 "创建者： appgw-rg" 标记，以防外接程序删除你的应用程序网关。 

若要禁用 AGIC 外接程序，请运行以下命令： 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>在现有应用程序网关和 AKS 群集上启用 AGIC 外接程序
如果你禁用了 AGIC 外接程序并需要重新启用该外接程序，或者想要使用现有应用程序网关和 AKS 群集启用该外接程序，请运行以下命令：

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>后续步骤
有关如何使用现有应用程序网关和 AKS 群集启用 AGIC 外接程序的更多详细信息，请参阅[AGIC 外接程序要重建 mqtt 部署](tutorial-ingress-controller-add-on-existing.md)。