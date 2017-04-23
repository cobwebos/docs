---
title: "设置 Azure Service Fabric 群集 | Microsoft Docs"
description: "快速入门 - 在 Azure 上创建 Windows 或 Linux Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 813217567c7e1fa5fc9ac11492933ad0c34553d1
ms.lasthandoff: 04/19/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>在 Azure 上创建你的第一个 Service Fabric 群集
[Service Fabric 群集](service-fabric-deploy-anywhere.md)是一组通过网络连接在一起的虚拟机或物理计算机，你的微服务将在其中部署和管理。 本快速入门可帮助你在数分钟内通过 [Azure 门户](http://portal.azure.com)创建一个运行在 Windows 或 Linux 上的五节点型群集。  

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登录 Azure
通过 [http://portal.azure.com](http://portal.azure.com) 登录到 Azure 门户。

## <a name="create-the-cluster"></a>创建群集

1. 单击 Azure 门户左上角的“新建”按钮。
2. 从“新建”边栏选项卡选择“计算”，然后从“计算”边栏选项卡选择“Service Fabric 群集”。
3. 填充 Service Fabric 的“基本信息”表单。 对于“操作系统”，请选择想要群集节点运行的 Windows 或 Linux 版本。 在此处输入的用户名和密码用于登录到虚拟机。 对于“资源组”，请创建一个新的资源组。 资源组是在其中创建并集中管理 Azure 资源的逻辑容器。 完成后，单击“确定”。

    ![群集设置输出][cluster-setup-basics]

4. 填充“群集配置”表单。  对于“节点类型”，请输入“1”，而对于[耐久性层](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)，请将其设置为“Bronze”。

5. 选择“配置每个节点类型”并填充“节点类型配置”表单。 节点类型定义 VM 大小、VM 数、自定义终结点，以及该类型 VM 的其他设置。 定义的每个节点类型均作为独立的虚拟机规模集设置，用于以集的形式部署和管理虚拟机。 每个节点类型可以独立扩展或缩减、打开不同的端口集，并且可以有不同的容量指标。  第一个节点类型（主节点类型）用于托管 Service Fabric 系统服务，必须包含至少五个 VM。

    对于任何生产部署，[容量规划](service-fabric-cluster-capacity.md)都是一个重要的步骤。  但就此快速入门来说，你不会运行应用程序，因此请选择“DS1_v2 Standard”VM 大小。  请选择“Silver”作为[可靠性层](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)，并选择 5 作为初始虚拟机规模集容量。  

    自定义终结点会在 Azure 负载均衡器中打开端口，允许你通过群集上运行的应用程序进行连接。  输入“80, 8172”打开端口 80 和 8172。

    请勿勾选“配置高级设置”框，该框用于自定义 TCP/HTTP 管理终结点、应用程序端口范围、[放置约束](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints)和[容量属性](service-fabric-cluster-resource-manager-metrics.md)。    

    选择“确定”。

6. 在“群集配置”表单中，将“诊断”设置为“开”。  就本快速入门来说，不需输入任何 [Fabric 设置](service-fabric-cluster-fabric-settings.md)属性。  在“Fabric 版本”中，选择“自动”升级模式，让 Microsoft 自动更新运行群集的 Fabric 代码的版本。  若要[选择可以升级到的受支持的版本](service-fabric-cluster-upgrade.md)，请将模式设置为“手动”。 

    ![节点类型配置][node-type-config]

    选择“确定”。

7. 填充“安全性”表单。  就本快速入门来说，请选择“不安全”。  但是，强烈建议为生产型工作负荷创建安全群集，因为任何人都可以匿名连接到不安全的群集并执行管理操作。  

    证书在 Service Fabric 中用于提供身份验证和加密，为群集及其应用程序提供全方位的保护。 若要详细了解如何在 Service Fabric 中使用证书，请参阅 [Service Fabric 群集安全方案](service-fabric-cluster-security.md)。  若要允许使用 Azure Active Directory 进行用户身份验证，或者要设置证书以确保应用程序安全性，请[从 Resource Manager 模板创建群集](service-fabric-cluster-creation-via-arm.md)。

    选择“确定”。

8. 查看摘要。  若要下载根据输入的设置生成的 Resource Manager 模板，请选择“下载模板和参数”。  选择“创建”可创建群集。

    可以在通知栏中查看群集创建进度。 （单击屏幕右上角状态栏附近的铃铛图标）。如果在创建群集时曾经单击“固定到启动板”，则会看到“部署 Service Fabric 群集”已固定到“启动”板。

## <a name="view-cluster-status"></a>查看群集状态
创建群集后，即可在门户的“概览”边栏选项卡中检查群集。 现在，仪表板会显示群集的详细信息，包括群集的公共终结点和 Service Fabric Explorer 的链接。

![群集状态][cluster-status]

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 可视化群集
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 是一项很好的工具，适用于可视化群集和管理应用程序。  Service Fabric Explorer 是在群集中运行的一项服务。  可以使用 Web 浏览器对其进行访问，方法是在门户中单击群集“概览”页的“Service Fabric Explorer”链接。  也可直接在浏览器中输入以下地址：[http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer)

群集仪表板提供了群集的概览，包括应用程序和节点运行状况的摘要。 节点视图显示群集的物理布局。 对于给定的节点，你可以检查已在该节点上部署代码的应用程序。

![Service Fabric Explorer][service-fabric-explorer]

## <a name="connect-to-the-cluster-using-powershell"></a>使用 PowerShell 连接到群集
使用 PowerShell 进行连接，验证群集是否正在运行。  ServiceFabric PowerShell 模块与 [Service Fabric SDK](service-fabric-get-started.md) 一起安装。  [Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster) cmdlet 可建立到群集的连接。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
有关如何连接到群集的其他示例，请参阅[连接到安全群集](service-fabric-connect-to-secure-cluster.md)。 连接到群集以后，请使用 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) cmdlet 显示群集中节点的列表，以及每个节点的状态信息。 每个节点的 **HealthState** 应该为“正常”。

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

## <a name="remove-the-cluster"></a>删除群集
Service Fabric 群集由群集资源本身以及其他 Azure 资源组成。 因此，若要彻底删除 Service Fabric 群集，还需删除组成该群集的所有资源。 若要删除群集及其所有资源，最简单的方式是删除资源组。 若要了解如何通过其他方式删除群集，或者删除资源组中的部分（而非全部）资源，请参阅[删除群集](service-fabric-cluster-delete.md)

在 Azure 门户中删除资源组：
1. 导航到要删除的 Service Fabric 群集。
2. 在群集基本信息页上单击**资源组**名称。
3. 在“资源组基本信息”页中单击“删除”，然后按照该页上的说明进行操作，以完成资源组的删除。
    ![删除资源组][cluster-delete]

## <a name="next-steps"></a>后续步骤
设置单独的开发群集以后，可尝试以下操作：
* [在门户中创建安全群集](service-fabric-cluster-creation-via-portal.md)
* [从模板创建群集](service-fabric-cluster-creation-via-arm.md) 
* [使用 PowerShell 部署应用](service-fabric-deploy-remove-applications.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
