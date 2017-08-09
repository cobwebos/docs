---
title: "创建和修改 Azure ExpressRoute 线路：CLI | Microsoft Docs"
description: "本文介绍如何使用 CLI 创建、预配、验证、更新、删除和取消预配 ExpressRoute 线路。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: anzaman;cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 1a1c9a96b772868e2c832e9ff57874038c0db2d4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>使用 CLI 创建和修改 ExpressRoute 线路


本文介绍如何使用命令行接口 (CLI) 创建 Azure ExpressRoute 线路。 本文还介绍如何检查状态，以及如何更新、删除和取消预配线路。 如果想使用不同的方法处理 ExpressRoute 线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [视频 - Azure 门户](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>开始之前

* 在开始之前，请安装最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli) 和 [Azure CLI 2.0 入门](/cli/azure/get-started-with-azure-cli)。
* 在开始配置之前，请查看[先决条件](expressroute-prerequisites.md)和[工作流](expressroute-workflows.md)。

## <a name="create-and-provision-an-expressroute-circuit"></a>创建和预配 ExpressRoute 线路

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1.登录到 Azure 帐户，然后选择订阅

要开始配置，请登录到 Azure 帐户。 使用以下示例来帮助连接：

```azurecli
az login
```

检查该帐户的订阅。

```azurecli
az account list
```

选择要为其创建 ExpressRoute 线路的订阅。

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2.获取支持的提供商、位置和带宽的列表

在创建 ExpressRoute 线路之前，需要支持的连接服务提供商、位置和带宽选项的列表。 CLI 命令“az network express-route list-service-providers”会返回此信息，在随后的步骤中将使用此信息：

```azurecli
az network express-route list-service-providers
```

响应类似于以下示例：

```azurecli
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

查看此响应以检查你的连接服务提供商是否已在此处列出。 请记下以下信息，稍后在创建线路时需要用到：

* 名称
* PeeringLocations
* BandwidthsOffered

现在，已准备好创建 ExpressRoute 线路。

### <a name="3-create-an-expressroute-circuit"></a>3.创建 ExpressRoute 线路

> [!IMPORTANT]
> 从发布服务密钥的那一刻起，将对 ExpressRoute 线路进行计费。 在连接服务提供商准备好预配线路后，请执行此操作。
> 
> 

如果尚未有资源组，则在创建 ExpressRoute 线路之前，必须先创建一个资源组。 可以运行以下命令来创建资源组：

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

以下示例演示如何通过硅谷中的 Equinix 创建 200-Mbps 的 ExpressRoute 线路。 如果使用的是其他提供商和其他设置，请在发出请求时替换该信息。 

请确保指定合适的 SKU 层和 SKU 系列：

* SKU 层决定是否启用 ExpressRoute 标准版或 ExpressRoute 高级版外接程序。 可以指定“Standard”以获取标准 SKU，或指定“Premium”以获取高级版外接程序。
* SKU 系列确定计费类型。 可以指定“Metereddata”以获取数据流量套餐，指定“Unlimiteddata”以获取无限制流量套餐。 可以将计费类型从“Metereddata”更改为“Unlimiteddata”，但不能将类型从“Unlimiteddata”更改为“Metereddata”。


从发布服务密钥的那一刻起，将对 ExpressRoute 线路进行计费。 以下是请求新的服务密钥的示例：

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

响应将包含服务密钥。

### <a name="4-list-all-expressroute-circuits"></a>4.列出所有 ExpressRoute 线路

若要获取已创建的所有 ExpressRoute 线路的列表，请运行“az network express-route list”命令。 可以随时使用此命令检索此信息。 若要列出所有线路，请进行不带任何参数的调用。

```azurecli
az network express-route list
```

服务密钥会在响应的 ServiceKey 字段中列出。

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

可以通过使用“-h”参数运行以下命令获取所有参数的详细说明。

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5.将服务密钥发送给连接服务提供商进行预配

“ServiceProviderProvisioningState”提供有关服务提供商端当前预配状态的信息。 此状态提供 Microsoft 端的状态。 有关详细信息，请参阅[工作流文章](expressroute-workflows.md#expressroute-circuit-provisioning-states)。

创建新的 ExpressRoute 线路时，线路将为以下状态：

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

当连接服务提供商正在为你启用线路时，线路将更改为以下状态：

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

只有 ExpressRoute 线路处于以下状态时，才能使用它。

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6.定期检查线路密钥的状态

检查线路密钥的状态，可以通过此状态了解提供商何时启用了线路。 配置线路后，“ServiceProviderProvisioningState” 会显示为“Provisioned”，如以下示例所示：

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

响应类似于以下示例：

```azurecli
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7.创建路由配置

有关分步说明，请参阅 [ExpressRoute 线路路由配置](howto-routing-cli.md)一文，了解如何创建和修改线路对等互连。

> [!IMPORTANT]
> 这些说明只适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果服务提供商提供第 3 层托管服务（通常是 IP VPN，如 MPLS），则连接服务提供商将为你配置和管理路由。
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8.将虚拟网络链接到 ExpressRoute 线路

接下来，将虚拟网络链接到 ExpressRoute 线路。 按照[将虚拟网络链接到 ExpressRoute 线路](howto-linkvnet-cli.md)文章中的内容进行操作。

## <a name="modify"></a>修改 ExpressRoute 线路

可以在不影响连接的情况下修改 ExpressRoute 线路的某些属性。 可以在不停机的情况下进行以下更改：

* 为 ExpressRoute 线路启用或禁用 ExpressRoute 高级版外接程序。
* 增加 ExpressRoute 线路的带宽，前提是端口上有可用容量。 但不支持对线路的带宽进行降级。 
* 将计量套餐从数据流量套餐更改为无限制流量套餐。 但不支持将计量套餐从无限制流量套餐更改为数据流量套餐。
* 可以启用和禁用允许经典操作。

有关限制和局限性的详细信息，请参阅 [ExpressRoute 常见问题解答](expressroute-faqs.md)。

### <a name="to-enable-the-expressroute-premium-add-on"></a>启用 ExpressRoute 高级版外接程序

可以使用以下命令为现有线路启用 ExpressRoute 高级版外接程序：

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

线路现已启用 ExpressRoute 高级版外接程序功能。 该命令成功运行后，我们就会开始对高级版外接程序功能进行计费。

### <a name="to-disable-the-expressroute-premium-add-on"></a>禁用 ExpressRoute 高级版外接程序

> [!IMPORTANT]
> 如果使用的资源超出了标准线路允许的范围，此操作可能会失败。
> 
> 

在禁用 ExpressRoute 高级版外接程序之前，请了解以下条件：

* 从高级版降级到标准版之前，必须确保链接到线路的虚拟网络数量少于 10。 如果超过 10 个，更新请求则会失败，并且我们将按高级版费率进行收费。
* 必须取消其他地理政治区域的所有虚拟网络的链接。 如果没有对所有虚拟网络取消链接，更新请求则会失败，并且我们将按高级版费率进行收费。
* 路由表中专用对等互连的路由必须少于 4,000。 如果你的路由表大小大于 4,000 路由，BGP 会话则会掉线。 在播发的前缀数量小于 4,000 之前，不会重新启用此会话。

可以使用以下示例为现有线路禁用 ExpressRoute 高级版外接程序：

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>更新 ExpressRoute 线路带宽

有关提供商支持的带宽选项，请查看 [ExpressRoute 常见问题解答](expressroute-faqs.md)。 可以选取大于现有线路大小的任何大小。

> [!IMPORTANT]
> 如果现有端口上的容量不足，则可能需要重新创建 ExpressRoute 线路。 如果该位置没有额外的可用容量，则不能升级线路。
>
> 但是，无法在不中断的情况下降低 ExpressRoute 线路的带宽。 带宽降级要求取消对 ExpressRoute 线路的预配，并重新预配新的 ExpressRoute 线路。
>

确定所需的大小后，可以使用以下命令调整线路的大小：

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

线路的大小会在 Microsoft 端进行调整。 接着，必须联系你的连接服务提供商，让他们在那一端根据此更改更新配置。 在发出此通知后，我们将开始对更新后的带宽选项进行计费。

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>将 SKU 从按流量计费转为不受限制

可以通过使用以下示例更改 ExpressRoute 线路的 SKU：

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>控制对经典环境和 Resource Manager 环境的访问

查看[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-howto-move-arm.md)中的说明。

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>取消预配和删除 ExpressRoute 线路

若要取消预配并删除 ExpressRoute 线路，请确保已了解以下条件：

* 必须取消所有虚拟网络与 ExpressRoute 线路的链接。 如果此操作失败，请查看是否有虚拟网络链接到了该线路。
* 如果 ExpressRoute 线路服务提供商预配状态为“正在预配”或“已预配”，则必须与服务提供商合作，在他们那一端取消预配线路。 在服务提供商取消对线路的预配并通知我们之前，我们会继续保留资源并向你收费。
* 如果服务提供商已取消预配线路，则可以删除此线路。 取消预配线路后，服务提供商预配状态将被设置为“未预配”。 这样就会停止对线路的计费。

可以通过运行以下命令来删除 ExpressRoute 线路：

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>后续步骤

创建线路后，请务必执行以下任务：

* [创建和修改 ExpressRoute 线路的路由](howto-routing-cli.md)
* [将虚拟网络链接到 ExpressRoute 线路](howto-linkvnet-cli.md)
