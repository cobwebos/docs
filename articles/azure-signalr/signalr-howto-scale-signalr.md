---
title: 缩放 Azure SignalR 服务的实例
description: 了解如何缩放 Azure SignalR 服务实例，通过 Azure 门户或 Azure CLI 来添加或减少容量。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659281"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>如何缩放 Azure SignalR 服务实例？
本文介绍如何缩放 Azure SignalR 服务的实例。 可通过两种方案进行缩放、纵向扩展和横向扩展。

* [向上缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：获取更多单元、连接、消息等。 可以通过将定价层从 "免费" 更改为 "标准" 进行扩展。
* [横向扩展](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加 SignalR 单位数。 可以向外扩展到多达100个单位。

缩放设置需要几分钟时间。 在极少数情况下，应用可能需要大约30分钟。 它们不需要更改代码或重新部署服务器应用程序。

有关各个 SignalR 服务的定价和容量的信息，请参阅[Azure SignalR 服务定价详细信息](https://azure.microsoft.com/pricing/details/signalr-service/)。  

> [!NOTE]
> 将 SignalR Service 从**免费**层更改为**标准**层，反之亦然，将更改公共服务 IP，并且通常需要30-60 分钟才能将更改传播到整个 internet 上的 DNS 服务器。 在更新 DNS 之前，可能无法访问你的服务。 通常不建议经常更改定价层。


## <a name="scale-on-azure-portal"></a>缩放 Azure 门户

1. 在浏览器中，打开 [Azure 门户](https://portal.azure.com)。

2. 在 SignalR 服务页的左侧菜单中，选择 "**缩放**"。
   
3. 选择定价层，然后单击 "**选择**"。 设置**标准**层的单位计数。
   
    ![在门户中缩放](./media/signalr-howto-scale/signalr-howto-scale.png)

4. 单击“ **保存**”。

## <a name="scale-using-azure-cli"></a>使用 Azure CLI 进行缩放

此脚本创建一个新的 "**免费**" 层的 SignalR 服务资源和一个新的资源组，并将其扩展到 "**标准**" 层。 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

记下为新资源组生成的实际名称。 如果要删除所有组资源，将使用该资源组名称。

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>比较定价层

有关详细信息，如每个定价层的包含消息和连接，请参阅[SignalR Service 定价详细](https://azure.microsoft.com/pricing/details/signalr-service/)信息。

有关每个层中服务限制、配额和约束的表，请参阅[SignalR 服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何缩放单个 SignalR 服务实例。

缩放、分片和跨区域方案也支持多个终结点。

> [!div class="nextstepaction"]
> [缩放具有多个实例的 SignalR 服务](./signalr-howto-scale-multi-instances.md)
