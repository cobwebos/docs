<properties title="Load Balancing for Azure Infrastructure Services" pageTitle="负载平衡以 Azure 基础结构服务" description="描述要执行负载平衡与 Traffic Manager 和负载平衡器的功能。" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="cherylmc" videoId="" scriptId="" manager="adinah" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="cherylmc" />

#负载平衡 Azure 基础结构服务 #

有两个级别的负载平衡可用于 Azure 基础结构服务：

- **DNS 级别**：负载平衡到不同云服务的流量 （位于不同的数据中心），到位于不同的数据中心中的不同 Azure 网站或外部终结点。这是与流量管理器和轮循机制负载平衡方法。
- **网络级别**：负载平衡的 Internet 流量传入不同的虚拟机的云服务或负载平衡的云服务或虚拟网络中的虚拟机之间的通信。这是通过 Azure 负载平衡器。

##Traffic Manager 负载平衡，用于云服务和网站 # #

使用 azure Traffic Manager 可以控制到终结点，其中可能包括云服务、 网站、 外部站点和其他 Traffic Manager 配置文件的用户流量分布。Traffic Manager 工作方式是将智能策略引擎应用于对 Internet 资源域名的域名系统 (DNS) 查询。您的云服务或网站可以运行在不同的数据中心在世界各地。 

必须使用 REST 或 Windows PowerShell 来配置外部终结点或 Traffic Manager 配置文件作为终结点。 

Azure Traffic Manager 使用三种负载平衡方法来分配流量：

- **故障转移**：如果您想要对所有流量使用主终结点，但在主变得不可用时提供备份，请使用此方法。
- **性能**：当在不同地理位置具有终结点并且您想请求客户端使用"最近的"终结点 （因为最低的延迟，请使用此方法。
- **轮循机制：**时您想要将负载分布到一组云服务在同一数据中心内或整个云服务或不同数据中心内的网站使用此方法。

有关详细信息，请参阅[有关 Traffic Manager 负载平衡方法](http://msdn.microsoft.com/zh-cn/library/azure/dn339010.aspx)。

下图显示的轮循机制负载平衡方法用于分发的不同云服务之间的通信的一个示例。

![loadbalancing](./media/load-balancing-vms/TMSummary.png)

以下是基本过程：

1.	Internet 客户端查询对应于 web 服务的域名。
2.	DNS 将名称查询请求转发到 Traffic Manager。
3.	Traffic Manager 将返回发送轮循机制列表中的云服务的 DNS 名称。Internet 客户端的 DNS 服务器将名称解析为 IP 地址，并将其发送到 Internet 客户端。
4.	Internet 客户端将与所选的云服务连接。

有关详细信息，请参阅[Traffic Manager](http://msdn.microsoft.com/zh-cn/library/azure/hh745750.aspx)。

## Azure 负载平衡的虚拟机 # #

在同一个虚拟机云服务或虚拟网络可以直接使用其专用 IP 地址相互通信。计算机和云服务或虚拟网络外部的服务只能与云服务或与已配置的终结点的虚拟网络中的虚拟机通信。终结点是公用 IP 地址和端口以连接到该专用的 IP 地址的映射和虚拟机或 Azure 云服务中的 web 角色的端口。

Azure 负载平衡器随机分布在多个虚拟机或服务在配置中称为负载平衡集的特定类型的传入流量。例如，您可以将 web 请求流量负载分布在多个 web 服务器或 web 角色。

下图显示的公钥和私钥的 TCP 端口为 80 的三个虚拟机之间共享的标准 （未加密） 的 web 流量负载平衡的终结点。这三个虚拟机位于负载平衡集。

![loadbalancing](./media/load-balancing-vms/LoadBalancing.png)

有关详细信息，请参阅[Azure 负载平衡器](http://msdn.microsoft.com/zh-cn/library/azure/dn655058.aspx)。若要创建的负载平衡集的步骤，请参阅[配置负载平衡集](http://msdn.microsoft.com/zh-cn/library/azure/dn655055.aspx)。

Azure 还可以加载的云服务或虚拟网络中的平衡。这称为内部负载平衡，并可以通过以下方式使用：

- 多层应用程序 （例如，在 web 层和数据库层） 之间的不同层中的服务器之间进行负载平衡。
- 负载平衡而无需额外的负载平衡器硬件或软件托管在 Azure 中的业务线 (LOB) 应用程序。 
- 包括的一组其流量进行负载平衡的计算机中的在本地服务器。

类似于 Azure 负载平衡，可以将内部负载平衡方法来促进通过配置内部负载平衡集。 

下图显示的内部负载平衡终结点线在三个虚拟机之间共享在跨界虚拟网络中的业务 (LOB) 应用程序的一个示例。 

![loadbalancing](./media/load-balancing-vms/LOBServers.png)

有关详细信息，请参阅[内部负载平衡](http://msdn.microsoft.com/zh-cn/library/azure/dn690121.aspx)。若要创建的负载平衡集的步骤，请参阅[配置内部负载平衡集](http://msdn.microsoft.com/zh-cn/library/azure/dn690125.aspx)。

<!-- LINKS -->

