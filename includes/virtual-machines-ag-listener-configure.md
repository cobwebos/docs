---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 276ddf0a70fa450451cd3ddc78c7610c4ab1edc1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165781"
---
可用性组侦听器是 SQL Server 可用性组侦听的 IP 地址和网络名称。 若要创建可用性组侦听器，请执行以下操作：

1. <a name="getnet"></a>获取群集网络资源的名称。

    a. 使用 RDP 连接到托管主副本的 Azure 虚拟机。 

    b. 打开故障转移群集管理器。

    c. 选择“网络”节点，并记下群集网络名称。  将在 PowerShell 脚本的 `$ClusterNetworkName` 变量中使用此名称。 在下图中，群集网络名称为“群集网络 1”： 

   ![群集网络名称](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>添加客户端接入点。  
    客户端接入点是应用程序用来连接到可用性组中数据库的网络名称。 可在故障转移群集管理器中创建客户端接入点。

    a. 展开群集名称，并单击“角色”。 

    b. 在“角色”窗格中，右键单击可用性组名称，并选择“添加资源” > “客户端接入点”。   

   ![客户端接入点](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. 在“名称”框中，创建新侦听器的名称。  
   新侦听器的名称是应用程序用来连接 SQL Server 可用性组中数据库的网络名称。

    d. 要完成创建侦听器，请单击“下一步”两次，并单击“完成”。   不要在此时使侦听器或资源联机。

1. 使可用性组群集角色脱机。 在“故障转移群集管理器”  的“角色”  下，右键单击角色，然后选择“停止角色”  。

1. <a name="congroup"></a>配置可用性组的 IP 资源。

    a. 单击“资源”选项卡，并展开创建的客户端接入点。   
    客户端接入点处于脱机状态。

   ![客户端接入点](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. 右键单击 IP 资源，并单击“属性”。 记下 IP 地址的名称，将其用在 PowerShell 脚本的 `$IPResourceName` 变量中。

    c. 在“IP 地址”下面，单击“静态 IP 地址”。   将 IP 地址设置为在 Azure 门户中设置负载均衡器地址时所用的同一地址。

   ![IP 资源](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>使 SQL Server 可用性组资源依赖于客户端接入点。

    a. 在故障转移群集管理器中，单击“角色”  ，并单击可用性组。

    b. 在“资源”  选项卡的“其他资源”下，右键单击可用性资源组，并单击“属性”   。 

    c. 在依赖关系选项卡上，添加客户端接入点（侦听器）资源的名称。

   ![IP 资源](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. 单击“确定”。 

1. <a name="listname"></a>使客户端接入点资源依赖于 IP 地址。

    a. 在故障转移群集管理器中，单击“角色”  ，并单击可用性组。 

    b. 在“资源”选项卡中，右键单击“服务器名称”下面的客户端接入点资源，并单击“属性”。    

   ![IP 资源](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. 选择“依赖项”选项卡。  验证 IP 地址是否为依赖项。 如果不是，则设置 IP 地址的依赖项。 如果有多个资源列出，请验证 IP 地址具有 OR 而不是 AND 依赖项。 单击“确定”。  

   ![IP 资源](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >可以验证是否正确配置了依赖项。 在故障转移群集管理器中，转到“角色”，右键单击可用性组，单击“更多操作”  ，然后单击“显示依赖项报告”  。 正确配置依赖项后，可用性组将依赖于网络名称，网络名称将依赖于 IP 地址。 


1. <a name="setparam"></a>在 PowerShell 中设置群集参数。

   a. 将以下 PowerShell 脚本复制到某个 SQL Server 实例。 请更新环境的变量。

   - `$ListenerILBIP` 是在 Azure 负载均衡器上为可用性组侦听程序创建的 IP 地址。
    
   - `$ListenerProbePort` 是在 Azure 负载均衡器上为可用性组侦听程序配置的端口。

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. 通过在某个群集节点上运行 PowerShell 脚本设置群集参数。  

   > [!NOTE]
   > 如果 SQL Server 实例位于不同的区域，则需要运行 PowerShell 脚本两次。 第一次运行时，请从第一个区域中选择 `$ListenerILBIP` 和 `$ListenerProbePort`。 第二次运行时，请从第二个区域中选择 `$ListenerILBIP` 和 `$ListenerProbePort`。 每个区域的群集网络名称和群集 IP 资源名称也不同。

1. 使可用性组群集角色联机。 在“故障转移群集管理器”  的“角色”  下，右键单击角色，然后选择“启动角色”  。

如有必要，重复上述步骤以设置 WSFC 群集 IP 地址的群集参数。

1. 获取 WSFC 群集 IP 地址的 IP 地址名称。 在“故障转移群集管理器”  中的“群集核心资源”  下，找到“服务器名称”  。

1. 右键单击“IP 地址”  ，并选择“属性”  。

1. 记下 IP 地址的**名称**。 该项可能为 `Cluster IP Address`。 

1. <a name="setwsfcparam"></a>在 PowerShell 中设置群集参数。
  
   a. 将以下 PowerShell 脚本复制到某个 SQL Server 实例。 请更新环境的变量。

   - `$ClusterCoreIP` 是在 Azure 负载均衡器上为 WSFC 核心群集资源创建的 IP 地址。 它不同于可用性组侦听程序的 IP 地址。

   - `$ClusterProbePort` 是在 Azure 负载均衡器上为 WSFC 运行状况探测配置的端口。 它不同于可用性组侦听程序的探测。

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. 通过在某个群集节点上运行 PowerShell 脚本来设置群集参数。  

>[!WARNING]
>可用性组侦听程序运行状况探测端口必须不同于群集核心 IP 地址运行状况探测端口。 在这些示例中，侦听程序端口为 59999，群集核心 IP 地址为 58888。 这两个端口都要求允许入站防火墙规则。