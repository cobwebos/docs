---
title: "在 Azure 中创建 SAP 多 SID 配置 | Microsoft 文档"
description: "Windows 虚拟机上的高可用性 SAP NetWeaver 多 SID 配置指南"
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b48df78df9f53ac7bf0804f55a8d36a2fe2f86b4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>创建 SAP NetWeaver 多 SID 配置

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

2016 年 9 月，Microsoft 推出了一项功能，可用于通过 Azure 内部负载均衡器管理多个虚拟 IP 地址。 Azure 外部负载均衡器已包含此功能。

在 SAP 部署中，可以根据 [Windows VM 上的高可用性 SAP NetWeaver 指南][sap-ha-guide]中所述，使用内部负载均衡器创建 SAP ASCS/SCS 的 Windows 群集配置。

本文重点介绍如何通过在现有 Windows Server 故障转移群集 (WSFC) 中安装附加的 SAP ASCS/SCS 群集实例，从单一 ASCS/SCS 安装转移到 SAP 多 SID 配置。 完成此过程后，即已配置一个 SAP 多 SID 群集。


## <a name="prerequisites"></a>先决条件
已根据 [Windows VM 上的高可用性 SAP NetWeaver 指南][sap-ha-guide]中的介绍和下图所示，配置了用于一个 SAP ASCS/SCS 实例的 WSFC 群集。

![高可用性 SAP ASCS/SCS 实例][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>目标体系结构

目标是在同一个 WSFC 群集中安装多个 SAP ABAP ASCS 或 SAP Java SCS 群集实例，如下图所示：

![Azure 中有多个 SAP ASCS/SCS 群集实例][sap-ha-guide-figure-6002]

> [!NOTE]
>每个 Azure 内部负载均衡器的专用前端 IP 数有限制。
>
>一个 WSFC 群集中的最大 SAP ASCS/SCS 实例数等于每个 Azure 内部负载均衡器的最大专用前端 IP 数。
>

有关负载均衡器限制的详细信息，请参阅 [Networking Limits: Azure Resource Manager][networking-limits-azure-resource-manager]（网络限制：Azure Resource Manager）中的“Private front end IP per load balancer”（每个负载均衡器的专用前端 IP）。

包含两个高可用性 SAP 系统的完整布局如下所示：

![包含两个 SAP 系统 SID 的 SAP 高可用性多 SID 设置][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> 该设置必须满足以下条件：
> - SAP ASCS/SCS 实例必须共享同一个 WSFC 群集。
> - 每个 DBMS SID 必须有自身的专用 WSFC 群集。
> - 属于一个 SAP 系统 SID 的 SAP 应用程序服务器必须有自身的专用 VM。


## <a name="prepare-the-infrastructure"></a>准备基础结构
若要准备基础结构，可以使用以下参数安装附加的 SAP ASCS/SCS 实例：

| 参数名称 | 值 |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| SAP DBMS 内部负载均衡器 | PR5 |
| SAP 虚拟主机名 | pr5-sap-cl |
| SAP ASCS/SCS 虚拟主机 IP 地址（附加的 Azure 负载均衡器IP 地址） | 10.0.0.50 |
| SAP ASCS/SCS 实例编号 | 50 |
| 附加 SAP ASCS/SCS 实例的 ILB 探测端口 | 62350 |

> [!NOTE]
> 对于 SAP ASCS/SCS 群集实例，每个 IP 地址需要唯一的探测端口。 例如，如果 Azure 内部负载均衡器上有一个 IP 地址使用探测端口 62300，该负载均衡器上的其他任何 IP 地址就不能使用探测端口 62300。
>
>在本例中，由于探测端口 62300 已被保留，因此我们将使用探测端口 62350。

可在现有的 WSFC 群集中安装包含两个节点的附加 SAP ASCS/SCS 实例：

| 虚拟机角色 | 虚拟机主机名 | 静态 IP 地址 |
| --- | --- | --- |
| ASCS/SCS 实例的第 1 个群集节点 |pr1-ascs-0 |10.0.0.10 |
| ASCS/SCS 实例的第 2 个群集节点 |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>在 DNS 服务器上创建 SAP ASCS/SCS 群集实例的虚拟主机名

可以使用以下参数创建 ASCS/SCS 实例虚拟主机名的 DNS 项：

| 新的 SAP ASCS/SCS 虚拟主机名 | 关联的 IP 地址 |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

新主机名和 IP 地址显示在 DNS 管理器中，如以下屏幕截图所示：

![DNS 管理器列表，其中突出显示了新 SAP ASCS/SCS 群集虚拟名称和 TCP/IP 地址的已定义 DNS 项][sap-ha-guide-figure-6004]

有关 DNS 项的详细创建过程，请参阅主要文档 [Windows VM 上的高可用性 SAP NetWeaver 指南][sap-ha-guide-9.1.1]。

> [!NOTE]
> 分配给附加 ASCS/SCS 实例虚拟主机名的新 IP 地址必须与分配给 SAP Azure 负载均衡器的新 IP 地址相同。
>
>在本例中，该 IP 地址为 10.0.0.50。

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>使用 PowerShell 将 IP 地址添加到现有 Azure 内部负载均衡器

要在同一个 WSFC 群集中创建多个 SAP ASCS/SCS 实例，请使用 PowerShell 将 IP 地址添加到现有 Azure 内部负载均衡器。 每个 IP 地址需有自身的负载均衡规则、探测端口、前端 IP 池和后端池。

以下脚本将新的 IP 地址添加到现有负载均衡器。 更新环境的 PowerShell 变量。 该脚本将为所有 SAP ASCS/SCS 端口创建全部所需的负载均衡规则。

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Succesfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
运行脚本后，结果会显示在 Azure 门户中，如以下屏幕截图所示：

![Azure 门户中的新前端 IP 池][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>将磁盘添加到群集计算机并配置 SIOS 群集共享磁盘

必须为每个附加的 SAP ASCS/SCS 实例添加一个新的群集共享磁盘。 在 Windows Server 2012 R2 中，目前使用的 WSFC 群集共享磁盘是 SIOS DataKeeper 软件解决方案。

请执行以下操作：
1. 将相同大小的一个或多个附加磁盘（需要条带化）添加到每个群集节点，并将其格式化。
2. 使用 SIOS DataKeeper 配置存储复制。

此过程假设已在 WSFC 群集计算机上安装 SIOS DataKeeper。 如果已安装，现在必须配置计算机之间的复制。 有关此过程的详细信息，请参阅主要文档 [Windows VM 上的高可用性 SAP NetWeaver 指南][sap-ha-guide-8.12.3.3]。  

![新 SAP ASCS/SCS 共享磁盘的 DataKeeper 同步镜像][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>为 SAP 应用程序服务器和 DBMS 群集部署 VM

若要完成第二个 SAP 系统的基础结构准备，请执行以下操作：

1. 为 SAP 应用程序服务器部署专用 VM，并将这些 VM 放在其自身的专用可用性组中。
2. 为 DBMS 群集部署专用 VM，并将这些 VM 放在其自身的专用可用性组中。


## <a name="install-the-second-sap-sid2-netweaver-system"></a>安装第二个 SAP SID2 NetWeaver 系统

有关第二个 SAP SID2 系统的完整安装过程，请参阅主要文档 [Windows VM 上的高可用性 SAP NetWeaver 指南][sap-ha-guide-9]。

概要过程如下所述：

1. [安装 SAP 第一个群集节点][sap-ha-guide-9.1.2]。  
 此步骤在**现有 WSFC 群集节点 1** 上安装包含高可用性 ASCS/SCS 实例的 SAP 系统。

2. [修改 ASCS/SCS 实例的 SAP 配置文件][sap-ha-guide-9.1.3]。

3. [配置探测端口][sap-ha-guide-9.1.4]。  
 此步骤使用 PowerShell 配置 SAP 群集资源 SAP-SID2-IP 探测端口。 应在其中一个 SAP ASCS/SCS 群集节点上执行此配置。

4. [安装数据库实例][sap-ha-guide-9.2]。  
 此步骤在专用 WSFC 群集上安装 DBMS。

5. [安装第二个群集节点][sap-ha-guide-9.3]。  
 此步骤在现有 WSFC 群集节点 2 上安装包含高可用性 ASCS/SCS 实例的 SAP 系统。

6. 打开 SAP ASCS/SCS 实例的 Windows 防火墙端口和探测端口。  
 在用于 SAP ASCS/SCS 实例的两个群集节点上，打开 SAP ASCS/SCS 使用的所有 Windows 防火墙端口。 有关这些端口，请参阅 [Windows VM 上的高可用性的 SAP NetWeaver 指南][sap-ha-guide-8.8]。  
 此外，打开 Azure 内部负载均衡器探测端口，在本例中为 62350。

7. [更改 SAP ERS Windows 服务实例的启动类型][sap-ha-guide-9.4]。

8. 在新的专用 VM 上[安装 SAP 主应用程序服务器][sap-ha-guide-9.5]。

9. 在新的专用 VM 上[安装 SAP 附加应用程序服务器][sap-ha-guide-9.6]。

10. [测试 SAP ASCS/SCS 实例故障转移和 SIOS 复制][sap-ha-guide-10]。

## <a name="next-steps"></a>后续步骤

- [网络限制：Azure Resource Manager][networking-limits-azure-resource-manager]
- [Azure 负载均衡器的多个 VIP][load-balancer-multivip-overview]
- [Windows VM 上的高可用性 SAP NetWeaver 指南][sap-ha-guide]
