---
title: 配置 ExpressRoute Global Reach：Azure CLI | Microsoft Docs
description: 本文介绍了如何将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络并启用 Global Reach。
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 89ada41c5f3c9cf1ca7a2ac707363f57080c361d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869969"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>使用 Azure CLI 配置 ExpressRoute 全球覆盖

本文帮助你使用 Azure CLI 配置 Azure ExpressRoute Global Reach。 有关详细信息，请参阅 [ExpressRoute Global Reach](expressroute-global-reach.md)。
 
在开始配置前，请满足以下要求：

* 安装最新版本的 Azure CLI。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli) 和 [Azure CLI 入门](/cli/azure/get-started-with-azure-cli)。
* 了解 ExpressRoute 线路预配[工作流](expressroute-workflows.md)。
* 确保 ExpressRoute 线路处于“已预配”状态。
* 确保在 ExpressRoute 线路上配置了 Azure 专用对等互连。  

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

若要开始配置，请登录到 Azure 帐户。 以下命令会打开默认浏览器并提示输入 Azure 帐户的登录凭据：  

```azurecli
az login
```

如果有多个 Azure 订阅，请查看该帐户的订阅：

```azurecli
az account list
```

指定要使用的订阅：

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>确定用于配置的 ExpressRoute 线路

您可以启用 ExpressRoute 全球覆盖之间任何两个 ExpressRoute 线路，只要它们位于支持的国家/地区和不同对等互连位置创建。 如果你的订阅同时拥有这两条线路，则可以选择其中任一条线路运行配置，如本文稍后所述。 如果两条线路位于不同的 Azure 订阅中，则必须拥有来自一个 Azure 订阅的授权，并且在另一个 Azure 订阅中运行配置命令时必须传入授权密钥。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>启用本地网络之间的连接

运行该命令以启用连接时，请注意参数值的以下要求：

* *peer-circuit* 应当是完整的资源 ID。 例如：

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *address-prefix* 必须是一个“/29”IPv4 子网（例如，“10.0.0.0/29”）。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 不得在 Azure 虚拟网络或本地网络中使用此子网中的地址。

运行以下 CLI 命令来连接两条 ExpressRoute 线路：

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI 输出如下所示：

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

此操作完成后，将通过两条 ExpressRoute 线路在两端的本地网络之间建立连接。

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>在不同 Azure 订阅中启用 ExpressRoute 线路之间的连接

如果两条线路不在同一 Azure 订阅中，则需要获得授权。 在下面的配置中，将在线路 2 的订阅中生成授权并将授权密钥传递给线路 1。

1. 生成授权密钥：

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLI 输出如下所示：

   ```azurecli
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. 记录线路 2 的资源 ID 和授权密钥。

1. 针对线路 1 运行以下命令，传入线路 2 的资源 ID 和授权密钥：

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

此操作完成后，将通过两条 ExpressRoute 线路在两端的本地网络之间建立连接。

## <a name="get-and-verify-the-configuration"></a>获取并验证配置

使用以下命令验证在其中进行了配置的线路（上例中的线路 1）的配置：

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

在 CLI 输出中，你将看到 *CircuitConnectionStatus*。 它将指示两条线路之间的连接是已建立（“Connected”）还是未建立（“Disconnected”）。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>禁用本地网络之间的连接

若要禁用连接，请针对在其中进行了配置的线路（上例中的线路 1）运行以下命令。

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

使用 ```show``` 命令验证状态。

此操作完成后，本地网络之间将不再具有通过 ExpressRoute 线路建立的连接。

## <a name="next-steps"></a>后续步骤

* [详细了解 ExpressRoute Global Reach](expressroute-global-reach.md)
* [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
* [将 ExpressRoute 线路链接到虚拟网络](expressroute-howto-linkvnet-arm.md)
