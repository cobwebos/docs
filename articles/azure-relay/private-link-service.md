---
title: 将 Azure 中继与 Azure 专用链接服务集成
description: 了解如何将 Azure 中继与 Azure 专用链接服务集成
services: service-bus-relay
author: spelluru
ms.author: spelluru
ms.date: 05/07/2020
ms.service: service-bus-relay
ms.topic: article
ms.openlocfilehash: ad57ea4ddeab2fb2af0da68d199445d9737a67cd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210614"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>将 Azure 中继与 Azure 专用链接集成（预览版）
使用 azure**专用链接服务**，可以通过虚拟网络中的专用终结点访问 azure 服务（例如，azure 中继、Azure 服务总线、Azure 事件中心、azure 存储和 Azure Cosmos DB）以及 azure 托管的客户/合作伙伴服务。 有关详细信息，请参阅[什么是 Azure 专用链接（预览版）？](../private-link/private-link-overview.md)

**专用终结点**是一个网络接口，该接口允许在虚拟网络中运行的工作负荷在具有**专用链接资源**（例如，中继命名空间）的服务中进行私下连接和安全连接。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 到服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute、VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量通过 Microsoft 主干网络进行遍历，从而消除了公共 Internet 的泄露。 可以通过允许连接到特定的 Azure 中继命名空间来提供访问控制的粒度级别。 


> [!IMPORTANT]
> 此功能目前处于**预览阶段**。 
>
> 当前在发送方客户端上支持专用链接连接。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 门户添加专用终结点

### <a name="prerequisites"></a>必备条件
若要将 Azure 中继命名空间与 Azure 专用链接（预览版）集成，你将需要以下实体或权限：

- Azure 中继命名空间。
- 一个 Azure 虚拟网络。
- 虚拟网络中的子网。
- 虚拟网络上的 "所有者" 或 "参与者" 权限。

专用终结点和虚拟网络必须位于同一区域。 使用门户选择专用终结点的区域时，只会自动筛选该区域中的虚拟网络。 命名空间可以在不同的区域中。

专用终结点使用虚拟网络中的专用 IP 地址。

### <a name="steps"></a>步骤
有关创建新的 Azure 中继命名空间和其中的实体的分步说明，请参阅[使用 Azure 门户创建 Azure 中继命名空间](relay-create-namespace-portal.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在搜索栏中，键入**中继**。
3. 从列表中选择要向其添加专用终结点的**命名空间**。
4. 选择 "**设置**" 下的 "**网络**" 选项卡。
5. 选择页面顶部的 "**专用终结点连接（预览）** " 选项卡
6. 选择页面顶部的 " **+ 专用终结点**" 按钮。

    !["添加专用终结点" 按钮](./media/private-link-service/add-private-endpoint-button.png)
7. 在 "**基本**信息" 页上，执行以下步骤： 
    1. 选择要在其中创建专用终结点的**Azure 订阅**。 
    2. 选择专用终结点资源的**资源组**。
    3. 输入专用终结点的**名称**。 
    5. 选择专用终结点的**区域**。 专用终结点必须与虚拟网络位于同一区域中，但可以在与你连接到的 Azure 中继命名空间不同的区域中。 
    6. 选择页面底部的 "**下一步：资源 >** " 按钮。

        ![创建专用终结点-基本页](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 "**资源**" 页上，执行以下步骤：
    1. 对于 "连接方法"，如果在 "我的目录" 中选择了 "**连接到 Azure 资源**"，则拥有对命名空间的 "所有者" 或 "参与者" 访问权限，并且该命名空间与专用终结点在同一目录中 
        1. 选择**Azure 中继命名空间**所在的**azure 订阅**。 
        2. 对于 "**资源类型**"，请为**资源类型**选择 " **Microsoft"。**
        3. 对于 "**资源**"，从下拉列表中选择 "中继命名空间"。 
        4. 确认 "**目标 subresource** " 设置为 "**命名空间**"。
        5. 选择页面底部的 "**下一步：配置 >** " 按钮。 
        
            ![创建专用终结点-资源页](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果选择 "**按资源 ID 或别名连接到 Azure 资源**"，因为命名空间与专用终结点的目录不在同一目录下，请执行以下步骤：
        1. 输入**资源 ID**或**别名**。 它可以是某人与你共享的资源 ID 或别名。 获取资源 ID 的最简单方法是导航到 Azure 门户中的 Azure 中继命名空间，并从中复制 URI 的一部分 `/subscriptions/` 。 下面是一个示例：`/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. 对于**目标子资源**，请输入**命名空间**。 这是专用终结点可以访问的子资源的类型。
        3. 可有可无输入**请求消息**。 资源所有者在管理专用终结点连接时看到此消息。
        4. 然后，选择页面底部的 "**下一步：配置 >** " 按钮。

            ![创建专用终结点-使用资源 ID 进行连接](./media/private-link-service/connect-resource-id.png)
9. 在 "**配置**" 页上，选择要在其中部署专用终结点的虚拟网络中的子网。 
    1. 选择一个**虚拟网络**。 下拉列表中仅列出当前所选订阅和位置中的虚拟网络。 
    2. 选择所选虚拟网络中的**子网**。 
    3. 如果要将专用终结点与专用 DNS 区域集成，请启用**与专用 dns 区域集成**。 
    
        若要以私密方式连接到专用终结点，需有一条 DNS 记录。 建议将专用终结点与**专用 DNS 区域**集成。 你还可以使用自己的 DNS 服务器，或使用虚拟机上的主机文件创建 DNS 记录。 有关详细信息，请参阅[Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。 在此示例中，已选择 "**集成到专用 dns 区域**" 选项，将为你创建一个专用 dns 区域。 
    3. 选择页面底部的 "**下一步：标记 >** " 按钮。 

        ![创建专用终结点-配置页](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 "**标记**" 页上，创建要与专用终结点和专用 DNS 区域关联的任何标记（名称和值）。 然后，选择页面底部的 "**查看 + 创建**" 按钮。 
11. 在 "**查看**" 和 "创建" 中，查看所有设置，并选择 "**创建**" 以创建专用终结点。
    
    ![创建专用终结点-查看和创建页面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 在 "**专用终结点**" 页上，可以查看专用终结点连接的状态。 如果你是中继命名空间的所有者，或对**连接方法**选择了 "**在我的目录中连接到 Azure 资源**" 选项，则应**自动批准**端点连接。 如果它处于 "**挂起**" 状态，请参阅[使用 Azure 门户管理专用终结点](#manage-private-endpoints-using-azure-portal)部分。

    ![专用终结点页](./media/private-link-service/private-endpoint-page.png)
13. 导航回**命名空间**的 "**网络**" 页，并切换到**专用终结点连接（预览）** 选项卡。应会看到已创建的专用终结点。 

    ![已创建专用终结点](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 添加专用终结点
下面的示例演示如何使用 Azure PowerShell 来创建与 Azure 中继命名空间的专用终结点连接。

专用终结点和虚拟网络必须位于同一区域。 你的 Azure 中继命名空间可以在不同的区域中。 而且，专用终结点在虚拟网络中使用专用 IP 地址。

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>使用 Azure 门户管理专用终结点

创建专用终结点时，必须批准连接。 如果你要为其创建专用终结点的资源（中继命名空间）在你的目录中，则可以通过中继命名空间来批准你拥有 "管理" 权限的连接请求。 如果要连接到不具有 "管理" 访问权限的中继命名空间，则必须等待该资源的所有者批准连接请求。

有四种预配状态：

| 服务操作 | 服务使用者专用终结点状态 | 说明 |
|--|--|--|
| 无 | 挂起的 | 将手动创建连接，并等待 Azure 中继命名空间所有者进行审批。 |
| 审批 | 已批准 | 连接已自动或手动批准，可供使用。 |
| 拒绝 | 已拒绝 | Azure 中继命名空间所有者拒绝了连接。 |
| 删除 | 已断开连接 | 连接已被 Azure 中继命名空间所有者删除，专用终结点将变为信息性，应删除以进行清理。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>批准、拒绝或删除专用终结点连接

1. 登录到 Azure 门户。  
1. 在搜索栏中，键入**中继**。
1. 选择要管理的**命名空间**。
1. 选择“网络”选项卡。 
5. 根据你想要执行的操作（"批准"、"拒绝" 或 "删除"），切换到下面的相应部分。 

### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接

1. 如果有任何挂起的连接，你会在预配状态中看到 "**挂起**" 列出的连接。 
2. 选择要批准的**专用终结点**
3. 选择 "**批准**" 按钮。

    ![批准专用终结点](./media/private-link-service/private-endpoint-approve.png)
4. 在 "**批准连接**" 页上，输入可选**注释**，然后选择 **"是"**。 如果选择 "**否**"，则不会执行任何操作。 

    !["批准连接" 页](./media/private-link-service/approve-connection-page.png)
5. 你应在列表中看到连接状态已更改为 "**已批准**"。

### <a name="reject-a-private-endpoint-connection"></a>拒绝专用终结点连接

1. 如果要拒绝的任何专用终结点连接、该连接是挂起的请求还是以前批准的现有连接，请选择终结点连接，然后单击 "**拒绝**" 按钮。

    ![拒绝按钮](./media/private-link-service/private-endpoint-reject.png)
2. 在 "**拒绝连接**" 页上，输入可选注释，然后选择 **"是"**。 如果选择 "**否**"，则不会执行任何操作。 

    ![拒绝连接页](./media/private-link-service/reject-connection-page.png)
3. 应会在列表中已更改的已**拒绝**状态显示连接状态。


### <a name="remove-a-private-endpoint-connection"></a>删除专用终结点连接

1. 若要删除专用终结点连接，请在列表中选择它，然后在工具栏上选择 "**删除**"。 

    ![“删除”按钮](./media/private-link-service/remove-endpoint.png)
2. 在 "**删除连接**" 页上，选择 **"是"** 以确认删除专用终结点。 如果选择 "**否**"，则不会执行任何操作。 

    ![删除连接页](./media/private-link-service/delete-connection-page.png)
3. 你应看到状态已更改为 "已**断开连接**"。 然后，你将看到终结点从列表中消失。 

## <a name="validate-that-the-private-link-connection-works"></a>验证专用链接连接是否有效
应该验证专用终结点的同一子网内的资源是否通过其专用 IP 地址连接到 Azure 中继命名空间。

对于此测试，请按照在[Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤创建虚拟机

在 "**网络**" 选项卡中： 

1. 指定**虚拟网络**和**子网**。 你必须选择在其上部署了专用终结点的虚拟网络。
2. 指定**公共 IP**资源。
3. 对于 " **NIC 网络安全组**"，请选择 "**无**"。
4. 对于 "**负载均衡**"，请选择 "**否**"。

连接到 VM 并打开命令行，并运行以下命令：

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

应会看到如下所示的结果。 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和设计注意事项

### <a name="design-considerations"></a>设计注意事项
- Azure 中继的专用终结点处于**公共预览版**中。 
- 有关定价信息，请参阅 [Azure 专用链接（预览版）定价](https://azure.microsoft.com/pricing/details/private-link/)。

### <a name="limitations"></a>限制 
- 每个 Azure 中继命名空间的专用终结点的最大数目：64。
- 每个订阅的专用终结点的 Azure 中继命名空间的最大数量：64。
- 网络安全组（NSG）规则和用户定义的路由不适用于专用终结点。 有关详细信息，请参阅[Azure Private Link service：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 专用链接（预览版）](../private-link/private-link-service-overview.md)
- 了解有关[Azure 中继](relay-what-is-it.md)的详细信息
