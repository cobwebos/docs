---
title: 将 Azure 事件中心与 Azure 专用链接服务集成
description: 了解如何将 Azure 事件中心与 Azure 专用链接服务集成
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: 110d4b94eda8315c20f4baa70256f7e5ed378530
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106468"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>将 Azure 事件中心与 Azure 专用链接集成（预览版）
使用 azure 专用链接服务，可以通过虚拟网络中的**专用终结点**访问 azure 服务（例如，Azure 事件中心、azure 存储和 Azure Cosmos DB）以及 azure 托管的客户/合作伙伴服务。

专用终结点是一个网络接口，该接口将你私下并安全地连接到由 Azure 专用链接提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[什么是 Azure 专用链接？](../private-link/private-link-overview.md)

> [!IMPORTANT]
> 只有**专用**层才支持此功能。 有关专用层的详细信息，请参阅[事件中心专用层的概述](event-hubs-dedicated-overview.md)。 
>
> 此功能目前处于**预览阶段**。 

>[!WARNING]
> 启用专用终结点可阻止其他 Azure 服务与事件中心交互。
>
> 实现虚拟网络时，受信任的 Microsoft 服务不受支持。
>
> 不适用于虚拟网络常见 Azure 方案（请注意，该列表内容并不详尽）****-
> - Azure Monitor （诊断设置）
> - Azure 流分析
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
>
> 需要在虚拟网络上安装以下 Microsoft 服务
> - Azure Web 应用
> - Azure Functions

## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 门户添加专用终结点

### <a name="prerequisites"></a>先决条件

若要将事件中心命名空间与 Azure 专用链接集成，需要以下实体或权限：

- 事件中心命名空间。
- 一个 Azure 虚拟网络。
- 虚拟网络中的子网。
- 命名空间和虚拟网络的所有者或参与者权限。

专用终结点和虚拟网络必须位于同一区域。 使用门户选择专用终结点的区域时，只会自动筛选该区域中的虚拟网络。 命名空间可以在不同的区域中。

专用终结点使用虚拟网络中的专用 IP 地址。

### <a name="steps"></a>步骤
如果已有事件中心命名空间，可以通过执行以下步骤创建专用链接连接：

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在搜索栏中，键入 "**事件中心**"。
3. 从列表中选择要向其添加专用终结点的**命名空间**。
4. 选择 "**设置**" 下的 "**网络**" 选项卡。
5. 选择页面顶部的 "**专用终结点连接（预览版）** " 选项卡。 如果未使用事件中心的专用层，则会看到一条消息：**事件中心的专用终结点连接仅受专用群集中创建的命名空间支持**。
6. 选择页面顶部的 " **+ 专用终结点**" 按钮。

    ![映像](./media/private-link-service/private-link-service-3.png)
7. 在 "**基本**信息" 页上，执行以下步骤： 
    1. 选择要在其中创建专用终结点的**Azure 订阅**。 
    2. 选择专用终结点资源的**资源组**。
    3. 输入专用终结点的**名称**。 
    5. 选择专用终结点的**区域**。 专用终结点必须与虚拟网络位于同一区域中，但可以在与所连接到的专用链接资源不同的区域中。 
    6. 选择页面底部的 "**下一步：资源 >** " 按钮。

        ![创建专用终结点-基本页](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 "**资源**" 页上，执行以下步骤：
    1. 对于 "连接方法"，如果在 **"我的目录" 中选择 "连接到 Azure 资源**"，请执行以下步骤： 
        1. 选择**事件中心命名空间**所在的**Azure 订阅**。 
        2. 对于 "**资源类型**"，请为**资源类型**选择 "node.js **/命名空间**"。
        3. 对于 "**资源**"，请从下拉列表中选择一个事件中心命名空间。 
        4. 确认 "**目标 subresource** " 设置为 "**命名空间**"。
        5. 选择页面底部的 "**下一步：配置 >** " 按钮。 
        
            ![创建专用终结点-资源页](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 如果选择 "**按资源 ID 或别名连接到 Azure 资源**"，请执行以下步骤：
        1. 输入**资源 ID**或**别名**。 它可以是某些与你共享的资源 ID 或别名。
        2. 对于**目标子资源**，请输入**命名空间**。 这是专用终结点可以访问的子资源的类型。
        3. 可有可无输入**请求消息**。 资源所有者在管理专用终结点连接时看到此消息。
        4. 然后，选择页面底部的 "**下一步：配置 >** " 按钮。

            ![创建专用终结点-使用资源 ID 进行连接](./media/private-link-service/connect-resource-id.png)
9. 在 "**配置**" 页上，选择要在其中部署专用终结点的虚拟网络中的子网。 
    1. 选择一个**虚拟网络**。 下拉列表中仅列出当前所选订阅和位置中的虚拟网络。 
    2. 选择所选虚拟网络中的**子网**。 
    3. 选择页面底部的 "**下一步：标记 >** " 按钮。 

        ![创建专用终结点-配置页](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 "**标记**" 页上，创建要与专用终结点资源关联的任何标记（名称和值）。 然后，选择页面底部的 "**查看 + 创建**" 按钮。 
11. 在 "**查看**" 和 "创建" 中，查看所有设置，并选择 "**创建**" 以创建专用终结点。
    
    ![创建专用终结点-查看和创建页面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 确认你看到已创建的专用终结点连接将显示在终结点列表中。 在此示例中，专用终结点是自动批准的，因为你已连接到目录中的 Azure 资源，并且你有足够的权限。 

    ![已创建专用终结点](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 添加专用终结点
下面的示例演示如何使用 Azure PowerShell 创建专用终结点连接。 它不会为你创建一个专用群集。 按照[本文](event-hubs-dedicated-cluster-create-portal.md)中的步骤创建专用的事件中心群集。 

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
为事件中心域创建专用 DNS 区域，并创建与虚拟网络的关联链接：

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

创建专用终结点时，必须批准连接。 如果你要为其创建专用终结点的资源在你的目录中，则可以批准所提供的连接请求。 如果要连接到另一个目录中的 Azure 资源，则必须等待该资源的所有者批准连接请求。

有四种预配状态：

| 服务操作 | 服务使用者专用终结点状态 | 说明 |
|--|--|--|
| 无 | 挂起的 | 连接是手动创建的，正等待专用链接资源所有者批准。 |
| 审批 | 已批准 | 连接已自动或手动批准，可供使用。 |
| 拒绝 | 已拒绝 | 连接被专用链接资源所有者拒绝。 |
| 删除 | 已断开连接 | 连接已被专用链接资源所有者删除，专用终结点仅供参考，应将其删除以清理资源。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>批准、拒绝或删除专用终结点连接

1. 登录到 Azure 门户。
2. 在搜索栏中，键入 "**事件中心**"。
3. 选择要管理的**命名空间**。
4. 选择 "**网络**" 选项卡。
5. 根据你想要执行的操作（"批准"、"拒绝" 或 "删除"），切换到下面的相应部分。

### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接
1. 如果有任何挂起的连接，你会在预配状态中看到 "**挂起**" 列出的连接。 
2. 选择要批准的**专用终结点**
3. 选择 "**批准**" 按钮。

    ![映像](./media/private-link-service/approve-private-endpoint.png)
4. 在 "**批准连接**" 页上，添加注释（可选），然后选择 **"是"**。 如果选择 "**否**"，则不会执行任何操作。 
5. 你应看到列表中的专用终结点连接状态已更改为 "**已批准**"。 

### <a name="reject-a-private-endpoint-connection"></a>拒绝专用终结点连接

1. 如果要拒绝的任何专用终结点连接、该连接是挂起的请求还是现有连接，请选择该连接，然后单击 "**拒绝**" 按钮。

    ![映像](./media/private-link-service/private-endpoint-reject-button.png)
2. 在 "**拒绝连接**" 页上，输入注释（可选），然后选择 **"是"**。 如果选择 "**否**"，则不会执行任何操作。 
3. 你应看到列表中的专用终结点连接状态已更改为 "已**拒绝**"。 

### <a name="remove-a-private-endpoint-connection"></a>删除专用终结点连接

1. 若要删除专用终结点连接，请在列表中选择它，然后在工具栏上选择 "**删除**"。
2. 在 "**删除连接**" 页上，选择 **"是"** 以确认删除专用终结点。 如果选择 "**否**"，则不会执行任何操作。
3. 你应看到状态已更改为 "已**断开连接**"。 然后，你将看到终结点从列表中消失。

## <a name="validate-that-the-private-link-connection-works"></a>验证专用链接连接是否有效

应该验证专用终结点资源的同一子网中的资源是否通过专用 IP 地址连接到事件中心命名空间，以及它们是否具有正确的专用 DNS 区域集成。

首先，遵循[在 Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤创建一个虚拟机。

在 "**网络**" 选项卡中：

1. 指定**虚拟网络**和**子网**。 可以创建新的或选择现有的虚拟网络。 如果选择现有的虚拟网络，请确保区域匹配。
1. 指定**公共 IP**资源。
1. 在 " **NIC 网络安全组**" 中，选择 "**无**"。
1. 在**负载平衡**中，选择 "**否**"。

打开命令行并运行以下命令：

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

如果运行 ns 查找命令来解析公用终结点上的事件中心命名空间的 IP 地址，则会看到如下所示的结果：

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

如果运行 ns 查找命令来解析某个专用终结点上的事件中心命名空间的 IP 地址，则会看到如下所示的结果：

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和设计注意事项

**定价**：有关定价信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**： Azure 事件中心的专用终结点处于公开预览。 此功能可在所有 Azure 公共区域中使用。

**每个事件中心命名空间的专用终结点的最大数目**：120。

有关详细信息，请参阅 [Azure 专用链接服务：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 专用链接](../private-link/private-link-service-overview.md)
- 了解有关[Azure 事件中心](event-hubs-about.md)的详细信息
