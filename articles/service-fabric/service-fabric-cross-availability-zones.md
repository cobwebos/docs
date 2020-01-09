---
title: 跨可用性区域部署群集
description: 了解如何跨可用性区域创建 Azure Service Fabric 群集。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609972"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性区域部署 Azure Service Fabric 群集
Azure 中的可用性区域是一种高可用性产品，可保护应用程序和数据免受数据中心故障的影响。 可用性区域是一种独特的物理位置，它在 Azure 区域内配有独立的电源、冷却和网络。

Service Fabric 通过部署固定到特定区域的节点类型来支持跨可用性区域的群集。 这将确保应用程序的高可用性。 Azure 可用性区域仅在选择区域中可用。 有关详细信息，请参阅[Azure 可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。

示例模板可用： [Service Fabric 跨可用性区域模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>跨可用性区域的 Azure Service Fabric 群集的主节点类型的建议拓扑
分布在可用性区域上的 Service Fabric 群集可确保群集状态的高可用性。 若要跨区域跨 Service Fabric 群集，必须在该区域支持的每个可用性区域中创建主节点类型。 这会在每个主节点类型之间平均分配种子节点。

建议用于主节点类型的拓扑需要如下所述的资源：

* 群集可靠性级别设置为 "白金"。
* 三个标记为主节点的节点类型。
    * 每个节点类型都应映射到其自己的虚拟机规模集，位于不同的区域中。
    * 每个虚拟机规模集应至少具有五个节点（银持续性）。
* 使用标准 SKU 的单个公共 IP 资源。
* 使用标准 SKU 的单个负载均衡器资源。
* 用于部署虚拟机规模集的子网所引用的 NSG。

>[!NOTE]
> "虚拟机规模集单位置组" 属性必须设置为 "true"，因为 Service Fabric 不支持跨区域的单个虚拟机规模集。

 ![Azure Service Fabric 可用性区域体系结构][sf-architecture]

## <a name="networking-requirements"></a>网络要求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源
若要启用虚拟机规模集资源的 "区域" 属性，该虚拟机规模集所引用的负载均衡器和 IP 资源必须都使用*标准*SKU。 如果不使用 SKU 属性创建负载平衡器或 IP 资源，将会创建不支持可用性区域的基本 SKU。 默认情况下，标准 SKU 负载均衡器会阻止从外部的所有流量。若要允许外部流量，必须将 NSG 部署到子网。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> 不能在公共 IP 和负载均衡器资源上对 SKU 进行就地更改。 如果要从具有基本 SKU 的现有资源进行迁移，请参阅本文的迁移部分。

### <a name="virtual-machine-scale-set-nat-rules"></a>虚拟机规模集 NAT 规则
负载均衡器入站 NAT 规则应该匹配虚拟机规模集中的 NAT 池。 每个虚拟机规模集都必须具有唯一的入站 NAT 池。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>标准 SKU 负载均衡器出站规则
与使用基本 Sku 相比，标准负载均衡器和标准公共 IP 向出站连接引入了新功能和不同的行为。 如果在使用标准 SKU 时需要出站连接，则必须使用标准公共 IP 地址或标准公共负载均衡器显式定义它。 有关详细信息，请参阅[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust)和[Azure 标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

>[!NOTE]
> 标准模板引用了默认情况下允许所有出站流量的 NSG。 入站流量仅限于 Service Fabric 管理操作所需的端口。 可对 NSG 规则进行修改以满足你的要求。

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>在虚拟机规模集上启用区域
若要在虚拟机规模集上启用区域，必须在虚拟机规模集资源中包含以下三个值：

* 第一个值是**zone**属性，它指定虚拟机规模集将部署到哪个可用性区域。
* 第二个值为 "singlePlacementGroup" 属性，该属性必须设置为 true。
* 第三个值是 Service Fabric 虚拟机规模集扩展中的 "faultDomainOverride" 属性。 此属性的值应包括将在其中放置此虚拟机规模集的区域和区域。 例如： "faultDomainOverride"： "eastus/az1" 所有虚拟机规模集资源必须放置在同一区域中，因为 Azure Service Fabric 群集没有跨区域支持。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>在 Service Fabric 群集资源中启用多个主节点类型
若要将一个或多个节点类型设置为群集资源中的主要节点类型，请将 "isPrimary" 属性设置为 "true"。 跨可用性区域部署 Service Fabric 群集时，在不同的区域应具有三种节点类型。

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>使用基本 SKU 负载均衡器和基本 SKU IP 从群集迁移到使用可用性区域
若要迁移将负载均衡器和 IP 与基本 SKU 一起使用的群集，必须先使用标准 SKU 创建全新的负载均衡器和 IP 资源。 不能就地更新这些资源。

新的 LB 和 IP 应在你想要使用的新的跨可用性区域节点类型中进行引用。 在上面的示例中，在区域1、2和3中添加了三个新的虚拟机规模集资源。 这些虚拟机规模集引用新创建的 LB 和 IP，并标记为 Service Fabric 群集资源中的主节点类型。

若要开始，需要将新资源添加到现有资源管理器模板。 这些资源包括：
* 使用标准 SKU 的公共 IP 资源。
* 使用标准 SKU 的负载均衡器资源。
* 用于部署虚拟机规模集的子网所引用的 NSG。
* 三个标记为主节点的节点类型。
    * 每个节点类型都应映射到其自己的虚拟机规模集，位于不同的区域中。
    * 每个虚拟机规模集应至少具有五个节点（银持续性）。

可在[示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)中找到这些资源的示例。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

资源完成部署后，可以开始从原始群集中禁用主节点类型中的节点。 在禁用节点的情况下，系统服务将迁移到前面步骤中部署的新的主节点类型。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

节点全部禁用后，系统服务将在跨区域的主节点类型上运行。 然后，可以从群集中删除禁用的节点。 删除节点后，可以删除原始 IP、负载均衡器和虚拟机规模集资源。

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

然后，应从已部署的资源管理器模板中删除对这些资源的引用。

最后一个步骤将涉及更新 DNS 名称和公共 IP。

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
