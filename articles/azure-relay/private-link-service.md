---
title: 将 Azure 中继与 Azure 专用链接服务集成
description: 了解如何将 Azure 中继与 Azure 专用链接服务集成
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 10d82fe8e272ed18dcc339830dfef0f71d4b2ddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263826"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>与 Azure 中继与 Azure 专用链接（预览版）集成
使用 Azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如，Azure 中继、Azure 服务总线、Azure 事件中心、Azure 存储和 Azure Cosmos DB）以及 Azure 托管的客户/合作伙伴服务。 有关详细信息，请参阅[什么是 Azure 专用链接（预览版）？](../private-link/private-link-overview.md)

专用终结点是一个网络接口，它允许在虚拟网络中运行的工作负荷以私密方式安全地连接到具有专用链接资源的服务（例如，中继命名空间）。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute、VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以通过允许连接到特定的 Azure 中继命名空间来提供访问控制的粒度级别。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 门户添加专用终结点

### <a name="prerequisites"></a>先决条件
若要将 Azure 中继命名空间与 Azure 专用链接（预览版）集成，需要以下实体或权限：

- 一个 Azure 中继命名空间。
- 一个 Azure 虚拟网络。
- 虚拟网络中的子网。
- 虚拟网络的所有者或参与者权限。

专用终结点和虚拟网络必须位于同一区域。 使用门户选择专用终结点的区域时，只会自动筛选该区域中的虚拟网络。 命名空间可以位于不同的区域中。

专用终结点使用虚拟网络中的专用 IP 地址。

### <a name="steps"></a>步骤
有关创建新的 Azure 中继命名空间并在其中创建实体的分步说明，请参阅 [使用 Azure 门户创建 Azure 中继命名空间](relay-create-namespace-portal.md)。

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在搜索栏中，键入“中继”。
3. 从列表中选择要将专用终结点添加到的命名空间。
4. 在“设置”下选择“网络”选项卡。 
5. 在页面顶部选择“专用终结点连接(预览版)”选项卡
6. 在页面顶部选择“+ 专用终结点”按钮。

    ![“添加专用终结点”按钮](./media/private-link-service/add-private-endpoint-button.png)
7. 在“基本信息”页上执行以下步骤： 
    1. 选择要在其中创建专用终结点的 Azure 订阅。 
    2. 选择专用终结点资源的资源组。
    3. 输入专用终结点的名称。 
    5. 选择专用终结点的区域。 专用终结点必须与虚拟网络位于同一区域，但可以与要连接的 Azure 中继命名空间不在同一区域。 
    6. 在页面底部选择“下一步:资源 >”按钮。

        ![创建专用终结点 -“基本信息”页](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在“资源”页上执行以下步骤：
    1. 对于连接方法，如果选择了“连接到我的目录中的 Azure 资源”，你拥有对命名空间的所有者或参与者访问权限，并且该命名空间与专用终结点在同一目录中，请执行以下步骤： 
        1. 选择你的 Azure 中继命名空间所在的 Azure 订阅 。 
        2. 对于“资源类型”，请选择“Microsoft.Relay/namespaces”。  
        3. 对于“资源”，请从下拉列表中选择一个中继命名空间。 
        4. 确认“目标子资源”设置为“命名空间” 。
        5. 在页面底部选择“下一步:配置 >”按钮。 
        
            ![创建专用终结点 -“资源”页](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果选择“按资源 ID 或别名连接到 Azure 资源”，因为该命名空间与专用终结点的命名空间不在同一目录中，请执行以下步骤：
        1. 输入资源 ID 或别名 。 该值可以是其他人与你共享的资源 ID 或别名。 获取资源 ID 的最简单方法是在 Azure 门户中导航到 Azure 中继命名空间，然后复制 URI 中从 `/subscriptions/` 开始部分。 示例如下：`/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. 对于“目标子资源”，请输入“命名空间” 。 它是专用终结点可以访问的子资源类型。
        3. （可选）输入一条请求消息。 资源所有者在管理专用终结点连接时会看到此消息。
        4. 然后选择“下一步:配置 >”按钮。

            ![创建专用终结点 - 使用资源 ID 进行连接](./media/private-link-service/connect-resource-id.png)
9. 在“配置”页上，选择要在其中部署专用终结点的虚拟网络中的子网。 
    1. 选择一个虚拟网络。 下拉列表中仅列出了当前所选订阅和位置中的虚拟网络。 
    2. 在所选的虚拟网络中选择一个子网。 
    3. 如果要将专用终结点与专用 DNS 区域集成，请启用“与专用 DNS 区域集成”。 
    
        若要与专用终结点建立专用连接，则需要 DNS 记录。 建议将专用终结点与专用 DNS 区域集成。 也可使用自己的 DNS 服务器，或使用虚拟机上的主机文件来创建 DNS 记录。 有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。 此示例选择了“与专用 DNS 区域集成”选项，因此将为你创建一个专用 DNS 区域。 
    3. 在页面底部选择“下一步:标记 >”按钮。 

        ![创建专用终结点 -“配置”页](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在“标记”页上，创建要与专用终结点关联的任何标记（名称和值）以及专用 DNS 区域（如果已启用此选项）。 然后，在页面底部选择“查看 + 创建”按钮。 
11. 在“查看 + 创建”页上查看所有设置，然后选择“创建”以创建专用终结点 。
    
    ![创建专用终结点 -“查看 + 创建”页](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 在“专用终结点”页上，可以查看专用终结点连接的状态。 如果你是中继命名空间的所有者或者对其具有管理权限，并且对于“连接方法”已选择“连接到我的目录中的 Azure 资源”选项，则终结点连接的状态应该为“已自动批准”  。 如果它处于“挂起”状态，请参阅[使用 Azure 门户管理专用终结点](#manage-private-endpoints-using-azure-portal)部分。

    ![“专用终结点”页](./media/private-link-service/private-endpoint-page.png)
13. 向后导航到命名空间的“网络”页，并切换到“专用终结点连接(预览版)”选项卡  。你应该会看到你所创建的专用终结点。 

    ![专用终结点已创建](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 添加专用终结点
以下示例演示如何使用 Azure PowerShell 创建与 Azure 中继命名空间的专用终结点连接。

专用终结点和虚拟网络必须位于同一区域。 Azure 中继命名空间可以位于不同的区域中。 并且，专用终结点使用虚拟网络中的专用 IP 地址。

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

创建专用终结点时，必须批准连接。 如果要为其创建专用终结点的资源（中继命名空间）位于你的目录中，那么在你对中继命名空间具有管理权限的前提下，你可以批准连接请求。 如果要连接到一个你无权访问的中继命名空间，则必须等待该资源的所有者批准你的连接请求。

有四种预配状态：

| 服务操作 | 服务使用者专用终结点状态 | 说明 |
|--|--|--|
| 无 | 挂起的 | 连接是手动创建的，正等待 Azure 中继命名空间所有者的批准。 |
| 审批 | 已批准 | 连接已自动或手动批准，随时可供使用。 |
| 拒绝 | 已拒绝 | Azure 中继命名空间所有者拒绝了连接。 |
| 删除 | 已断开连接 | 连接已被 Azure 中继命名空间所有者删除，专用终结点仅供参考，应将其删除以清理资源。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>批准、拒绝或删除专用终结点连接

1. 登录到 Azure 门户。
1. 在搜索栏中，键入“中继”。
1. 选择要管理的命名空间。
1. 选择“网络”选项卡。
5. 根据要执行的操作（批准、拒绝或删除），转到下面的相应部分。 

### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接

1. 如果有任何挂起的连接，则会看到预配状态为“挂起”的连接被列出。 
2. 选择要批准的专用终结点
3. 选择“批准”按钮。

    ![批准专用终结点](./media/private-link-service/private-endpoint-approve.png)
4. 在“批准连接”页上输入可选注释，然后选择“是”  。 如果选择“否”，则不会执行任何操作。 

    ![“批准连接”页](./media/private-link-service/approve-connection-page.png)
5. 应会看到，列表中连接的状态已更改为“已批准”。

### <a name="reject-a-private-endpoint-connection"></a>拒绝专用终结点连接

1. 如果要拒绝任何专用终结点连接（不管是挂起的请求还是已批准的现有连接），请选择该终结点连接并单击“拒绝”按钮。

    ![“拒绝”按钮](./media/private-link-service/private-endpoint-reject.png)
2. 在“拒绝连接”页上输入可选注释，然后选择“是” 。 如果选择“否”，则不会执行任何操作。 

    ![“拒绝连接”页](./media/private-link-service/reject-connection-page.png)
3. 应会看到，列表中连接的状态已更改为“已拒绝”。


### <a name="remove-a-private-endpoint-connection"></a>删除专用终结点连接

1. 若要删除某个专用终结点连接，请在列表中选择它，然后在工具栏上选择“删除”。 

    ![“删除”按钮](./media/private-link-service/remove-endpoint.png)
2. 在“删除连接”页上，选择“是”以确认删除该专用终结点 。 如果选择“否”，则不会执行任何操作。 

    ![“删除连接”页](./media/private-link-service/delete-connection-page.png)
3. 应会看到，状态已更改为“已断开连接”。 然后你会发现，该终结点已在列表中消失。 

## <a name="validate-that-the-private-link-connection-works"></a>验证专用链接连接是否有效
应该验证专用终结点的虚拟网络中的资源是否通过其专用 IP 地址连接到 Azure 中继命名空间。

为了进行此测试，请按照[在 Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤，创建一个虚拟机

在“网络”选项卡中： 

1. 指定虚拟网络和子网 。 必须选择已将专用终结点部署到的虚拟网络。
2. 指定一个公共 IP 资源。
3. 对于“NIC 网络安全组”，请选择“无” 。
4. 对于“负载均衡”，请选择“否” 。

连接到 VM，打开命令行并运行以下命令：

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

你应该会看到如下所示的结果。 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和设计注意事项

### <a name="design-considerations"></a>设计注意事项
- Azure 中继的专用终结点目前提供公共预览版。 
- 有关定价信息，请参阅 [Azure 专用链接（预览版）定价](https://azure.microsoft.com/pricing/details/private-link/)。

### <a name="limitations"></a>限制 
- 每个 Azure 中继命名空间的最大专用终结点数目：64。
- 每个订阅的包含专用终结点的 Azure 中继命名空间数目上限：64。
- 网络安全组 (NSG) 规则和用户定义的路由不适用于专用终结点。 有关详细信息，请参阅 [Azure 专用链接服务：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 专用链接（预览版）](../private-link/private-link-service-overview.md)
- 详细了解 [Azure 中继](relay-what-is-it.md)
