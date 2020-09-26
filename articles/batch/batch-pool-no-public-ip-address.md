---
title: 创建不具有公共 IP 地址的 Azure Batch 池
description: 了解如何创建不带公共 IP 地址的池
author: pkshultz
ms.topic: how-to
ms.date: 09/25/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 9b36c769c70792e47464c2704e1912dbb2d744dd
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91367931"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>创建不具有公共 IP 地址的 Azure Batch 池

创建 Azure Batch 池时，可以预配没有公共 IP 地址的虚拟机配置池。 本文介绍如何设置没有公共 IP 地址的 Batch 池。

## <a name="why-use-a-pool-without-public-ip-addresses"></a>为什么使用没有公共 IP 地址的池？

默认情况下，将为 Azure Batch 虚拟机配置池中的所有计算节点分配一个公共 IP 地址。 Batch 服务使用此地址来计划任务和与计算节点通信，包括对 internet 的出站访问。

若要限制对这些节点的访问并减少从 internet 的这些节点的发现，可以预配无公共 IP 地址的池。

> [!IMPORTANT]
> 对于在 Azure Batch 中没有公共 IP 地址的池的支持目前为除中国东部、中国东部2、中国北部和中国北部2以外的所有区域公开预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

- “身份验证”。 若要在 [虚拟网络](./batch-virtual-network.md)中使用没有公共 IP 地址的池，Batch 客户端 API 必须使用 AZURE ACTIVE DIRECTORY (AD) 身份验证。 有关 Azure AD 的 Azure Batch 支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。 如果不是在虚拟网络中创建池，则可以使用 Azure AD 身份验证，也可以使用基于密钥的身份验证。

- **一个 Azure VNet**。 如果要在 [虚拟网络](batch-virtual-network.md)中创建池，请遵循以下要求和配置。 若要提前准备具有一个或多个子网的 VNet，可以使用 Azure 门户、Azure PowerShell、Azure 命令行接口 (CLI) 或其他方法。
  - VNet 必须与用于创建池的 Batch 帐户位于同一订阅和区域中。
  - 为池指定的子网必须提供足够的未分配 IP 地址来容纳面向该池的 VM 的数量；即，池的 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 属性的总和。 如果子网没有足够的未分配 IP 地址，池将分配部分计算节点，并发生调整大小错误。
  - 必须禁用专用链接服务和终结点网络策略。 这可以通过使用 Azure CLI 来完成： ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```
  
> [!IMPORTANT]
> 对于每个100专用节点或低优先级节点，Batch 分配一个专用链接服务和一个负载均衡器。 这些资源受订阅的[资源配额](../azure-resource-manager/management/azure-subscription-service-limits.md)限制。 对于较大的池，你可能需要为这些资源中的一个或多个 [请求增加配额](batch-quota-limit.md#increase-a-quota) 。 此外，不应将资源锁应用于由 Batch 创建的任何资源，因为这样可以防止由于用户启动的操作（如删除池或调整为零）而清理资源。

## <a name="current-limitations"></a>当前限制

1. 没有公共 IP 地址的池必须使用虚拟机配置而不是云服务配置。
1. 对于没有公共 IP 地址的池，批处理计算节点的[自定义终结点配置](pool-endpoint-configuration.md)不起作用。
1. 由于没有公共 IP 地址，因此不能 [将自己指定的公共 ip 地址用于](create-pool-public-ip.md) 此类型的池。

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>在 Azure 门户中创建没有公共 IP 地址的池

1. 导航到 Azure 门户中的批处理帐户。 
1. 在左侧的 " **设置** " 窗口中，选择 " **池**"。
1. 在“池”窗口中，选择“添加”。 
1. 在“添加池”窗口中，从“映像类型”下拉列表中选择要使用的选项。 
1. 选择正确的映像 **发布者/产品/服务/Sku** 。
1. 指定剩余的必需设置，包括 **节点大小**、 **目标专用节点**和 **低优先级节点**，以及任何所需的可选设置。
1. 根据需要选择要使用的虚拟网络和子网。 此虚拟网络必须与要创建的池位于同一资源组中。
1. 在 " **IP 地址" 设置类型**中，选择 " **NoPublicIPAddresses**"。

![选择了 NoPublicIPAddresses 的 "添加池" 屏幕屏幕截图。](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>使用批处理 REST API 来创建没有公共 IP 地址的池

下面的示例演示如何使用 [Azure Batch REST API](/rest/api/batchservice/pool/add) 创建使用公共 IP 地址的池。

### <a name="rest-api-uri"></a>REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>请求正文

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "maxTasksPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>对 internet 的出站访问

在没有公共 IP 地址的池中，你的虚拟机将无法访问公共 internet，除非你适当地配置了网络设置，例如使用 [虚拟网络 NAT](../virtual-network/nat-overview.md)。 请注意，NAT 仅允许从虚拟网络中的虚拟机对 internet 进行出站访问。 由于没有关联的公共 IP 地址，因此，批处理创建的计算节点不能公开访问。

提供出站连接的另一种方法是使用用户定义的路由 (UDR) 。 这使你可以将流量路由到具有公共 internet 访问权限的代理计算机。

## <a name="next-steps"></a>后续步骤

- 详细了解如何 [在虚拟网络中创建池](batch-virtual-network.md)。
- 了解如何 [将专用终结点与 Batch 帐户一起使用](private-connectivity.md)。
