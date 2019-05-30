---
title: 跨可用性区域部署的 Azure Service Fabric 群集 |Microsoft Docs
description: 了解如何跨可用性区域中创建的 Azure Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077450"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>跨可用性区域部署的 Azure Service Fabric 群集
在 Azure 中的可用性区域是高可用性产品/服务，它从数据中心故障保护你的应用程序和数据。 可用性区域是一个唯一的物理位置配置了独立电源、 冷却和网络在 Azure 区域内。

Service Fabric 支持 s p a n 跨可用性区域通过将已固定的节点类型部署到特定区域的群集。 这将确保你的应用程序的高可用性。 Azure 可用性区域中选择的区域才可用。 有关详细信息，请参阅[Azure 可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。

提供了示例模板：[Service Fabric 跨可用性区域模板](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>建议的 Azure Service Fabric 群集跨越多个可用性区域的主节点类型的拓扑
跨可用性区域分布的 Service Fabric 群集，可以确保高可用性群集状态。 若要跨区域跨越的 Service Fabric 群集，必须在支持区域的每个可用性区域中创建主节点类型。 这将在每个主节点类型中均匀地分发种子节点。

主节点类型的建议的拓扑需要如下所述的资源：

* 群集可靠性级别设置为白金卡。
* 标记为主要的三个节点类型。
    * 每个节点类型应映射到其自己位于不同区域中的虚拟机规模集。
    * 每个虚拟机规模集应具有至少五个节点 （银级持续性）。
* 使用标准 SKU 的单个公共 IP 资源。
* 使用标准 SKU 的单个负载均衡器资源。
* 引用在其中部署你的虚拟机规模集的子网的 NSG。

>[!NOTE]
> 单个放置组属性必须设置为 true，因为 Service Fabric 不支持跨区域的单个虚拟机规模集虚拟机规模集。

 ![Azure Service Fabric 可用性区域体系结构][sf-architecture]

## <a name="networking-requirements"></a>网络要求
### <a name="public-ip-and-load-balancer-resource"></a>公共 IP 和负载均衡器资源
若要启用属性上的虚拟机规模集资源、 负载均衡器和引用该虚拟机规模集 IP 资源的区域必须都使用*标准*SKU。 创建负载均衡器或不带 SKU 属性的 IP 资源将创建基本 SKU，不支持可用性区域。 标准 SKU 负载均衡器将阻止从外部的所有流量默认设置。若要允许外部流量，必须将 NSG 部署到子网。

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
> 不能执行的公共 IP 和负载均衡器资源的 SKU 就地更改。 如果要从现有的资源具有基本 SKU 迁移，请参阅这篇文章的迁移部分。

### <a name="virtual-machine-scale-set-nat-rules"></a>虚拟机规模集 NAT 规则
负载均衡器入站的 NAT 规则应与从虚拟机规模集的 NAT 池。 每个虚拟机规模集必须具有唯一的入站的 NAT 池。

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
标准负载均衡器和标准公共 IP 引入新功能和不同的行为与使用基本 Sku 相比的出站连接。 如果在使用标准 SKU 时需要出站连接，则必须使用标准公共 IP 地址或标准公共负载均衡器显式定义它。 有关详细信息，请参阅[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust)并[Azure 标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

>[!NOTE]
> 标准模板引用它默认情况下允许所有出站流量的 NSG。 入站的流量仅限于所需的 Service Fabric 管理操作的端口。 可以修改 NSG 规则以满足您的要求。

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>启用的虚拟机规模集上的区域设置
若要启用区域，在虚拟机规模集必须在虚拟机规模集资源中包含以下三个值。

* 第一个值是**区域**属性，用于指定虚拟机规模集将部署到的可用性区域。
* 第二个值是"singlePlacementGroup"属性，它必须设置为 true。
* 第三个值是在 Service Fabric 虚拟机规模集扩展中的"faultDomainOverride"属性。 此属性的值应包括区域和区域将在其中放置此虚拟机规模集。 示例:"faultDomainOverride":"eastus/az1"所有虚拟机规模都集资源必须放置在同一区域中，因为 Azure Service Fabric 群集不具有跨区域支持。

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

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>启用 Service Fabric 群集资源中的多个主节点类型
若要将一个或多个节点类型设为主要群集资源中，设置为"true"的"isPrimary"属性。 在部署 Service Fabric 群集跨可用性区域时，您应有三个节点类型在不同区域中。

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

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>迁移到使用基本 SKU 负载均衡器和基本 SKU IP 从群集中使用可用性区域
若要迁移的群集，已使用负载均衡器和 IP 使用基本 SKU，必须首先创建使用标准 SKU 的全新负载均衡器和 IP 资源。 不能更新这些资源中的位置。

应在你想要使用的新跨可用性区域节点类型中引用新的 LB 和 IP。 在以下示例中，三个新的虚拟机规模集资源已添加在区域 1、 2 和 3 中。 这些虚拟机规模设置引用新创建的 LB 和 IP，并被标记为在 Service Fabric 群集资源中的主节点类型。

若要开始，你需要将新的资源添加到现有的资源管理器模板。 这些资源包括：
* 使用标准 SKU 公共 IP 资源。
* 使用标准 SKU 负载均衡器资源。
* 引用在其中部署你的虚拟机规模集的子网的 NSG。
* 标记为主要的三个节点类型。
    * 每个节点类型应映射到其自己位于不同区域中的虚拟机规模集。
    * 每个虚拟机规模集应具有至少五个节点 （银级持续性）。

中可以找到这些资源的示例[示例模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

一旦资源部署完成后，你可以开始禁用从原始群集主节点类型中的节点。 节点将被禁用，因为系统服务会将迁移到在前面步骤中部署了新的主节点类型。

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

后都将被禁用节点，将在跨多个区域的主节点类型上运行的系统服务。 然后可以从群集中删除已禁用的节点。 一旦删除节点时，可以删除原始 IP、 负载均衡器和虚拟机规模集资源。

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

然后应删除对这些资源从已部署的资源管理器模板的引用。

最后一步将涉及到更新的 DNS 名称和公共 IP。

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
