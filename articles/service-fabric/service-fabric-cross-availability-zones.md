---
title: 跨可用性区域部署群集
description: 了解如何跨可用性区域创建 Azure 服务结构群集。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: 6da9517f822c9c157d26a1bda8dab2c694b08b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609972"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性区域部署 Azure 服务结构群集
Azure 中的可用性区域是一种高可用性产品，可保护应用程序和数据免受数据中心故障的影响。 可用性区域是一个独特的物理位置，在 Azure 区域内配备独立的电源、冷却和网络。

Service Fabric 支持通过部署固定到特定区域的节点类型跨可用性区域的群集。 这将确保应用程序的高度可用性。 Azure 可用性区域仅在特定区域可用。 有关详细信息，请参阅[Azure 可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。

提供示例模板：[服务结构跨可用性区域模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>跨可用性区域跨越的 Azure 服务结构群集的主节点类型的建议拓扑
分布在可用性区域的服务结构群集可确保群集状态的高可用性。 要跨区域跨服务结构群集，必须在区域支持的每个可用区域中创建主节点类型。 这将在每个主节点类型之间均匀分布种子节点。

主节点类型的建议拓扑需要下面概述的资源：

* 群集可靠性级别设置为白金级。
* 标记为主节点的三个节点类型。
    * 每个节点类型应映射到位于不同区域的自己的虚拟机规模集。
    * 每个虚拟机规模集应至少有五个节点（银耐久性）。
* 使用标准 SKU 的单个公共 IP 资源。
* 使用标准 SKU 的单个负载均衡器资源。
* 部署虚拟机缩放集的子网引用的 NSG。

>[!NOTE]
> 虚拟机缩放集单个放置组属性必须设置为 true，因为 Service Fabric 不支持跨区域的单个虚拟机规模集。

 ![Azure 服务结构可用性区域体系结构][sf-architecture]

## <a name="networking-requirements"></a>网络要求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源
要在虚拟机缩放集资源上启用区域属性，该虚拟机规模集引用的负载均衡器和 IP 资源必须同时使用*标准*SKU。 在没有 SKU 属性的情况下创建负载均衡器或 IP 资源将创建基本 SKU，该功能库不支持可用性区域。 默认情况下，标准 SKU 负载均衡器将阻止来自外部的所有流量;为了允许外部流量，必须将 NSG 部署到子网。

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
> 无法对公共 IP 和负载均衡器资源进行 SKU 的就地更改。 如果要从具有基本 SKU 的现有资源进行迁移，请参阅本文的迁移部分。

### <a name="virtual-machine-scale-set-nat-rules"></a>虚拟机规模集 NAT 规则
负载均衡器入站 NAT 规则应与虚拟机规模集中的 NAT 池匹配。 每个虚拟机规模集必须具有唯一的入站 NAT 池。

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
与使用基本 SKU 相比，标准负载均衡器和标准公共 IP 引入了新的功能和不同的出站连接行为。 如果在使用标准 SKU 时需要出站连接，则必须使用标准公共 IP 地址或标准公共负载均衡器显式定义它。 有关详细信息，请参阅[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust)和[Azure 标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

>[!NOTE]
> 标准模板引用 NSG，默认情况下允许所有出站流量。 入站流量仅限于服务结构管理操作所需的端口。 可以修改 NSG 规则以满足您的要求。

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>在虚拟机规模集中启用区域
要启用区域，在虚拟机规模集中，必须在虚拟机缩放集资源中包括以下三个值。

* 第一个值是**区域**属性，它指定虚拟机缩放集将部署到哪个可用性区域。
* 第二个值是"单一放置组"属性，必须设置为 true。
* 第三个值是服务结构虚拟机扩展中的"故障域覆盖"属性。 此属性的值应包括将放置此虚拟机比例集的区域和区域。 示例："faultDomainOverride"："eastus/az1"所有虚拟机缩放集资源都必须放置在同一区域中，因为 Azure 服务结构群集没有跨区域支持。

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>在服务结构群集资源中启用多个主节点类型
要将一个或多个节点类型设置为群集资源中的主节点类型，将"isPrimary"属性设置为"true"。 跨可用区域部署服务结构群集时，应在不同的区域中有三个节点类型。

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
要迁移群集（该群集使用负载均衡器和 IP 与基本 SKU 一起），必须首先使用标准 SKU 创建全新的负载均衡器和 IP 资源。 无法就地更新这些资源。

新的 LB 和 IP 应在要使用的新交叉可用区域节点类型中引用。 在上面的示例中，在区域 1、2 和 3 中添加了三个新的虚拟机规模集资源。 这些虚拟机规模集引用新创建的 LB 和 IP，并在服务结构群集资源中标记为主要节点类型。

首先，您需要将新资源添加到现有资源管理器模板。 这些资源包括：
* 使用标准 SKU 的公共 IP 资源。
* 使用标准 SKU 的负载均衡器资源。
* 部署虚拟机缩放集的子网引用的 NSG。
* 三个节点类型标记为主节点。
    * 每个节点类型都应映射到位于不同区域的自己的虚拟机规模集。
    * 每个虚拟机规模集应至少有五个节点（银耐久性）。

这些资源的示例可以在[示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)中找到。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

资源完成部署后，即可开始从原始群集禁用主节点类型的节点。 禁用节点后，系统服务将迁移到上述步骤中已部署的新主节点类型。

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

一旦所有节点都禁用，系统服务将在主节点类型上运行，主节点类型跨区域分布。 然后，可以从群集中删除禁用的节点。 删除节点后，可以删除原始 IP、负载均衡器和虚拟机缩放集资源。

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

然后，您应该从已部署的资源管理器模板中删除对这些资源的引用。

最后一步将涉及更新 DNS 名称和公共 IP。

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
