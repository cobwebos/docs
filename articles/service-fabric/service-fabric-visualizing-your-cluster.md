<properties
   pageTitle="使用 Service Fabric 资源管理器可视化群集"
   description="Service Fabric 资源管理器是一个用于检验和管理 Microsoft Azure Service Fabric 群集中的云应用程序和节点的有用 GUI 工具。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>

# 使用 Service Fabric 资源管理器可视化群集

Service Fabric 资源管理器是一个用于检验和管理 Microsoft Azure Service Fabric 群集中的云应用程序和节点的可视工具。Service Fabric 资源管理器既可以连接到本地开发群集，也可以连接到 Azure 群集。有关 Service Fabric PowerShell cmdlet 的信息，请参阅**后续步骤**。

> [AZURE.NOTE]仍然不能在 Azure 中创建 Service Fabric 群集。

## Service Fabric 资源管理器介绍

确保你的本地开发环境已经按照[设置 Service Fabric 开发环境](/documentation/articles/service-fabric-get-started)中的说明进行了设置。

从你的本地安装路径 (%Program Files%\\Microsoft SDKs\\Service Fabric\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe) 运行 Service Fabric 资源管理器。如果存在本地开发群，该工具将自动连接到一个本地开发群集。它显示有关群集的信息，例如：

- 在群集上运行的应用程序
- 有关群集节点的信息
- 来自应用程序和节点的运行状况事件
- 群集中应用程序上的负载
- 监视应用程序升级状态

![Service Fabric 群集和已部署应用程序的可视化表示][servicefabricexplorer]

一个重要的可视化是群集映射，可在群集的仪表板上看到（例如单击 **Onebox/Local cluster**）。群集映射显示一组升级域和一组故障域，以及哪些节点映射到哪些域。请参阅 [Service Fabric 技术概述](/documentation/articles/service-fabric-technical-overview)以熟悉关键的 Service Fabric 概念。

![群集映射显示每个节点属于哪个升级域和故障域。][clustermap]


## 查看应用程序和服务

Service Fabric 资源管理器让你能够浏览在你的群集上运行的应用程序。展开**应用程序视图**可查看有关你的应用程序、服务、分区和副本的详细信息。

下图显示名为**“fabric:/Stateful1Application”**的应用程序有一个名为**“fabric:/Stateful1Application/MyFrontEnd”**的无状态服务和一个名为**“fabric:/Stateful1Application/Stateful1”**的有状态服务。无状态服务有一个分区，该分区有一个在节点 **Node.4** 上运行的副本。有状态服务有两个分区，每个分区有 3 个运行在不同节点上的副本。

![在 Service Fabric 群集上运行的应用程序的视图][applicationview]

单击一个应用程序、服务、分区或副本可显示有关该实体的详细信息。下图显示有状态服务的一个主副本的服务副本运行状况仪表板。这包括其角色、运行所在的节点、侦听的地址、其文件在磁盘上的位置以及运行状况事件。

![有关 Service Fabric 副本的详细信息][replicadetails]


## 连接到远程 Service Fabric 群集

若要查看远程 Service Fabric 群集，请单击“连接”以打开“连接到 Service Fabric 群集”对话框。输入群集的“ServiceFabric 终结点”，然后单击“连接”。Service Fabric 终结点通常是侦听端口 19000 的群集服务的公共名称。

![设置到远程 Service Fabric 群集的连接][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

- [可测试性概述](/documentation/articles/service-fabric-testability-overview)。
- [在 Visual Studio 中管理 Service Fabric 应用程序](/documentation/articles/service-fabric-manage-application-in-visual-studio)。
- [使用 PowerShell 部署 Service Fabric 应用程序](/documentation/articles/service-fabric-deploy-remove-applications)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png

<!---HONumber=74-->