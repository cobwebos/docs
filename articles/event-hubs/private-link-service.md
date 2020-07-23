---
title: 将 Azure 事件中心与 Azure 专用链接服务集成
description: 了解如何将 Azure 事件中心与 Azure 专用链接服务集成
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: bfed3f8e4c19463e10b721006d742726cf916900
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512243"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link"></a>将 Azure 事件中心与 Azure 专用链接集成
使用 Azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如 Azure 事件中心、Azure 存储和 Azure Cosmos DB）以及 Azure 托管的客户服务/合作伙伴服务。

专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 专用终结点使用虚拟网络中的专用 IP 地址，从而将该服务有效地引入到虚拟网络。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[什么是 Azure 专用链接？](../private-link/private-link-overview.md)

> [!IMPORTANT]
> **标准**层和**专用**层都支持此功能。 

>[!WARNING]
> 启用专用终结点可以防止其他 Azure 服务与事件中心交互。
>
> 使用虚拟网络时，受信任的 Microsoft 服务不受支持。
>
> 不适用于虚拟网络常见 Azure 方案（请注意，该列表内容并不详尽）-
> - Azure Monitor（诊断设置）
> - Azure 流分析
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
>
> 以下 Microsoft 服务必须在虚拟网络中
> - Azure Web 应用
> - Azure Functions

## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 门户添加专用终结点

### <a name="prerequisites"></a>先决条件

若要将事件中心命名空间与 Azure 专用链接集成，需要以下实体或权限：

- 事件中心命名空间。
- 一个 Azure 虚拟网络。
- 虚拟网络中的子网。
- 对命名空间和虚拟网络拥有所有者或参与者权限。

专用终结点和虚拟网络必须位于同一区域。 使用门户选择专用终结点的区域时，只会自动筛选该区域中的虚拟网络。 命名空间可以位于不同的区域中。

专用终结点使用虚拟网络中的专用 IP 地址。

### <a name="steps"></a>步骤
如果已有事件中心命名空间，可以执行以下步骤创建专用链接连接：

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在搜索栏中键入“事件中心”。
3. 从列表中选择要将专用终结点添加到的**命名空间**。
4. 在“设置”下选择“网络”选项卡。 

    > [!NOTE]
    > 只会看到**标准**命名空间或**专用**命名空间的 "**网络**" 选项卡。 
1. 选择页面顶部的“专用终结点连接”选项卡。 
1. 在页面顶部选择“+ 专用终结点”按钮。

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text=""网络" 页-专用终结点连接选项卡-添加专用终结点链接":::
7. 在“基本信息”页上执行以下步骤： 
    1. 选择要在其中创建专用终结点的 Azure 订阅。 
    2. 选择专用终结点资源的资源组。
    3. 输入专用终结点的名称。 
    5. 专用终结点选择“区域”。 专用终结点必须与虚拟网络位于同一区域，但可以与要连接的专用链接资源位于不同的区域。 
    6. 在完成时选择“下一步:资源 >”按钮，它位于页面底部。

        ![创建专用终结点 -“基本信息”页](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在“资源”页上执行以下步骤：
    1. 对于连接方法，如果选择了“连接到我的目录中的 Azure 资源”，请执行以下步骤： 
        1. 选择你的**事件中心命名空间**所在的 **Azure 订阅**。 
        2. 对于“资源类型”，请选择“Microsoft.EventHub/namespaces”。  
        3. 对于“资源”，请从下拉列表中选择一个事件中心命名空间。 
        4. 确认“目标子资源”设置为“命名空间”。 
        5. 在页面底部选择“下一步:配置 >”按钮。 
        
            ![创建专用终结点 -“资源”页](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果选择了“按资源 ID 或别名连接到 Azure 资源”，请执行以下步骤：
        1. 输入“资源 ID”或“别名”。  该值可以是某人已与你共享的资源 ID 或别名。 获取资源 ID 的最简单方法是在 Azure 门户中导航到“事件中心”命名空间，然后复制从 `/subscriptions/` 开始的 URI 部分。 参阅下图中的示例。 
        2. 对于“目标子资源”，请输入“命名空间”。  它是专用终结点可以访问的子资源类型。
        3. （可选）输入一条请求消息。 资源所有者在管理专用终结点连接时会看到此消息。
        4. 然后选择“下一步:配置 >”按钮。

            ![创建专用终结点 - 使用资源 ID 进行连接](./media/private-link-service/connect-resource-id.png)
9. 在“配置”页上，选择要在其中部署专用终结点的虚拟网络中的子网。 
    1. 选择一个虚拟网络。 下拉列表中仅列出了当前所选订阅和位置中的虚拟网络。 
    2. 在所选的虚拟网络中选择一个“子网”。 
    3. 在完成时选择“下一步:标记 >”按钮，它位于页面底部。 

        ![创建专用终结点 -“配置”页](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在“标记”页上，创建要与专用终结点资源关联的任何标记（名称和值）。 然后选择页面底部的“查看 + 创建”按钮。 
11. 在“查看 + 创建”页上查看所有设置，然后选择“创建”以创建专用终结点 。
    
    ![创建专用终结点 -“查看 + 创建”页](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 确认你创建的专用终结点连接已显示在终结点列表中。 在此示例中，专用终结点会自动获得批准，因为你已连接到自己目录中的 Azure 资源，并且有足够的权限。 

    ![已创建专用终结点](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 添加专用终结点
以下示例演示如何使用 Azure PowerShell 创建专用终结点连接。 此过程不会为你创建专用群集。 请遵循[此文](event-hubs-dedicated-cluster-create-portal.md)中的步骤创建专用的事件中心群集。 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

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

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域
为事件中心域创建专用 DNS 区域，并创建虚拟网络的关联链接：

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>使用 Azure 门户管理专用终结点

创建专用终结点时，必须批准连接。 如果要为其创建专用终结点的资源位于你的目录中，在拥有足够权限的前提下，你可以批准连接请求。 如果要连接到另一个目录中的 Azure 资源，必须等待该资源的所有者批准你的连接请求。

有四种预配状态：

| 服务操作 | 服务使用者专用终结点状态 | 说明 |
|--|--|--|
| 无 | 挂起的 | 连接是手动创建的，正等待专用链接资源所有者批准。 |
| 审批 | 已批准 | 连接已自动或手动批准，可供使用。 |
| 拒绝 | 已拒绝 | 连接被专用链接资源所有者拒绝。 |
| 删除 | 已断开连接 | 连接已被专用链接资源所有者删除，专用终结点仅供参考，应将其删除以清理资源。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>批准、拒绝或删除专用终结点连接

1. 登录到 Azure 门户。
2. 在搜索栏中键入“事件中心”。
3. 选择要管理的**命名空间**。
4. 选择“网络”选项卡。
5. 根据要执行的操作（批准、拒绝或删除），转到下面的相应部分。

### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接
1. 如果有任何挂起的连接，则会看到预配状态为“挂起”的连接被列出。 
2. 选择要批准的专用终结点
3. 选择“批准”按钮。

    ![映像](./media/private-link-service/approve-private-endpoint.png)
4. 在“批准连接”页上添加注释（可选），然后选择“是”。  如果选择“否”，则不会执行任何操作。 
5. 应会看到，列表中专用终结点连接的状态已更改为“已批准”。 

### <a name="reject-a-private-endpoint-connection"></a>拒绝专用终结点连接

1. 如果存在任何要拒绝的专用终结点连接（不管是挂起的请求还是现有的连接），请选择该连接并单击“拒绝”按钮。

    ![映像](./media/private-link-service/private-endpoint-reject-button.png)
2. 在“拒绝连接”页上输入注释（可选），然后选择“是”。  如果选择“否”，则不会执行任何操作。 
3. 应会看到，列表中专用终结点连接的状态已更改为“已拒绝”。 

### <a name="remove-a-private-endpoint-connection"></a>删除专用终结点连接

1. 若要删除某个专用终结点连接，请在列表中选择它，然后在工具栏上选择“删除”。
2. 在“删除连接”页上，选择“是”以确认删除该专用终结点。  如果选择“否”，则不会执行任何操作。
3. 应会看到，状态已更改为“已断开连接”。 然后你会发现，该终结点已在列表中消失。

## <a name="validate-that-the-private-link-connection-works"></a>验证专用链接连接是否有效

应该验证专用终结点资源的同一子网中的资源是否可以通过专用 IP 地址连接到事件中心命名空间，以及它们是否具有正确的专用 DNS 区域集成。

首先，遵循[在 Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤创建一个虚拟机。

在“网络”选项卡中： 

1. 指定虚拟网络和子网 。 必须选择已将专用终结点部署到的虚拟网络。
2. 指定一个公共 IP 资源。
3. 对于“NIC 网络安全组”，请选择“无” 。
4. 对于“负载均衡”，请选择“否”。 

连接到 VM，打开命令行并运行以下命令：

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

应会看到如下所示的结果。 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和设计注意事项

**定价**：有关定价信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**：此功能可在所有 Azure 公共区域中使用。

**每个事件中心命名空间的最大专用终结点数目**：120。

有关详细信息，请参阅 [Azure 专用链接服务：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 专用链接](../private-link/private-link-service-overview.md)
- 详细了解 [Azure 事件中心](event-hubs-about.md)
