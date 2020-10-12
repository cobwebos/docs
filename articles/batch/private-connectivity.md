---
title: 对 Azure Batch 帐户使用专用终结点
description: 了解如何使用专用终结点将专用连接到 Azure Batch 帐户。
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7dba3dd1d34421666821c6bc7320ef76ab77bb7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542132"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>对 Azure Batch 帐户使用专用终结点

默认情况下， [Azure Batch 帐户](accounts.md) 具有一个公共终结点，并且可公开访问。 Batch 服务提供创建专用批处理帐户的功能，禁用公共网络访问。

通过使用 [Azure 私有链接](../private-link/private-link-overview.md)，你可以通过 [专用终结点](../private-link/private-endpoint-overview.md)连接到 Azure Batch 帐户。 专用终结点是虚拟网络中某个子网内的一组专用 IP 地址。 然后，可以通过专用 IP 地址限制对 Azure Batch 帐户的访问。

专用链接允许用户从虚拟网络或任何对等互连的虚拟网络中访问 Azure Batch 帐户。 映射到专用链接的资源也可通过 VPN 或 [Azure ExpressRoute](../expressroute/expressroute-introduction.md)通过专用对等互连在本地访问。 你可以使用 " [自动" 或 "手动" 审批方法](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)连接到使用 "专用" 链接配置的 Azure Batch 帐户。

> [!IMPORTANT]
> 除了德国中部和德国东北部之外的所有公共区域，Azure Batch 中的专用连接支持目前均可供使用。

本文介绍了创建专用批处理帐户并使用专用终结点访问该帐户的步骤。

## <a name="azure-portal"></a>Azure 门户

使用以下步骤创建使用 Azure 门户的专用批处理帐户：

1. 从 " **创建资源** " 窗格中，选择 " **Batch 服务** "，然后选择 " **创建**"。
2. 在 " **基本** 信息" 选项卡中输入订阅、资源组、区域和 Batch 帐户名称，然后选择 " **下一步：高级**"。
3. 在 " **高级** " 选项卡中，将 " **公用网络访问** " 设置为 " **已禁用**"
4. 在 " **设置**" 中选择 " **专用终结点连接** "，然后选择 " **+ 专用终结点**"。
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="专用终结点连接&quot;:::
5. 在 &quot; **基本** 信息&quot; 窗格中，输入或选择订阅、资源组、专用终结点资源名称和区域详细信息，然后选择 &quot; **下一步：资源**&quot;。
6. 在 **资源** 窗格中，将 **资源类型** 设置为 **Microsoft.Batch/batchAccounts**。 选择要访问的专用批处理帐户，然后选择 " **下一步：配置**"。
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="专用终结点连接&quot;:::
5. 在 &quot; **基本** 信息&quot; 窗格中，输入或选择订阅、资源组、专用终结点资源名称和区域详细信息，然后选择 &quot; **下一步：资源**&quot;。
6. 在 **资源** 窗格中，将 **资源类型** 设置为 **Microsoft.Batch/batchAccounts**。 选择要访问的专用批处理帐户，然后选择 " **创建**"，然后等待 Azure 验证你的配置。
9. 看到“验证通过”消息时，选择“创建” 。

预配专用终结点后，可以使用专用终结点从同一虚拟网络中的 Vm 访问 Batch 帐户。 若要查看 Azure 门户中的 IP 地址：

1. 选择“所有资源”，
2. 搜索前面创建的专用终结点。
3. 选择“概览”选项卡，查看 DNS 设置和 IP 地址。

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="专用终结点连接&quot;:::
5. 在 &quot; **基本** 信息&quot; 窗格中，输入或选择订阅、资源组、专用终结点资源名称和区域详细信息，然后选择 &quot; **下一步：资源**&quot;。
6. 在 **资源** 窗格中，将 **资源类型** 设置为 **Microsoft.Batch/batchAccounts**。 选择要访问的专用批处理帐户，然后选择 ":::

## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

[使用 Azure 资源管理器模板创建 Batch 帐户](quick-create-template.md)时，请修改模板，将**publicNetworkAccess**设置为 "**已禁用**"，如下所示。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>配置 DNS 区域

使用在其中创建了专用终结点的子网中的 [专用 DNS 区域](../dns/private-dns-privatednszone.md) 。 配置终结点，以便将每个专用 IP 地址映射到某个 DNS 条目。

创建专用终结点时，可以将其与 Azure 中的 [专用 DNS 区域](../dns/private-dns-privatednszone.md) 集成。 如果选择改为使用 [自](../dns/dns-custom-domain.md)定义域，则必须将其配置为为专用终结点保留的所有专用 IP 地址添加 DNS 记录。

## <a name="pricing"></a>定价

有关与专用终结点相关的成本的详细信息，请参阅 [Azure Private Link 定价](https://azure.microsoft.com/pricing/details/private-link/)。

## <a name="current-limitations-and-best-practices"></a>当前限制和最佳实践

创建专用批处理帐户时，请记住以下事项：

- 必须在与 Batch 帐户相同的订阅中创建专用终结点资源。
- 若要删除专用连接，必须删除专用终结点资源。
- 使用公共网络访问权限创建 Batch 帐户后，无法将其更改为仅限专用访问。
- 当你删除专用终结点或从批处理帐户中删除某个区域时，专用 DNS 区域中的 DNS 记录不会自动删除。 在添加链接到此专用 DNS 区域的新专用终结点之前，必须手动删除 DNS 记录。 如果不清理 DNS 记录，则可能会发生意外的数据平面问题，例如在删除私有终结点或删除区域后添加到区域的数据中断。

## <a name="next-steps"></a>后续步骤

- 了解如何 [在虚拟网络中创建批处理池](batch-virtual-network.md)。
- 了解如何 [创建不带公共 IP 地址的 Batch 池](batch-pool-no-public-ip-address.md)
- 了解如何 [创建具有指定公共 IP 地址的批处理池](create-pool-public-ip.md)。
- 了解 [Azure 专用链接](../private-link/private-link-overview.md)。
