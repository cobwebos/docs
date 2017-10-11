---
title: "为 Alwayson 可用性组配置外部侦听器 |Microsoft 文档"
description: "本教程将指导你完成在 Azure 中使用关联的云服务的公共虚拟 IP 地址是从外部访问创建始终在可用性组侦听器的步骤。"
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 8e506be42aea4fb3c48c29b771a78dcf694f4518
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>在 Azure 中配置 Alwayson 可用性组的外部侦听器
> [!div class="op_single_selector"]
> * [内部侦听器](../classic/ps-sql-int-listener.md)
> * [外部侦听器](../classic/ps-sql-ext-listener.md)
> 
> 

本主题演示如何为 Alwayson 可用性组可以在 internet 上从外部访问配置侦听器。 这可通过将云服务相关联**公共虚拟 IP (VIP)**地址与侦听器。

> [!IMPORTANT] 
> Azure 具有用于创建和使用资源的两个不同的部署模型：[资源管理器和经典](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

可用性组可以包含的副本，仅，本地仅限，Azure 或跨越本地和 Azure 以实现混合配置。 Azure 副本可以位于同一区域内或跨越使用多个虚拟网络 (Vnet) 的多个区域。 下面的步骤假定你已具有[配置了一个可用性组](../classic/portal-sql-alwayson-availability-groups.md)但是没有配置侦听器。

## <a name="guidelines-and-limitations-for-external-listeners"></a>指导原则和限制的外部侦听器
部署使用云服务公共 VIP 地址时，请注意有关在 Azure 中的可用性组侦听器的以下准则：

* Windows Server 2008 R2、 Windows Server 2012 和 Windows Server 2012 R2 支持可用性组侦听器。
* 客户端应用程序必须位于与包含你的可用性组 Vm 的不同云服务。 Azure 不支持同一云服务中的客户端和服务器的直接服务器返回。
* 默认情况下，这篇文章中的步骤显示如何配置一个侦听器，以使用云服务的虚拟 IP (VIP) 地址。 但是，很可能保留并创建你的云服务的多个 VIP 地址。 这使您要使用此文章中的步骤来创建多个不同的 VIP 与每个关联的侦听器。 有关如何创建多个 VIP 地址的信息，请参阅[每个云服务的多个 Vip](../../../load-balancer/load-balancer-multivip.md)。
* 如果要创建的混合环境的侦听器，在本地网络必须连接到公共 Internet，还使用 Azure 虚拟网络站点到站点 VPN。 在 Azure 子网，可用性组侦听器是只能通过相应云服务的公共 IP 地址来访问。
* 它不支持你还可以使用内部负载平衡器 (ILB) 的内部侦听器同一云服务中创建的外部侦听器。

## <a name="determine-the-accessibility-of-the-listener"></a>确定侦听器的可访问性
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

本文重点介绍在创建使用侦听器**外部负载平衡**。 如果你想要为专用于虚拟网络的侦听器，请参阅这篇文章提供了步骤设置的版本[使用 ILB 的侦听器](../classic/ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>创建直接服务器返回负载平衡 VM 终结点
外部负载平衡使用虚拟托管 Vm 的云服务的公共虚拟 IP 地址。 因此，在这种情况下，你不需要创建或配置负载均衡器。

必须为每个托管 Azure 副本的 VM 创建负载平衡的终结点。 如果你有多个区域中的副本，该区域的每个副本必须位于同一 VNet 中相同的云服务中。 跨越多个 Azure 区域的创建可用性组副本需要配置多个 Vnet。 有关配置跨 VNet 连接的详细信息，请参阅[配置 VNet 到 VNet 连接](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

1. 在 Azure 门户中，导航到承载副本的每个 VM 和查看的详细信息。
2. 单击**终结点**为每个 Vm 的选项卡。
3. 验证**名称**和**公用端口**侦听器你想要使用的终结点是否已被使用。 在下面的示例中，名称为"MyEndpoint"，端口为"1433"。
4. 在本地客户端，下载并安装[最新的 PowerShell 模块](https://azure.microsoft.com/downloads/)。
5. 启动**Azure PowerShell**。 其中加载了 Azure 管理模块将打开一个新的 PowerShell 会话。
6. 运行**Get-azurepublishsettingsfile**。 此 cmdlet 将引导你浏览器以将发布设置文件下载到本地目录。 你可能会提示你登录的凭据为你的 Azure 订阅。
7. 运行**Import-azurepublishsettingsfile**命令以及你下载发布设置文件的路径：
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    一旦导入发布设置文件，你可以管理你的 PowerShell 会话中的 Azure 订阅。
    
1. 将以下 PowerShell 脚本复制到文本编辑器并设置变量的值，以根据你的环境 （默认值为某些参数已提供）。 请注意，是否你的可用性组跨 Azure 区域，你必须运行该脚本一次对云服务和驻留在该数据中心中的节点的每个数据中心中。
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. 一旦设置变量后，将脚本复制到你的 Azure PowerShell 会话的文本编辑器，来运行它从。 如果提示符仍然显示 >>，按 ENTER，以确保脚本开始运行。

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>验证已安装 KB2854082，如有必要
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>可用性组节点中，打开防火墙端口
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>创建可用性组侦听器

在两个步骤中创建可用性组侦听器。 首先，创建客户端访问点的群集资源，并配置依赖关系。 其次，使用 PowerShell 配置的群集资源。

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>创建客户端访问点和配置群集依赖关系
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>在 PowerShell 中配置的群集资源
1. 对于外部负载平衡，你必须获取包含副本的云服务的公共虚拟 IP 地址。 登录到 Azure 门户。 导航到包含你的可用性组 VM 的云服务。 打开**仪表板**视图。
2. 请注意下显示的地址**公共虚拟 IP (VIP) 地址**。 如果解决方案跨 Vnet，请为包含虚拟机承载副本的每个云服务重复此步骤。
3. 在其中一个 Vm，将以下 PowerShell 脚本复制到文本编辑器，并将变量设置为你前面记下的值。
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. 一次你设置变量，打开提升的 Windows PowerShell 窗口，然后从文本编辑器复制脚本并粘贴到你的 Azure PowerShell 会话中运行它。 如果提示符仍然显示 >>，按 ENTER，以确保脚本开始运行。
5. 每个 VM 上重复此过程。 此脚本使用云服务的 IP 地址配置的 IP 地址资源，同时设置探测端口等其他参数。 在 IP 地址资源上线后，它可以然后响应的探测端口上的轮询，在本教程前面创建的负载平衡的终结点。

## <a name="bring-the-listener-online"></a>使侦听器联机
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>后续操作项
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>测试可用性组侦听器 （在同一 VNet 中)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>测试可用性组侦听器 （通过 internet)
若要访问从虚拟网络外部侦听器，你必须使用外部/公共负载平衡 （如本主题所述） 而不 ILB，这在同一 VNet 中的唯一访问。 在连接字符串中，你指定的云服务名称。 例如，如果你必须具有名称的云服务*mycloudservice*，则 sqlcmd 语句将如下所示：

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

与前面的示例中，必须使用 SQL 身份验证，因为调用方无法通过 internet 使用 windows 身份验证。 有关详细信息，请参阅[Always On 可用性组 Azure VM 中： 客户端连接方案](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)。 当使用 SQL 身份验证，请确保这两个副本上创建相同的登录名。 有关排查可用性组登录问题的详细信息，请参阅[如何映射登录或使用包含 SQL 数据库用户连接到其他副本并映射到可用性数据库](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)。

如果 Alwayson 副本位于不同子网，则客户端必须指定**MultisubnetFailover = True**连接字符串中。 这会导致尝试并行连接到不同的子网中的副本。 请注意，这种情况下包括跨区域 Alwayson 可用性组部署。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

