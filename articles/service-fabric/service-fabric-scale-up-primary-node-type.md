---
title: 纵向扩展 Azure Service Fabric 主节点类型
description: 了解如何通过添加节点类型来缩放 Service Fabric 群集。
ms.topic: article
ms.date: 08/06/2020
ms.author: pepogors
ms.openlocfilehash: a18a40cc9e467b089ea9d6be3d0ca81a21d2c474
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89228709"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-node-type"></a>通过添加节点类型来纵向扩展 Service Fabric 群集主节点类型
本文介绍了如何通过向群集添加额外的节点类型来纵向扩展 Service Fabric 群集主节点类型。 Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。

可在以下位置找到以下教程中的示例模板：[Service Fabric 主节点类型缩放示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Primary-NodeType-Scaling-Sample)

> [!WARNING]
> 如果集群状态不正常，请勿尝试主节点类型纵向扩展过程，因为这只会进一步破坏集群的稳定性。
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type"></a>升级主节点类型的大小和操作系统的过程
下面是升级主节点类型 VM 的 VM 大小和操作系统的过程。  升级后，主节点类型 VM 的大小为 Standard_D4_V2，并且运行带容器的 Windows Server 2019 Datacenter。

> [!WARNING]
> 在生产群集上尝试执行此过程之前，建议先研究示例模板并对测试群集验证此过程。 群集也可能不可用，但这段不可用的时间较短。 

### <a name="deploy-the-initial-service-fabric-cluster"></a>部署初始 Service Fabric 群集 
如果你想要按照示例进行操作，请部署包含单个主节点类型和单个规模集的初始群集：[Service Fabric - 初始群集](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-1.json)。 如果你有已部署的现有 Service Fabric 群集，则可以跳过此步骤。 

1. 登录到 Azure 帐户。 
```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```
2. 创建新的资源组。 
```powershell
# create a resource group for your cluster deployment
$resourceGroupName = "myResourceGroup"
$location = "WestUS"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location
```
3. 填写模板文件中的参数值。 
4. 将群集部署到在步骤 2 中创建的资源组。 
```powershell
# deploy the template files to the resource group created above
$templateFilePath = "C:\AzureDeploy-1.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath
```

### <a name="add-a-new-primary-node-type-to-the-cluster"></a>向群集添加新的主节点类型
> [!Note]
> 缩放操作完成后，以下步骤中创建的资源将成为群集中新的主节点类型。 请确保为初始子网、公共 IP、负载均衡器、虚拟机规模集和节点类型使用唯一的名称。 

可在以下位置找到包含以下所有步骤的模板：[Service Fabric - 新节点类型群集](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-2.json)。 以下步骤包含部分资源代码片段，其中突出显示了新资源中的更改。  

1. 在现有虚拟网络中创建新的子网。
```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```
2. 创建具有唯一 domainNameLabel 的新的公共 IP 资源。 
```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt2')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```
3. 创建依赖于上面创建的公共 IP 的新负载均衡器资源。 
```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```
4. 创建新的虚拟机规模集，该规模集使用想要扩展到的新 VM SKU 和操作系统 SKU。 

节点类型引用 
```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

VM SKU
```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS SKU 
```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

以下代码片段是新的虚拟机规模集资源的一个示例，用于为 Service Fabric 群集创建新的节点类型。 你需要确保包含工作负载所需的任何其他扩展。 

```json
    {
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeType1Name')]",
      "location": "[variables('computeLocation')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType1Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
      ],
      "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
          "mode": "Automatic"
        },
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
              {
                "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType1Name'))]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "protectedSettings": {
                    "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                    "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                  },
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeType1Name')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "enableParallelJobs": true,
                    "nicPrefixOverride": "[variables('subnet1Prefix')]",
                    "certificate": {
                      "thumbprint": "[parameters('certificateThumbprint')]",
                      "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.0"
                }
              }
            ]
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[concat(variables('nicName'), '-1')]",
                "properties": {
                  "ipConfigurations": [
                    {
                      "name": "[concat(variables('nicName'),'-',1)]",
                      "properties": {
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[variables('lbPoolID1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[variables('lbNatPoolID1')]"
                          }
                        ],
                        "subnet": {
                          "id": "[variables('subnet1Ref')]"
                        }
                      }
                    }
                  ],
                  "primary": true
                }
              }
            ]
          },
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "computernamePrefix": "[variables('vmNodeType1Name')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[parameters('sourceVaultValue')]"
                },
                "vaultCertificates": [
                  {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('certificateUrlValue')]"
                  }
                ]
              }
            ]
          },
          "storageProfile": {
            "imageReference": {
              "publisher": "[parameters('vmImagePublisher1')]",
              "offer": "[parameters('vmImageOffer1')]",
              "sku": "[parameters('vmImageSku1')]",
              "version": "[parameters('vmImageVersion1')]"
            },
            "osDisk": {
              "caching": "ReadOnly",
              "createOption": "FromImage",
              "managedDisk": {
                "storageAccountType": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      },
      "sku": {
        "name": "[parameters('vmNodeType1Size')]",
        "capacity": "[parameters('nt1InstanceCount')]",
        "tier": "Standard"
      },
      "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
      }
    },

```

5. 向群集添加新的节点类型，该类型引用前面创建的虚拟机规模集。 此节点类型上的 **isPrimary** 属性应当设置为 true。 
```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```
6. 部署更新后的 ARM 模板。 
```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-2.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

部署完成后，Service Fabric 群集现在将具有两个节点类型。 

### <a name="remove-the-existing-node-type"></a>删除现有节点类型 
资源完成部署后，可以开始禁用原始主节点类型中的节点。 因为节点被禁用，所以系统服务将迁移到前面步骤中部署的新的主节点类型。

1. 将 Service Fabric 群集资源中的“主节点类型”属性设置为 false。 
```json
{
    "name": "[variables('vmNodeType0Name')]",
    "applicationPorts": {
        "endPort": "[variables('nt0applicationEndPort')]",
        "startPort": "[variables('nt0applicationStartPort')]"
    },
    "clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
    "durabilityLevel": "Bronze",
    "ephemeralPorts": {
        "endPort": "[variables('nt0ephemeralEndPort')]",
        "startPort": "[variables('nt0ephemeralStartPort')]"
    },
    "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
    "isPrimary": false,
    "reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
    "vmInstanceCount": "[parameters('nt0InstanceCount')]"
}
```
2. 部署在原始节点类型上具有更新的 isPrimary 属性的模板。 你可以在此处找到原始节点类型上 primary 标志设置为 false 的模板：[Service Fabric - 主节点类型 False](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-3.json).

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-3.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

3. 禁用节点类型 0 中的节点。 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterConnectionEndpoint `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"


$nodeType = "nt1vm" # specify the name of node type
$nodes = Get-ServiceFabricNode 

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```
* 对于铜级持续性，请等待所有节点进入已禁用状态。
* 对于银级和金级持续性，某些节点将进入已禁用状态，剩余节点将处于正在禁用状态。 检查处于正在禁用状态的节点的详细信息选项卡，如果这些节点全部停滞在确保基础结构服务分区的仲裁状态，则可以安全地继续操作。

> [!Note]
> 此步骤可能需要一段时间才能完成。 

4. 停止节点类型 0 上的数据。 
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```
5. 解除分配原始虚拟机规模集中的节点 
```powershell
$scaleSetName="nt1vm"
$scaleSetResourceType="Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```
> [!Note]
> 如果你已在使用标准 SKU 公共 IP 和标准 SKU 负载均衡器，则步骤 6 和 7 是可选的。 在这种情况下，可以在同一个负载均衡器下拥有多个虚拟机规模集/节点类型。 

6. 你现在可以删除原始 IP 和负载均衡器资源。 在此步骤中，你还将更新 DNS 名称。 

```powershell
$lbname="LB-cluster-name-nt1vm"
$lbResourceType="Microsoft.Network/loadBalancers"
$ipResourceType="Microsoft.Network/publicIPAddresses"
$oldPublicIpName="PublicIP-LB-FE-nt1vm"
$newPublicIpName="PublicIP-LB-FE-nt2vm"

$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbname -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
``` 

7. 更新群集上的管理终结点以引用新 IP。 
```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```
8. 从节点类型 0 中删除节点状态。
```powershell
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```
9. 从 ARM 模板的 Service Fabric 资源中删除原始节点类型引用。 
```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```
（仅适用于银级和更高级别持续性的群集）更新模板中的群集资源，并通过在群集资源属性下添加 applicationDeltaHealthPolicies，将运行状况策略配置为忽略 fabric:/System 应用程序运行状况，如下所示。 以下策略应忽略现有错误，但不允许新的运行状况错误。
```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```
10. 从 ARM 模板中删除与原始节点类型相关的所有其他资源。 如果需要一个已删除所有这些原始资源的模板，请参阅 [Service Fabric - 新节点类型群集](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/Primary-NodeType-Scaling-Sample/AzureDeploy-4.json)。

11. 部署修改后的 Azure 资源管理器模板。 ** 此步骤需要花费一段时间，通常最长为两个小时。 此升级会将设置更改为 InfrastructureService;因此，需要重新启动节点。 在这种情况下，将忽略 forceRestart。 参数 upgradeReplicaSetCheckTimeout 指定 Service Fabric 等待分区进入安全状态（如果尚未进入安全状态）的最长时间。 一旦节点上的所有分区都已通过安全检查，Service Fabric 就会在该节点上继续升级。 可将参数 upgradeTimeout 的值减至 6 小时，但若要获得最高安全性，应使用 12 小时。
然后，验证门户中的 Service Fabric 资源是否显示为就绪状态。 

```powershell
# deploy the updated template files to the existing resource group
$templateFilePath = "C:\AzureDeploy-4.json"
$parameterFilePath = "C:\AzureDeploy.Parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
```

群集的主节点类型现已升级。 验证已部署的所有应用程序是否正常运行以及群集运行状况是否正常。

## <a name="next-steps"></a>后续步骤
* 了解如何[向群集添加节点类型](virtual-machine-scale-set-scale-node-type-scale-out.md)
* 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
* [横向扩展或缩减 Azure 群集](service-fabric-tutorial-scale-cluster.md)
* 使用 fluent Azure 计算 SDK [以编程方式缩放 Azure 群集](service-fabric-cluster-programmatic-scaling.md)。
* [横向扩展或缩减独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。
