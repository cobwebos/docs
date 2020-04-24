---
title: 将 Azure 服务总线与 Azure 专用链接服务集成
description: 了解如何将 Azure 服务总线与 Azure 专用链接服务集成
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: f456137b61a96f555b2604e7871516fd1d38ab42
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116700"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>将 Azure 服务总线与 Azure 专用链接集成（预览版）

使用 azure 专用链接服务，可以通过虚拟网络中的**专用终结点**访问 azure 服务（例如，Azure 服务总线、azure 存储和 Azure Cosmos DB）以及 azure 托管的客户/合作伙伴服务。

专用终结点是一个网络接口，该接口将你私下并安全地连接到由 Azure 专用链接提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[什么是 Azure 专用链接？](../private-link/private-link-overview.md)

>[!WARNING]
> 实现专用终结点可阻止其他 Azure 服务与 Service Bus 交互。
>
> 实现虚拟网络时，受信任的 Microsoft 服务不受支持。
>
> 不适用于虚拟网络常见 Azure 方案（请注意，该列表内容并不详尽）****-
> - 与 Azure 事件网格的集成
> - Azure IoT 中心路由
> - Azure IoT Device Explorer
>
> 以下 Microsoft 服务必须在虚拟网络中
> - Azure 应用服务
> - Azure Functions

> [!IMPORTANT]
> Azure 服务总线**高级**层支持此功能。 有关高级层的详细信息，请参阅[服务总线高级和标准消息传送层](service-bus-premium-messaging.md)一文。
>
> 此功能目前处于**预览阶段**。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 门户添加专用终结点

### <a name="prerequisites"></a>必备条件

若要将服务总线命名空间与 Azure 专用链接集成，你将需要以下实体或权限：

- 一个服务总线命名空间。
- 一个 Azure 虚拟网络。
- 虚拟网络中的子网。
- 服务总线命名空间和虚拟网络的所有者或参与者权限。

专用终结点和虚拟网络必须位于同一区域。 使用门户选择专用终结点的区域时，只会自动筛选该区域中的虚拟网络。 你的服务总线命名空间可以在不同的区域中。 而且，专用终结点在虚拟网络中使用专用 IP 地址。

### <a name="steps"></a>steps

如果已经有一个命名空间，则可以通过执行以下步骤创建专用终结点：

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在搜索栏中，键入 "**服务总线**"。
3. 从列表中选择要向其添加专用终结点的**命名空间**。
4. 选择 "**设置**" 下的 "**网络**" 选项卡。
5. 选择页面顶部的 "**专用终结点连接（预览）** " 选项卡
6. 选择页面顶部的 " **+ 专用终结点**" 按钮。

    !["添加专用终结点" 按钮](./media/private-link-service/private-link-service-3.png)
7. 在 "**基本**信息" 页上，执行以下步骤： 
    1. 选择要在其中创建专用终结点的**Azure 订阅**。 
    2. 选择专用终结点资源的**资源组**。
    3. 输入专用终结点的**名称**。 
    5. 选择专用终结点的**区域**。 专用终结点必须与虚拟网络位于同一区域中，但可以在与所连接到的专用链接资源不同的区域中。 
    6. 选择页面底部的 "**下一步：资源 >** " 按钮。

        ![创建专用终结点-基本页](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 "**资源**" 页上，执行以下步骤：
    1. 对于 "连接方法"，如果在 **"我的目录" 中选择 "连接到 Azure 资源**"，请执行以下步骤：   
        1. 选择**服务总线命名空间**所在的**Azure 订阅**。 
        2. 对于 "**资源类型**"，请为**资源类型**选择 "" **。**
        3. 对于 "**资源**"，请从下拉列表中选择一个服务总线命名空间。 
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
12. 确认已创建专用终结点。 如果你是资源的所有者，并且已在**连接方法**中选择了 "**连接到我的目录中的 Azure 资源**" 选项，则应**自动批准**端点连接。 如果它处于 "**挂起**" 状态，请参阅[使用 Azure 门户管理专用终结点](#manage-private-endpoints-using-azure-portal)部分。

    ![已创建专用终结点](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 添加专用终结点
下面的示例演示如何使用 Azure PowerShell 创建到服务总线命名空间的专用终结点连接。

专用终结点和虚拟网络必须位于同一区域。 你的服务总线命名空间可以在不同的区域中。 而且，专用终结点在虚拟网络中使用专用 IP 地址。

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

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
1. 在搜索栏中，键入 "**服务总线**"。
1. 选择要管理的**命名空间**。
1. 选择 "**网络**" 选项卡。
5. 根据你想要执行的操作（"批准"、"拒绝" 或 "删除"），切换到下面的相应部分。 

### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接

1. 如果有任何挂起的连接，你会在预配状态中看到 "**挂起**" 列出的连接。 
2. 选择要批准的**专用终结点**
3. 选择 "**批准**" 按钮。

    ![批准专用终结点](./media/private-link-service/private-endpoint-approve.png)
4. 在 "**批准连接**" 页上，输入可选**注释**，然后选择 **"是"**。 如果选择 "**否**"，则不会执行任何操作。 

    !["批准连接" 页](./media/private-link-service/approve-connection-page.png)
5. 你应在列表中看到连接状态已更改为 "**已批准**"。 

    ![连接状态-已批准](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>拒绝专用终结点连接

1. 如果要拒绝的任何专用终结点连接、该连接是挂起的请求还是以前批准的现有连接，请选择终结点连接，然后单击 "**拒绝**" 按钮。

    ![拒绝按钮](./media/private-link-service/private-endpoint-reject.png)
2. 在 "**拒绝连接**" 页上，输入可选注释，然后选择 **"是"**。 如果选择 "**否**"，则不会执行任何操作。 

    ![拒绝连接页](./media/private-link-service/reject-connection-page.png)
3. 应会在列表中已更改的已**拒绝**状态显示连接状态。 

    ![已拒绝终结点](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>删除专用终结点连接

1. 若要删除专用终结点连接，请在列表中选择它，然后在工具栏上选择 "**删除**"。 

    ![“删除”按钮](./media/private-link-service/remove-endpoint.png)
2. 在 "**删除连接**" 页上，选择 **"是"** 以确认删除专用终结点。 如果选择 "**否**"，则不会执行任何操作。 

    ![删除连接页](./media/private-link-service/delete-connection-page.png)
3. 你应看到状态已更改为 "已**断开连接**"。 然后，你将看到终结点从列表中消失。 

## <a name="validate-that-the-private-link-connection-works"></a>验证专用链接连接是否有效

应该验证专用终结点资源的同一子网中的资源是否通过专用 IP 地址连接到服务总线命名空间，以及它们是否具有正确的专用 DNS 区域集成。

首先，遵循[在 Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤创建一个虚拟机。

在 "**网络**" 选项卡中：

1. 指定**虚拟网络**和**子网**。 可以创建新的或选择现有的虚拟网络。 如果选择现有的虚拟网络，请确保区域匹配。
1. 指定**公共 IP**资源。
1. 对于 " **NIC 网络安全组**"，请选择 "**无**"。
1. 对于 "**负载均衡**"，请选择 "**否**"。

打开命令行并运行以下命令：

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

如果运行 ns 查找命令来解析公用终结点上的服务总线命名空间的 IP 地址，则会看到如下所示的结果：

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

如果运行 ns 查找命令来解析专用终结点上的服务总线命名空间的 IP 地址，则会看到如下所示的结果：

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和设计注意事项

**定价**：有关定价信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**： Azure 服务总线的专用终结点处于公共预览版中。 此功能可在所有 Azure 公共区域中使用。

**每个服务总线命名空间的专用终结点的最大数目**：120。

有关详细信息，请参阅 [Azure 专用链接服务：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 专用链接](../private-link/private-link-service-overview.md)
- 了解有关[Azure 服务总线](service-bus-messaging-overview.md)的详细信息
