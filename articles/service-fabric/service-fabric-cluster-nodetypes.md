---
title: "Azure Service Fabric 节点类型与虚拟机规模集 | Microsoft Docs"
description: "了解 Azure Service Fabric 节点类型如何与虚拟机规模集相关联，以及如何远程连接到规模集实例或群集节点。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.openlocfilehash: 2bd3053d645d9acd4850fddf7f27237ff954e8c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 节点类型与虚拟机规模集
虚拟机规模集是一种 Azure 计算资源。 可使用规模集以集的形式部署和管理虚拟机集合。 为在 Azure Service Fabric 群集中定义的每个节点类型设置独立规模集。 可独立增加或减少每个节点类型、打开不同的端口集，并使用不同的容量指标。

下图显示了具有名为 FrontEnd 和 BackEnd 的两个节点类型的群集。 每个节点类型具有五个节点。

![具有两个节点类型的群集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>将虚拟机规模集实例映射到节点
如上图中所示，缩放集实例以实例 0 开始，然后以基数 1 递增。 编号反映在节点名称中。 例如，节点 BackEnd_0 是 BackEnd 规模集的实例 0。 此特定规模集具有五个实例，名称分别为 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

扩展规模集时，将创建新的实例。 新的规模集实例名称通常是规模集名称加上下一个实例编号。 在本示例中，即 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>将规模集负载均衡器映射到节点类型和规模集
如果已在 Azure 门户部署群集，或使用了示例 Azure 资源管理器模板，将列出资源组下所有的资源。 可看到每个规模集或节点类型的负载均衡器。 负载均衡器名称使用以下格式：LB-&lt;节点类型名称&gt;。 例如，下图中显示的 LB-sfcluster4doc-0：

![资源][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>远程连接到虚拟机规模集实例或群集节点
为在群集中定义的每个节点类型设置独立规模集。 可独立增加或减少每个节点类型。 还可使用其他 VM SKU。 不同于单实例 VM，规模集实例本身没有虚拟 IP 地址。 可能很难找到可用来远程连接到特定实例的 IP 地址和端口。

若要查找可用于远程连接到特定实例的 IP 地址和端口，请完成以下步骤。

步骤 1：通过获取远程桌面协议 (RDP) 的入站 NAT 规则，查找节点类型的虚拟 IP 地址。

首先，获取已定义为 `Microsoft.Network/loadBalancers` 资源定义的一部分的入站 NAT 规则值。

在 Azure 门户中的负载均衡器页上，选择“设置” > “入站 NAT 规则”。 可获得可用来远程连接到第一个规模集实例的 IP 地址和端口。 

![负载均衡器][LBBlade]

在下图中，IP 地址和端口为 104.42.106.156 和 3389。

![NAT 规则][NATRules]

步骤 2：查找可用来远程连接到特定规模集实例或节点的端口。

将规模集实例映射到节点。 使用规模集信息确定要使用的确切端口。

将按照与规模集实例相匹配的升序分配端口。 对于之前的 FrontEnd 节点类型示例中，下表列出了 5 个节点实例各自的端口。 向规模集实例应用相同的映射。

| 虚拟机规模集实例 | **端口** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

步骤 3：远程连接到特定规模集实例。

下图演示了如何使用远程桌面连接连接 FrontEnd_1 规模集实例：

![远程桌面连接][RDP]

## <a name="change-the-rdp-port-range-values"></a>更改 RDP 端口范围值

### <a name="before-cluster-deployment"></a>群集部署之前
使用资源管理器模板设置群集时，在 `inboundNatPools` 中指定范围。

转到 `Microsoft.Network/loadBalancers` 的资源定义。 找到 `inboundNatPools` 的描述。  替换 `frontendPortRangeStart` 和 `frontendPortRangeEnd` 值。

![inboundNatPools 值][InboundNatPools]

### <a name="after-cluster-deployment"></a>群集部署之后
部署群集后更改 RDP 端口范围值会更复杂。 为确保不回收 VM，请使用 Azure PowerShell 设置新值。 

> [!NOTE]
> 确保计算机上安装了 Azure PowerShell 1.0 或更高版本。 如果尚未安装 Azure PowerShell 1.0 或更高版本，建议根据[如何安装和配置 Azure PowerShell](/powershell/azure/overview) 一文中所述的步骤进行安装。

1. 登录到 Azure 帐户。 如果以下 PowerShell 命令失败，请验证是否正确安装了 PowerShell。

    ```
    Login-AzureRmAccount
    ```

2. 若要获取有关负载均衡器的详细信息，并查看值中对 `inboundNatPools` 的描述，请运行以下命令：

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. 将 `frontendPortRangeEnd` 和 `frontendPortRangeStart` 设置为所需值。

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>更改节点的 RDP 用户名和密码

若要更改特定节点类型中所有节点的密码，请完成以下步骤。 这些更改将应用于规模集中的所有当前和未来节点。

1. 以管理员身份打开 PowerShell。 
2. 若要登录并为会话选择订阅，请运行以下命令。 将 `SUBSCRIPTIONID` 参数更改为订阅 ID。 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. 运行以下脚本。 使用相关的 `NODETYPENAME`、`RESOURCEGROUP`、`USERNAME` 和 `PASSWORD` 值。 其中 `USERNAME` 和 `PASSWORD` 值是要在将来的 RDP 会话中用到的新凭据。 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>后续步骤
* 请参阅[“随地部署”功能的概述及其与 Azure 托管群集的比较](service-fabric-deploy-anywhere.md)。
* 了解[群集安全性](service-fabric-cluster-security.md)。
* 了解 [Service Fabric SDK 及其入门](service-fabric-get-started.md)。

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
