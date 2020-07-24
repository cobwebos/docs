---
title: 使用指定的公共 IP 地址创建池
description: 了解如何创建使用自己的公共 IP 地址的 Batch 池。
ms.topic: how-to
ms.date: 07/20/2020
ms.openlocfilehash: 630da3ff9c1f2318c7ed4da0e8f4b5ee8212f389
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023749"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>使用指定的公共 IP 地址创建 Azure Batch 池

创建 Azure Batch 池时，可以在指定的[Azure 虚拟网络（VNet）的子网中预配该池](batch-virtual-network.md)。 批处理池中的虚拟机可通过 Batch 创建的公共 IP 地址进行访问。 这些公共 IP 地址可以在池的生存期内更改，这意味着，如果未刷新 IP 地址，则网络设置可能会过时。

你可以创建静态公共 IP 地址的列表，以用于池中的虚拟机。 这样，你就可以控制公共 IP 地址的列表，并确保它们不会意外更改。 如果你使用的是可限制对特定 IP 地址的访问的任何外部服务（如数据库），这会特别有用。

有关创建没有公共 IP 地址的池的信息，请参阅[创建没有公共 ip 地址的 Azure Batch 池](./batch-pool-no-public-ip-address.md)。

## <a name="prerequisites"></a>先决条件

- “身份验证”。 若要使用公共 IP 地址，Batch 客户端 API 必须使用[Azure Active Directory （AD）身份验证](batch-aad-auth.md)。

- **一个 Azure VNet**。 必须使用在其中创建池的同一 Azure 订阅中的[虚拟网络](batch-virtual-network.md)以及 IP 地址。 仅可使用基于 Azure 资源管理器的 Vnet。 确保 VNet 满足所有[一般要求](batch-virtual-network.md#vnet-requirements)。

- **至少一个 Azure 公共 IP 地址**。 若要创建一个或多个公共 IP 地址，可以使用[Azure 门户](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)、 [Azure 命令行接口（CLI）](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)或[Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress)。 务必遵循下面所列的要求。

> [!NOTE]
> Batch 会自动分配包含公共 IP 地址的资源组中的其他网络资源。 对于每个100专用节点，Batch 通常会分配一个网络安全组（NSG）和一个负载均衡器。 这些资源受订阅的资源配额的限制。 使用较大的池时，可能需要为其中一个或多个资源[请求增加配额](batch-quota-limit.md#increase-a-quota)。

## <a name="public-ip-address-requirements"></a>公共 IP 地址要求

创建公共 IP 地址时，请注意以下要求：

- 公共 IP 地址必须与用于创建池的 Batch 帐户位于同一订阅和区域中。
- **IP 地址分配**必须设置为 "**静态**"。
- **SKU**必须设置为 "**标准**"。
- 必须指定 DNS 名称。
- 公共 IP 地址必须仅用于虚拟机配置池。 其他资源不应使用这些 IP 地址，或者池可能会遇到分配失败的情况。
- 无安全策略或资源锁应限制用户对公共 IP 地址的访问。
- 为池指定的公共 IP 地址数必须足够大，才能容纳为池指定的 Vm 数量。 它必须至少为池的 **targetDedicatedNodes**   和 **targetLowPriorityNodes**   属性的总和。 如果没有足够的 IP 地址，则池将部分分配计算节点，并发生调整大小错误。 目前，Batch 对每个 100 Vm 使用一个公共 IP 地址。
- 始终具有公共 IP 地址的附加缓冲区。 建议至少添加一个额外的公共 IP 地址，或添加到池的总公共 IP 地址的10%，取两者中的较大者。 当向下缩放时，此附加缓冲区可帮助批处理其内部优化，并允许在增加或减少失败后更快速地进行扩展。
- 创建池后，无法添加或更改池使用的公共 IP 地址的列表。 如果需要修改列表，则必须删除该池，然后重新创建它。

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>使用公共 IP 地址创建 Batch 池

下面的示例演示如何使用[Azure Batch 服务 REST API](/rest/api/batchservice/pool/add)创建使用公共 IP 地址的池。

### <a name="batch-service-rest-api"></a>Batch 服务 REST API

REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

请求正文

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "maxTasksPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      }, 
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>后续步骤

- 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)（如池、节点、作业和任务）。
- 了解如何[在 Azure 虚拟网络的子网中创建池](batch-virtual-network.md)。
- 了解如何[创建不带公共 IP 地址的 Azure Batch 池](./batch-pool-no-public-ip-address.md)。

