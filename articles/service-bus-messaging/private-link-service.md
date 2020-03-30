---
title: 将 Azure 服务总线与 Azure 专用链接服务集成
description: 了解如何将 Azure 服务总线与 Azure 专用链接服务集成
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: b8c4248b7275ac96acce96f890f6ff0148116f48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478001"
---
# <a name="integrate-azure-service-bus-with-azure-private-link-preview"></a>将 Azure 服务总线与 Azure 专用链接集成（预览）

Azure 专用链接服务使您能够通过虚拟网络中的**专用终结点**访问 Azure 服务（例如，Azure 服务总线、Azure 存储和 Azure 宇宙数据库）和 Azure 托管的客户/合作伙伴服务。

专用终结点是一个网络接口，可私下安全地连接到由 Azure 专用链接提供支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅什么是[Azure 专用链接？](../private-link/private-link-overview.md)

> [!NOTE]
> Azure 服务总线**的高级**层支持此功能。 有关高级层的详细信息，请参阅[服务总线高级和标准消息级别](service-bus-premium-messaging.md)一文。
>
> 此功能当前处于**预览状态**。 


## <a name="add-a-private-endpoint-using-azure-portal"></a>使用 Azure 门户添加专用终结点

### <a name="prerequisites"></a>先决条件

要将服务总线命名空间与 Azure 专用链接集成，您需要以下实体或权限：

- 服务总线命名空间。
- 一个 Azure 虚拟网络。
- 虚拟网络中的子网。
- 服务总线命名空间和虚拟网络的所有者或参与者权限。

专用终结点和虚拟网络必须位于同一区域。 使用门户选择专用终结点的区域时，只会自动筛选该区域中的虚拟网络。 服务总线命名空间可以位于其他区域。 此外，您的专用终结点在虚拟网络中使用专用 IP 地址。

### <a name="steps"></a>steps

如果已有现有命名空间，则可以按照以下步骤创建专用终结点：

1. 登录到 Azure[门户](https://portal.azure.com)。 
2. 在搜索栏中，键入**服务总线**。
3. 从要向其添加专用终结点的列表中选择**命名空间**。
4. 选择 **"设置**"下的 **"网络**"选项卡。
5. 选择页面顶部的**专用终结点连接（预览）** 选项卡
6. 选择页面顶部的 **"+ 专用终结点"** 按钮。

    ![添加专用终结点按钮](./media/private-link-service/private-link-service-3.png)
7. 在 **"基础知识"** 页上，按照以下步骤操作： 
    1. 选择要在其中创建专用终结点的**Azure 订阅**。 
    2. 选择专用终结点**资源的资源组**。
    3. 输入专用终结点**的名称**。 
    5. 为专用终结点选择**区域**。 专用终结点必须与虚拟网络位于同一区域中，但可以位于要连接到的专用链接资源的不同区域中。 
    6. 选择 **"下一步："页面底部的资源>** 按钮。

        ![创建专用终结点 - 基础知识页面](./media/private-link-service/create-private-endpoint-basics-page.png)
8. 在 **"资源"** 页上，按照以下步骤操作：
    1. 对于连接方法，如果选择 **"连接到目录中的 Azure 资源**"，请按照以下步骤操作：   
        1. 选择**服务总线命名空间**存在的**Azure 订阅**。 
        2. 对于**资源类型**，选择**Microsoft.服务总线/****资源类型的**命名空间。
        3. 对于**资源**，从下拉列表中选择服务总线命名空间。 
        4. 确认**目标子资源**设置为**命名空间**。
        5. 选择 **"下一步"：页面底部的"配置>"** 按钮。 
        
            ![创建专用终结点 - 资源页面](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. 如果选择 **"按资源 ID 或别名连接到 Azure 资源**"，请按照以下步骤操作：
        1. 输入**资源 ID**或**别名**。 它可以是一些人与您共享的资源 ID 或别名。
        2. 对于**目标子资源**，输入**命名空间**。 它是专用终结点可以访问的子资源的类型。 
        3. （可选）输入**请求消息**。 资源所有者在管理专用终结点连接时看到此消息。 
        4. 然后，选择**页面底部的"下一步：配置>** 按钮。 

            ![创建专用终结点 - 使用资源 ID 进行连接](./media/private-link-service/connect-resource-id.png)
9. 在 **"配置"** 页上，选择虚拟网络中的子网，以选择要部署专用终结点的位置。 
    1. 选择**虚拟网络**。 下拉列表中仅列出当前选择的订阅和位置中的虚拟网络。 
    2. 在所选虚拟网络中选择**子网**。 
    3. 选择 **"下一步"：在页面底部标记>** 按钮。 

        ![创建专用终结点 - 配置页面](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. 在 **"标记"** 页上，创建要与专用终结点资源关联的任何标记（名称和值）。 然后，选择页面底部的 **"查看 + 创建**"按钮。 
11. 在 **"审阅 + 创建**"上，查看所有设置，然后选择 **"创建"** 以创建专用终结点。
    
    ![创建专用终结点 - 查看和创建页面](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 确认已创建专用终结点。 如果您是资源的所有者，并且选择了**连接方法****的目录选项中的 Azure 资源**，则终结点连接应**自动批准**。 如果处于**挂起**状态，请参阅[使用 Azure 门户部分管理专用终结点](#manage-private-endpoints-using-azure-portal)。

    ![已创建专用终结点](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>使用 PowerShell 添加专用终结点
下面的示例演示如何使用 Azure PowerShell 创建到服务总线命名空间的专用终结点连接。

专用终结点和虚拟网络必须位于同一区域。 服务总线命名空间可以位于其他区域。 此外，您的专用终结点在虚拟网络中使用专用 IP 地址。

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

创建专用终结点时，必须批准连接。 如果要为其创建专用终结点的资源位于目录中，则可以批准连接请求，前提是您具有足够的权限。 如果要连接到另一个目录中的 Azure 资源，则必须等待该资源的所有者批准连接请求。

有四种预配状态：

| 服务操作 | 服务使用者专用终结点状态 | 描述 |
|--|--|--|
| 无 | 挂起的 | 连接是手动创建的，正等待专用链接资源所有者批准。 |
| 审批 | 已批准 | 连接已自动或手动批准，可供使用。 |
| 拒绝 | 已拒绝 | 连接被专用链接资源所有者拒绝。 |
| 删除 | 已断开连接 | 连接已被专用链接资源所有者删除，专用终结点仅供参考，应将其删除以清理资源。 |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>批准、拒绝或删除专用终结点连接

1. 登录到 Azure 门户。  
1. 在搜索栏中，键入**服务总线**。
1. 选择要管理的**命名空间**。
1. 选择 **"网络"** 选项卡。
5. 转到下面的相应部分，具体取决于您想要的操作：批准、拒绝或删除。 

### <a name="approve-a-private-endpoint-connection"></a>批准专用终结点连接

1. 如果存在任何挂起的连接，您将看到与 **"挂起"** 处于预配状态的连接。 
2. 选择要批准的**专用终结点**
3. 选择"**批准**"按钮。

    ![批准专用终结点](./media/private-link-service/private-endpoint-approve.png)
4. 在 **"批准连接**"页上，输入可选**注释**，然后选择 **"是**"。 如果选择 **"否**"，则不发生任何操作。 

    ![批准连接页](./media/private-link-service/approve-connection-page.png)
5. 您应该看到列表中的连接状态更改为 **"已批准**"。 

    ![连接状态 - 已批准](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>拒绝专用终结点连接

1. 如果要拒绝任何专用终结点连接，无论是挂起的请求还是之前已批准的现有连接，请选择终结点连接并单击 **"拒绝**"按钮。

    ![拒绝按钮](./media/private-link-service/private-endpoint-reject.png)
2. 在 **"拒绝连接"** 页上，输入可选注释，然后选择 **"是**"。 如果选择 **"否**"，则不发生任何操作。 

    ![拒绝连接页](./media/private-link-service/reject-connection-page.png)
3. 您应该在列表中看到连接的状态已更改**为"已拒绝**"。 

    ![终结点被拒绝](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>删除专用终结点连接

1. 要删除专用终结点连接，请在列表中选择它，并在工具栏上选择 **"删除**"。 

    ![“删除”按钮](./media/private-link-service/remove-endpoint.png)
2. 在 **"删除连接**"页上，选择 **"是**"以确认删除专用终结点。 如果选择 **"否**"，则不发生任何操作。 

    ![删除连接页](./media/private-link-service/delete-connection-page.png)
3. 您应该看到状态更改为 **"已断开连接**"。 然后，您将看到终结点从列表中消失。 

## <a name="validate-that-the-private-link-connection-works"></a>验证专用链接连接是否有效

应验证专用终结点资源的同一子网中的资源是否通过专用 IP 地址连接到服务总线命名空间，并且它们具有正确的专用 DNS 区域集成。

首先，遵循[在 Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)中的步骤创建一个虚拟机。

在 **"网络"** 选项卡中：

1. 指定**虚拟网络和****子网**。 可以创建新的或选择现有的虚拟网络。 如果选择现有的虚拟网络，请确保区域匹配。
1. 指定**公共 IP**资源。
1. 对于**NIC 网络安全组**，选择 **"无**"。
1. 对于**负载平衡**，选择 **"否**"。

打开命令行并运行以下命令：

```console
nslookup <your-service-bus-namespace-name>.servicebus.windows.net
```

如果运行 ns 查找命令来解决公共终结点上的服务总线命名空间的 IP 地址，您将看到如下所示的结果：

```console
c:\ >nslookup <your-service-bus-namespace-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

如果运行 ns 查找命令以解决专用终结点上的服务总线命名空间的 IP 地址，您将看到如下所示的结果：

```console
c:\ >nslookup your_service-bus-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>限制和设计注意事项

**定价**：有关定价信息，请参阅[Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。

**限制**： Azure 服务总线的专用终结点处于公共预览版。 此功能可在所有 Azure 公共区域中使用。

**每个服务总线命名空间的最大专用终结点数**：120。

有关详细信息，请参阅[Azure 专用链接服务：限制](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 专用链接](../private-link/private-link-service-overview.md)
- 了解有关[Azure 服务总线](service-bus-messaging-overview.md)的更多
