---
title: 在 Azure 中缩放 Service Fabric 群集 | Microsoft Docs
description: 本教程介绍如何在 Azure 中缩放 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 315e3c05e23d68edb701e705fd1ed02a840a8936
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226804"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>教程：缩放 Azure 中的 Service Fabric 群集

本教程是系列教程的第三部分，介绍如何扩大和缩小现有群集。 完成时，将知道如何缩放群集以及如何清理剩余的资源。  有关缩放 Azure 中运行的群集的详细信息，请阅读[缩放 Service Fabric 群集](service-fabric-cluster-scaling.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加和删除节点（横向扩展和缩小）
> * 添加和删除节点类型（横向扩展和缩小）
> * 增加节点资源（纵向扩展）

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 使用模板在 Azure 上创建安全 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
> * [监视群集](service-fabric-tutorial-monitor-cluster.md)
> * 缩小或扩大群集
> * [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)
> * [删除群集](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 安装 [Azure Powershell 模块版本 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)或者 [Azure CLI](/cli/azure/install-azure-cli)。
* 在 Azure 上创建安全 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

## <a name="important-considerations-and-guidelines"></a>重要注意事项和指南

应用程序工作负荷会随时间推移而发生变化，现有服务是否需要更多（或更少）资源？  从某个节点类型[添加或删除节点](#add-nodes-to-or-remove-nodes-from-a-node-type)以增加或减少群集资源。

是否需要向群集添加超过 100 个节点？  单一 Service Fabric 节点类型/规模集不能包含超过 100 个节点/VM。  若要将群集扩展到超过 100 个节点，需[添加更多节点类型](#add-nodes-to-or-remove-nodes-from-a-node-type)。

应用程序是否有多个服务，其中是否有任何服务需是面向公众或面向 Internet 的服务？  典型的应用程序包含可接收客户端输入的前端网关服务，以及一个或多个与前端服务通信的后端服务。 在这种情况下，建议向群集[添加至少两个节点类型](#add-nodes-to-or-remove-nodes-from-a-node-type)。  

服务是否有不同的基础结构需求，例如，更多的 RAM 或更高的 CPU 周期？ 例如，应用程序包含前端服务和后端服务。 前端服务可以在容量较小（D2 等的 VM 大小）且向 Internet 开放了端口的 VM 上运行。 但是，后端服务计算密集型的服务，需要放在容量较大（D4、D6、D15 等的 VM 大小）且不连接到 Internet 的 VM 上运行。 在这种情况下，建议向群集[添加两个或多个节点类型](#add-nodes-to-or-remove-nodes-from-a-node-type)。 这样，每个节点类型都有不同的属性，例如，Internet 连接或 VM 大小。 也可以单独缩放 VM 的数目。

缩放 Azure 群集时，请记住以下准则：

* 单一 Service Fabric 节点类型/规模集不能包含超过 100 个节点/VM。  若要将群集扩展到超过 100 个节点，需添加更多节点类型。
* 运行生产工作负荷的主节点类型应具有黄金或白银[持久性级别][durability]并始终具有五个或更多个节点。
* 运行有状态生产工作负荷的非主节点类型应始终具有五个或更多个节点。
* 运行无状态生产工作负荷的非主节点类型应始终具有两个或更多个节点。
* [持久性级别][durability]为金级或银级的任何节点类型应始终具有五个或更多个节点。
* 如果要缩减主节点类型的规模（从该类型删除节点），则不得将实例数减少到小于[可靠性级别][reliability]所要求的数量。

有关详细信息，请阅读[群集容量指南](service-fabric-cluster-capacity.md)。

## <a name="export-the-template-for-the-resource-group"></a>导出资源组的模板

成功创建一个安全的 [Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)并设置资源组后，导出该资源组的资源管理器模板。 导出模板后，便可自动执行群集及其资源的未来部署，因为模板包含所有完整的基础结构。  有关导出模板的详细信息，请阅读[使用 Azure 门户管理 Azure 资源管理器资源组](/azure/azure-resource-manager/manage-resource-groups-portal)。

1. 在 [Azure 门户](https://portal.azure.com)中，转到包含群集的资源组（如果按本教程操作，即为 **sfclustertutorialgroup**）。 

2. 在左窗格中，选择“部署”或选择“部署”下的链接。 

3. 从列表中选择最近的成功部署。

4. 在左窗格中，选择“模板”，然后选择“下载”，将模板导出为 ZIP 文件。  将模板和参数保存到本地计算机。

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>添加或删除某个节点类型的节点

横向扩展和缩减或水平缩放会更改群集中的节点数。 横向扩展和缩减时，会添加更多虚拟机实例到规模集。 这些实例成为 Service Fabric 使用的节点。 Service Fabric 知道规模集什么时候添加了更多实例（通过扩大实现）并自动做出反应。 随时可以缩放群集，即使该群集上正在运行工作负荷。

### <a name="update-the-template"></a>更新模板

从最近的部署的资源组中[导出模板和参数文件](#export-the-template-for-the-resource-group)。  打开 Parameters.json 文件。  如果使用本教程中的[示例模板][template]部署了群集，群集中就有三个节点类型，且有三个参数，这些参数分别为每个节点类型设置节点数：nt0InstanceCount、nt1InstanceCount 和 nt2InstanceCount。  例如，Nt1InstanceCount 参数，为第二个节点类型设置实例计数，并设置关联的虚拟机规模集中的 VM 数。

这样，可通过更新 nt1InstanceCount 的值来更改第二个节点类型中的节点数。  请记住，无法将一个节点类型横向扩展到超过 100 个节点。  运行有状态生产工作负荷的非主节点类型应始终具有五个或更多个节点。 运行无状态生产工作负荷的非主节点类型应始终具有两个或更多个节点。

如果缩减青铜[持久性级别][durability]的节点类型规模（从其中删除节点），必须[手动删除这些节点的状态](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)。  对于白银和黄金持久性层，平台会自动完成上述步骤。

### <a name="deploy-the-updated-template"></a>部署已更新的模板
将任何更改保存到 template.json 和 parameters.json 文件。  若要部署更新的模板，请运行以下命令：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
或者运行以下 Azure CLI 命令：
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>向群集添加节点类型

在 Azure 中运行的 Service Fabric 群集中定义的每个节点类型将设置为[独立的虚拟机规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 可独立增加或减少每个节点类型、打开不同的端口集，并使用不同的容量指标。 还可以独立更改每个群集节点上运行的 OS SKU，但请注意，不能混合示例群集中运行的 Windows 和 Linux。 单个节点类型/规模集不能包含超过 100 个节点。  可以通过添加其他节点类型/规模集将群集横向扩展到超过 100 个节点。 随时可以缩放群集，即使该群集上正在运行工作负荷。

### <a name="update-the-template"></a>更新模板

从最近的部署的资源组中[导出模板和参数文件](#export-the-template-for-the-resource-group)。  打开 Parameters.json 文件。  如果使用本教程中的[示例模板][template]部署了群集，群集中就有三个节点类型。  在本部分中，通过更新和部署资源管理器模板添加第四个节点类型。 

除了新的节点类型，还添加关联的虚拟机规模集（运行在虚拟网络的独立子网中）和网络安全组。  可以选择添加新的或现有的公共 IP 地址和新规模集的 Azure 负载均衡器资源。  新的节点类型具有白银[持久性级别][durability]和 "Standard_D2_V2" 的大小。

在 template.json 文件中，添加以下新参数：
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

在 template.json 文件中，添加以下新变量：
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

在 template.json 文件中，向虚拟网络资源添加新子网：
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

在 template.json 文件中，添加新的公共 IP 地址和负载均衡器资源：
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

在 template.json 文件中，添加新网络安全组和虚拟机规模集资源。  虚拟机规模集的 Service Fabric 扩展属性内的 NodeTypeRef 属性会将指定的节点类型映射到规模集。

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabSMB",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "445",
                    "direction": "Inbound",
                    "priority": 3950,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow SMB traffic within the net, used by fabric to move packages around"
                }
            },
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
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
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
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
                "computernamePrefix": "[variables('vmNodeType3Name')]",
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
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
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
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

在 template.json 文件中，更新群集资源，并添加新的节点类型：
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

在 parameters.json 文件中，添加以下新参数和值：
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>部署已更新的模板
将任何更改保存到 template.json 和 parameters.json 文件。  若要部署更新的模板，请运行以下命令：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
或者运行以下 Azure CLI 命令：
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>从群集中删除节点类型
创建 Service Fabric 群集之后，可以通过删除节点类型（虚拟机规模集）及其所有节点来水平缩放群集。 随时可以缩放群集，即使该群集上正在运行工作负荷。 在缩放群集的同时，应用程序也会随之自动缩放。

> [!WARNING]
> 建议不要频繁使用 Remove-AzureRmServiceFabricNodeType 从生产群集中删除节点类型。 这是一个非常危险的命令，因为它会删除节点类型后的虚拟机规模集资源。 

若要删除节点类型，运行 [Remove-AzureRmServiceFabricNodeType](/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) cmdlet。  节点类型必须为白银或黄金[持久性级别][durability]，cmdlet 会删除与节点类型关联的规模集，并需要一些时间来完成操作。  然后在每个要删除的节点上运行 [Remove-servicefabricnodestate](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) cmdlet，这会删除节点状态并从群集删除节点。 如果节点上有服务，则首先将服务移出到另一个节点。 如果群集管理器找不到副本/服务的节点，则会延迟/阻止该操作。

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzureRmServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>增加节点资源 
创建 Service Fabric 群集后，可以纵向缩放群集节点类型（更改节点的资源）或升级节点类型 VM 的操作系统。  

> [!WARNING]
> 我们建议不要更改规模集/节点类型的 VM SKU，除非它在银级持久性或更高的级别运行。 更改 VM SKU 大小是一种破坏数据的就地基础结构操作。 由于无法延迟或监视此更改，此操作可能会导致有状态服务的数据丢失或其他意外操作问题（甚至可能影响无状态工作负载）。

> [!WARNING]
> 我们建议不要更改主节点类型的 VM SKU，这是一种不受支持的危险操作。  如果需要更多群集容量，可以添加更多 VM 实例或更多节点类型。  如果这不可行，可以创建新群集并从旧群集[还原应用程序状态](service-fabric-reliable-services-backup-restore.md)（如果适用）。  如果无法执行此操作，则可以[更改主节点类型的 VM SKU](service-fabric-scale-up-node-type.md)。

### <a name="update-the-template"></a>更新模板

从最近的部署的资源组中[导出模板和参数文件](#export-the-template-for-the-resource-group)。  打开 Parameters.json 文件。  如果使用本教程中的[示例模板][template]部署了群集，群集中就有三个节点类型。  

第二个节点类型中 VM 的大小在 vmNodeType1Size 参数中设置。  将 vmNodeType1Size 参数值从 Standard_D2_V2 更改为 [Standard_D3_V2](/azure/virtual-machines/windows/sizes-general#dv2-series)，这会让每个 VM 实例的资源增加一倍。

所有三个节点类型的 VM SKU 在 vmImageSku 参数中设置。  同样，更改节点类型的 VM SKU 应慎重，不建议对主节点类型执行此操作。

### <a name="deploy-the-updated-template"></a>部署已更新的模板
将任何更改保存到 template.json 和 parameters.json 文件。  若要部署更新的模板，请运行以下命令：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
或者运行以下 Azure CLI 命令：
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加和删除节点（横向扩展和缩小）
> * 添加和删除节点类型（横向扩展和缩小）
> * 增加节点资源（纵向扩展）

接下来，请转到以下教程了解如何升级群集运行时。
> [!div class="nextstepaction"]
> [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
