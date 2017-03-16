---
title: "Azure Service Fabric 的网络模式 | Microsoft Docs"
description: "介绍 Service Fabric 的常见网络模式以及如何使用 Azure 网络功能创建群集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 030114fa1ea9b76c0ed48baeffb8859260fc8e52
ms.openlocfilehash: 78012ae1552c01690b0ad5b1285173ef9faf12bc
ms.lasthandoff: 03/01/2017


---
# <a name="service-fabric-networking-patterns"></a>Service Fabric 网络模式
创建 Service Fabric 群集时有一个较常见的问题，即如何将群集与各种 Azure 网络功能集成。  本文演示如何使用以下功能创建群集：

- [现有虚拟网络/子网](#existingvnet)
- [静态公共 IP 地址](#staticpublicip)
- [仅限内部的负载均衡器](#internallb)
- [内部 + 外部负载均衡器](#internalexternallb)

需要记住一个关键概念，即 Service Fabric 在标准虚拟机规模集中运行，因此，可在虚拟机规模集中使用的任何功能也能用于 Service Fabric 群集。 Resource Manager 模板的网络部分完全相同。  一旦部署到现有的 VNet，就能很轻松地整合其他网络功能，例如 ExpressRoute、VPN 网关、网络安全组 (NSG) 和 VNet 对等互连。

Service Fabric 唯一的特别之处在于 [Azure 门户](https://portal.azure.com)在内部使用 Service Fabric 资源提供程序 (SFRP) 连接到群集，以获取有关节点和应用程序的信息。  SFRP 需要对管理终结点上的 HTTP 网关端口（默认为&19080;）具有可公开访问的入站访问权限。[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 使用此端口来管理群集， Service Fabric 资源提供程序则使用此端口来查询群集相关信息，以便在 Azure 门户中显示。  如果不能从 SFRP 访问此端口，则会在管理门户中显示一条诸如“找不到节点”之类的消息，并且节点和应用程序列表显示为空。  如果想通过 Azure 门户查看群集，负载均衡器必须公开一个公共 IP 地址，而且 NSG 必须允许传入 19080 流量。  如果不能满足这些要求，Azure 门户则不会显示群集的当前状态。  如果能满足，不仅群集不受影响，还可以使用 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 来获取其当前状态（根据网络要求的不同，这可能是一个可接受的限制）。  这一限制只是暂时的，我们计划在未来的更新中解除，届时，即便无法公开访问群集也不会丢失任何管理门户功能。

## <a name="templates"></a>模板

[此处](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip)可找到所有模板。 使用以下 powershell 命令，应该能够按原样部署模板。  如果部署现有 VNet 模板或静态公共 IP 模板，请确保先完成[初始设置](#initialsetup)部分。

<a id="initialsetup"></a>
## <a name="initial-setup"></a>初始设置

### <a name="existing-virtual-network"></a>现有虚拟网络

我先从一个名为“ExistingRG-vnet”的现有虚拟网络开始，该虚拟网络位于资源组 *ExistingRG* 中，并有一个名为“default”的子网。  这些资源是在使用 Azure 门户创建标准虚拟机时默认创建的。  你也可以只创建 VNet 和子网，不创建虚拟机。 将群集添加到现有 VNet 的主要目的是提供与其他 VM 之间的网络连接，因此，通过创建 VM，可以具象地演示现有虚拟网络的典型用法。  如果 Service Fabric 群集仅使用不带公共 IP 地址的内部负载均衡器，则 VM 及其公共 IP 还可用作跳转盒。

### <a name="static-public-ip-address"></a>静态公共 IP 地址

静态公共 IP 地址通常是一项专用资源，与其所分配的 VM 分开管理，因此，它在专用网络资源组中（而不是在 Service Fabric 群集资源组中）进行预配。  在同一个 *ExistingRG* 资源组中，通过 Azure 门户或 Powershell 创建一个名为“staticIP1”的静态公共 IP 地址：

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric 模板

在使用标准门户向导创建群集之前，我要使用可从门户下载的 Service Fabric template.json。 你也可以使用[模板库](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric)中的模板，例如[五节点 Service Fabric 群集](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/)。

<a id="existingvnet"></a>
## <a name="existing-virtual-networksubnet"></a>现有虚拟网络/子网

[2016 年 1 月 24 日：[https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet) 针对此部分提供了 Service Fabric 范围外的另一个示例]

1. 将子网参数更改为现有子网的名称，并添加两个新参数以引用现有的 VNet：

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. 将 *vnetID* 变量更改为指向现有 VNet：

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. 从 Resources 中删除 *Microsoft.Network/virtualNetworks*，使 Azure 不创建新的 VNet：

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. 从 *Microsoft.Compute/virtualMachineScaleSets* 的 *dependsOn*属性中注释掉 VNet，以便我们不依赖于创建新的 VNet：

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. 部署模板：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    部署完成后，虚拟网络应包括新的规模集 VM，而且虚拟机规模集节点类型应显示现有的 VNet 和子网。  你也可以使用 RDP 访问 VNet 中已有的 VM，并对新规模集 VM 执行 ping 操作：

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>静态公共 IP 地址

1. 添加现有静态 IP 资源组名称、名称和 FQDN 的相应参数：

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. 删除 *dnsName* 参数，因为静态 IP 已有名称：

    ```
    /*
    "dnsName": {
        "type": "string"
    }, 
    */
    ```

3. 添加变量以引用现有静态 IP：

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. 从 *Resources* 中删除 *Microsoft.Network/publicIPAddresses*，使 Azure 不创建新的 IP 地址：

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. 从 *Microsoft.Network/loadBalancers* 的 *dependsOn* 属性中注释掉 IP 地址，以便我们不依赖于创建新的 IP 地址：

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. 将 *Microsoft.Network/loadBalancers* 资源中 *frontendIPConfigurations* 的 *publicIPAddress* 元素从引用新建 IP 改为引用现有静态 IP：

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. 将 *Microsoft.ServiceFabric/clusters* 资源中的 *managementEndpoint* 更改为静态 IP 的 DNS FQDN。  **如果使用的是安全群集，请确保将 'http://' 更改为 'https://'。** （注意：此说明仅适用于 Service Fabric 群集。  如果使用虚拟机规模集，则跳过此步骤）：

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. 部署模板：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

部署完成后，你会看到负载均衡器绑定到其他资源组中的公共静态 IP 地址。 Service Fabric 客户端连接终结点和 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 终结点指向静态 IP 地址的 DNS FQDN。

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>仅限内部的负载均衡器

本方案用仅限内部的负载均衡器替代默认 Service Fabric 模板中的外部负载均衡器。  有关 Azure 门户和 SFRP 的含义，请参阅前述内容。

1. 删除 *dnsName* 参数，因为不需要该参数：

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. （可选）如果使用静态分配方法，则添加一个静态 IP 地址参数。 如果使用动态分配方法，则无需添加：

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. 从 Resources 中删除 *Microsoft.Network/publicIPAddresses*，使 Azure 不创建新的 IP 地址：

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. 从 *Microsoft.Network/loadBalancers* 的 *dependsOn* 属性中删除 IP 地址，以便我们不依赖于创建新的 IP 地址。  添加 VNet *dependsOn* 属性，因为负载均衡器现在依赖于 VNet 中的子网：

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. 将负载均衡器的 *frontendIPConfigurations* 从使用 *publicIPAddress* 改为使用子网和 *privateIPAddress*，后者使用预定义的静态内部 IP 地址。  可以使用动态 IP 地址，方法是删除 *privateIPAddress* 元素并将 *privateIPAllocationMethod* 更改为“Dynamic”。

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. 在 *Microsoft.ServiceFabric/clusters* 资源中，将 *managementEndpoint* 更改为指向内部负载均衡器地址。  **如果使用的是安全群集，请确保将 'http://' 更改为 'https://'。** （注意：此说明仅适用于 Service Fabric 群集。  如果使用虚拟机规模集，则跳过此步骤）：

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. 部署模板：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

部署完成后，负载均衡器使用私有静态 10.0.0.250 IP 地址。 如果同一 VNet 中还有其他计算机，则还可以浏览到内部 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 终结点，你会看到它与负载均衡器后的某个节点连接。

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>内部和外部负载均衡器

本方案使用现有的单节点类型外部负载均衡器，并额外添加一个相同节点类型的内部负载均衡器。  连接到后端地址池的后端端口只能分配给单个负载均衡器，因此必须确定哪个负载均衡器拥有应用程序端口，哪个负载均衡器拥有管理终结点（端口 19000/19080）。  如果决定将管理终结点放在内部负载均衡器上，请记住上文所提到的 SFRP 限制。  本示例将管理终结点放在外部负载均衡器上，并添加一个端口号为 80 的应用程序端口，放在内部负载均衡器上。

如果想要一个双节点类型群集，即，外部负载均衡器上使用一种节点类型，内部负载均衡器上使用另一种节点类型，只需采用门户创建的双节点类型模板（附带 2 个负载均衡器），并按照上文“仅限内部的负载均衡器”部分的说明，将第二个负载均衡器切换为内部负载均衡器。

1. 添加静态内部 LB IP 地址参数 （如果需要动态 IP 地址，请参阅上文的注释）：

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. 添加应用程序端口 80 参数：

3. 通过复制/粘贴并在命名中添加“-Int”，来添加现有网络变量的内部版本：

    ```
    /* Add internal LB networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* internal LB networking variables end */
    ```

4. 如果从使用应用程序端口 80、由门户生成的模板开始，则默认门户模板会在外部负载均衡器上添加 *AppPort1*（端口 80）。  在此情况下，请将其从外部负载均衡器 *loadBalancingRules* 和探测器中删除，以便将其添加到内部负载均衡器中：

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe", 
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,,
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ], 
    "inboundNatPools": [
    ```

5. 再添加一个 *Microsoft.Network/loadBalancers* 资源，它与上文[仅限内部的负载均衡器](#internallb)部分中创建的内部负载均衡器看起来非常相似，不过它使用的是“-Int”负载均衡器变量，并且仅实现应用程序端口 80。  这样做还会删除 *inboundNatPools*，以便将 RDP 终结点放在公共负载均衡器上 — 如果想将 RDP 放在内部负载均衡器中，则将 *inboundNatPools* 从外部负载均衡器移到此内部负载均衡器。

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" LB variables */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add '-Internal' to name */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
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
                        /* Add the AppPort rule, making sure to reference the "-Int" versions of the backendAddressPool, frontendIPConfiguration, and probe variables */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. 在 *Microsoft.Compute/virtualMachineScaleSets* 资源的 *networkProfile* 中，添加内部后端地址池：

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. 部署模板：

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

部署完成后，资源组中显示两个负载均衡器，如果浏览这两个负载均衡器，可以看到公共 IP 地址和分配给公共 IP 地址的管理终结点（端口 19000/19080）、静态内部 IP 地址和分配给内部负载均衡器的应用程序终结点（端口 80），还可以看到这两个负载均衡器使用同一个虚拟机规模集后端池。

## <a name="next-steps"></a>后续步骤
现在已经了解如何将 Service Fabric 群集与 Azure 网络功能集成，接下来开始[创建群集](service-fabric-cluster-creation-via-arm.md)。 

