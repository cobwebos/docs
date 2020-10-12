---
title: 创建具有指定公共 IP 地址的池
description: 了解如何创建使用你自己的公共 IP 地址的 Batch 池。
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91849322"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>创建具有指定公共 IP 地址的 Azure Batch 池

创建 Azure Batch 池时，可以在指定的 [Azure 虚拟网络 (VNet) 的子网中预配该池](batch-virtual-network.md)。 可以通过 Batch 创建的公共 IP 地址访问 Batch 池中的虚拟机。 这些公共 IP 地址在池的生存期内可能会发生变化，这意味着，如果未刷新 IP 地址，则网络设置可能会过时。

你可以创建用于池中的虚拟机的静态公共 IP 地址的列表。 这样，你就可以控制公共 IP 地址的列表，确保它们不会意外更改。 如果你在使用仅限特定 IP 地址访问的任何外部服务（如数据库），这会特别有用。

有关创建没有公共 IP 地址的池的信息，请参阅 [创建没有公共 ip 地址的 Azure Batch 池](./batch-pool-no-public-ip-address.md)。

## <a name="prerequisites"></a>先决条件

- “身份验证”。 若要使用公共 IP 地址，Batch 客户端 API 必须使用 [Azure Active Directory (AD) 身份验证](batch-aad-auth.md)。

- **一个 Azure VNet**。 必须使用你在其中创建池 和 IP 地址的同一 Azure 订阅中的[虚拟网络](batch-virtual-network.md)。 只能使用基于 Azure 资源管理器的 VNet。 确保 VNet 满足所有[一般要求](batch-virtual-network.md#vnet-requirements)。

- **至少一个 Azure 公共 IP 地址**。 若要创建一个或多个公共 IP 地址，可以使用 [Azure 门户](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)、[Azure 命令行界面 (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create) 或 [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress)。 请务必遵循下面列出的要求。

> [!NOTE]
> Batch 自动在包含公共 IP 地址的资源组中分配其他网络资源。 通常情况下，每 100 个专用节点 Batch 会分配 1 个网络安全组 (NSG) 和 1 个负载均衡器。 这些资源受订阅的资源配额限制。 当使用较大的池时，可能需要为一个或多个此类资源[请求增加配额](batch-quota-limit.md#increase-a-quota)。

## <a name="public-ip-address-requirements"></a>公共 IP 地址要求

创建公共 IP 地址时，请注意以下要求：

- 公共 IP 地址必须与用于创建池的 Batch 帐户位于同一订阅和区域中。
- “IP 地址分配”必须设置为“静态”。
- “SKU”必须设置为“标准”。
- 必须指定 DNS 名称。
- 公共 IP 地址必须仅用于虚拟机配置池。 其他资源不应使用这些 IP 地址，否则池可能会遇到分配失败的情况。
- 不应有安全策略或资源锁限制用户对公共 IP 地址的访问。
- 为池指定的公共 IP 地址的数量必须足够大，以便容纳为池指定的相应数目的 VM。 该数目必须至少是池的 **targetDedicatedNodes** 和 **targetLowPriorityNodes** 属性之和。 如果没有足够的 IP 地址，池会分配部分计算节点，并会发生重设大小错误。 目前，每 100 个 VM Batch 会使用一个公共 IP 地址。
- 请始终留出额外的公共 IP 地址以便进行缓冲。 建议至少添加一个额外的公共 IP 地址，或者，额外添加的公共 IP 地址数是添加到池的公共 IP 地址总数的 10%，取两者中的较大者。 此额外的缓冲将有助于 Batch 在纵向缩减时进行内部优化，并允许在纵向扩展或缩减失败后更快地进行纵向扩展。
- 创建池后，无法添加或更改池使用的公共 IP 地址的列表。 如果需要修改列表，则必须删除该池，然后重新创建它。

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>创建具有公共 IP 地址的 Batch 池

下面的示例展示了如何使用 [Azure Batch 服务 REST API](/rest/api/batchservice/pool/add) 来创建使用公共 IP 地址的池。

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
      "taskSlotsPerNode":3,
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
- 了解如何 [创建不带公共 IP 地址的 Azure Batch 池](./batch-pool-no-public-ip-address.md)。

