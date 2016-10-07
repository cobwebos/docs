<properties
   pageTitle="在 Azure 安全中心进行安全运行状况监视 | Microsoft Azure"
   description="本文档介绍如何开始使用 Azure 安全中心的监视功能。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="yurid"/>

#在 Azure 安全中心进行安全运行状况监视
本文档介绍如何通过 Azure 安全中心的监视功能监视合规性。

##什么是安全运行状况监视？
通常可以认为，监视就是指观察并等待某个事件的发生，以便采取应对措施。安全监视是指制定前瞻性策略对资源进行审核，确定不符合组织标准或最佳规范的系统。

##监视安全运行状况
在用户为订阅的资源启用[安全策略](security-center-policies.md)以后，安全中心即可分析资源的安全性，确定可能的漏洞。网络配置信息可即刻提供，而虚拟机配置信息（例如安全更新状态和 OS 配置）可能需要至少一小时才能提供。可以在“资源安全运行状况”边栏选项卡中查看资源的安全状况以及任何问题。也可在“建议”边栏选项卡中查看此类问题的列表。

如需详细了解如何应用建议，请阅读 [Implementing security recommendations in Azure Security Center](security-center-recommendations.md)（实施 Azure 安全中心的安全建议）。

在“资源安全运行状况”磁贴中，可以监视资源的安全状况。在下面的示例中，可以看到许多严重性级别为“高”和“中”的需要注意的问题。启用的安全策略会影响受监视控件的类型。

![资源运行状况](./media/security-center-monitoring/security-center-monitoring-fig1-ga.png)

如果安全中心确定了一个需要解决的漏洞（例如 VM 缺少安全更新，或者子网没有[网络安全组](../virtual-network/virtual-networks-nsg.md)），则会将其列在此处。

###监视虚拟机
单击“资源安全运行状况”磁贴中的“虚拟机”时，“虚拟机”边栏选项卡就会打开，其中包含加载和预防步骤的更多详细信息，以及受安全中心监视的所有 VM 的列表，如下所示。

![缺少 VM 的系统更新](./media/security-center-monitoring/security-center-monitoring-fig2-ga.png)

- 加载步骤
- 虚拟机建议
- 虚拟机

每一部分都可以选择单个的选项，可以查看针对该问题的建议步骤的更多详细信息。以下部分将更详细地讲述这些方面。

#### 监视建议
此部分显示初始化后可以进行数据收集的 VM 的总数及其当前状态。所有 VM 都对数据收集进行初始化以后，这些 VM 即可接收安全中心的安全策略。单击此条目以后，“数据收集安装状态”边栏选项卡就会打开，此时可看到 VM 的名称，并可在“安装状态”栏中看到数据收集的当前状态，如下所示。

![初始化状态](./media/security-center-monitoring/security-center-monitoring-fig3-ga.png)


####虚拟机建议
此部分为每个受 Azure 安全中心监视的 VM 提供了一组建议。第一栏列出建议，第二栏列出受该建议影响的 VM 的总数，第三栏显示问题的严重性，如下所示。

![VM 建议](./media/security-center-monitoring/security-center-monitoring-fig4-ga.png)

> [AZURE.NOTE] “网络运行状况”边栏选项卡的“网络拓扑”列表中仅显示至少具有一个公共终结点的 VM。

每个建议都有一组操作，单击即可执行。例如，如果单击“缺少的系统更新”，“缺少的系统更新”边栏选项卡就会打开。其中会列出缺少修补程序的 VM，以及所缺失更新的严重级别，如下所示。

![缺少系统更新](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

“缺少的系统更新”边栏选项卡会显示一个包含以下信息的表：

- **虚拟机**：缺少更新的虚拟机的名称。
- **系统更新**：缺少的系统更新的数目。
- **上次扫描时间**：安全中心上次扫描 VM 以获取更新的时间。
- **状态**：该建议的当前状态：
	- **未解决**：建议尚未得到处理
	- **正在进行**：目前已将建议应用到相关资源，不需用户采取行动
	- **已解决**：建议的操作已完成（问题解决后，条目灰显）。
- **严重性**：描述该特定建议的严重性：
	- **高**：重要资源（应用程序、VM、网络安全组）存在漏洞，需要提请注意
	- **中**：需要采取非关键步骤或额外步骤来完成某个过程或消除某个漏洞
	- **低**：漏洞需要解决，但不需立即处理。（默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。）

若要查看建议的详细信息，请单击 VM 的名称。此时会为该 VM 打开新的边栏选项卡，其中包含更新的列表，如下所示。

![缺少根据 VM 提供的系统更新](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [AZURE.NOTE] 此处提供的安全建议与“建议”边栏选项卡中的相同。如需详细了解如何解决这些建议所涉及的问题，请参阅 [Implementing security recommendations in Azure Security Center](security-center-recommendations.md)（实施 Azure 安全中心的安全建议）一文。这不仅适用于 VM，而且适用于“资源运行状况”磁贴中提供的所有资源。

####虚拟机部分
虚拟机部分概述所有 VM 和建议。每一栏代表一组建议，如下所示：

![VM](./media/security-center-monitoring/security-center-monitoring-fig7-ga.png)

每个建议下都会显示一个图标，用于快速确定需要关注的 VM 以及建议的类型。

在上面的示例中，一个 VM 有一条关于终结点保护的关键建议。单击即可获取该 VM 的详细信息。此时会打开一个新的代表此 VM 的边栏选项卡，如下所示。

![VM 安全详细信息](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

此边栏选项卡提供 VM 的安全详细信息。在此边栏选项卡的底部，可以看到建议的操作以及每个问题的严重性。

#### 云服务（预览版）部分
虚拟机安全运行状况磁贴包含云服务的运行状况。OS 版本过时时，会创建建议，如下所示：

![云服务](./media/security-center-monitoring/security-center-monitoring-fig8-new2.png)

必须按建议中的步骤更新 OS 版本。例如，如果单击其中一个 Web 角色（运行 Windows Server，将 Web 应用自动部署到 IIS）或辅助角色（运行 Windows Server，将 Web 应用自动部署到 IIS）中的红色警报，将会打开一个新的边栏选项卡，其中包含此建议的更多详细信息，如下所示：

![云服务详细信息](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

若要查看此建议的更规范性的说明，请单击“说明”栏下的“更新 OS 版本”。此时会打开“更新 OS 版本(预览版)”边栏选项卡，其中包含更多详细信息。

![云服务建议](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)

### 监视虚拟网络
单击“资源安全运行状况”磁贴中的“网络”即可打开“网络”边栏选项卡，其中包含更多详细信息，如下所示：

![联网](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

####网络建议

此边栏选项卡在顶部提供汇总问题列表，在底部提供所监视网络的列表，与虚拟机资源运行状况信息类似。

网络状态细分部分列出了可能的安全问题，并提供了建议。可能的问题包括：

- 下一代防火墙 (NGFW) 未安装
- 子网上未启用网络安全组 (NSG)
- VM 上未启用 NSG
- 禁止通过公共的外部终结点进行外部访问
- 面向 Internet 的终结点运行正常

单击其中一项建议即可打开新的边栏选项卡，其中包含该建议的更多详细信息，如以下示例所示。

![限制终结点](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

在此示例中，“为子网配置缺少的网络安全组”边栏选项卡提供了一个列表，其中的子网和虚拟机缺少 NSG 保护。如果单击需要应用 NSG 的子网，将会打开另一个边栏选项卡。

在“选择网络安全组”边栏选项卡中，既可以选择最适合子网的网络安全组，也可以创建新的网络安全组。

####面向 Internet 的终结点部分

在“面向 Internet 的终结点”部分，可以看到当前配置了面向 Internet 的终结点的 VM 及其当前状态。

![面向 Internet 的终结点](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

此表提供代表 VM 的终结点名称、面向 Internet 的 IP 地址，以及 NSG 和 NGFW 当前的严重性。此表按严重性排序，如下所述：
- 红色（顶部）：优先级高，应立即处理
- 橙色：优先级中，应尽快处理
- 绿色（最后一条）：运行状况正常

####网络拓扑部分

“网络拓扑”部分提供了资源的分层视图，如下所示：

![网络拓扑](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

此表按严重性排序（VM 和子网），如下所述：
- 红色（顶部）：优先级高，应立即处理
- 橙色：优先级中，应尽快处理
- 绿色（最后一条）：运行状况正常

在此拓扑视图中，第一层级包括[虚拟网络](../virtual-network/virtual-networks-overview.md)、[虚拟网络网关](../vpn-gateway/vpn-gateway-site-to-site-create.md)和[虚拟网络（经典）](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。第二层级包括子网，第三层级包括属于这些子网的 VM。右侧一栏为这些资源的网络安全组 (NSG) 的当前状态，如以下示例所示：

![网络树](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

此边栏选项卡底部为对此 VM 的建议，与上面描述的类似。单击某个建议即可了解其详细信息或应用所需的安全控制/配置。

###监视 SQL 资源
单击“资源安全运行状况”磁贴中的“SQL”即可打开“SQL”边栏选项卡，其中包含对各种问题（例如：正在进行审核，透明数据加密未启用）的建议。此外还有针对数据库常规运行状况的建议。

![SQL 资源运行状况](./media/security-center-monitoring/security-center-monitoring-fig13-ga.png)

单击任意一条建议即可更详细地了解解决问题所需采取的进一步行动。以下示例对“数据库审核未启用”这个建议的问题进行了说明。

![SQL 资源运行状况](./media/security-center-monitoring/security-center-monitoring-fig14-ga.png)

“对 SQL 数据库启用审核”边栏选项卡包含以下信息：

- SQL 数据库列表
- 所在的服务器
- 此设置是从服务器继承的，还是此数据库独有的
- 当前状态
- 问题的严重性

单击数据库解决建议的问题时，将会打开“审核和威胁检测”边栏选项卡，如下所示。

![SQL 资源运行状况](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

直接在“审核”选项下选择“启用”即可启用审核。

### 监视应用程序

如果 Azure 工作负荷中的应用程序位于 [Resource Manager VM](../resource-manager-deployment-model.md) 中，且后者使用公开的 Web 端口（TCP 端口 80 和 443），安全中心可以监视这些端口，确定潜在的安全问题并提供补救步骤方面的建议。单击“应用程序”磁贴即可打开“应用程序”边栏选项卡，在“预防步骤”部分包含一系列建议。此外还会根据主机/虚拟 IP 显示应用程序明细，如下所示。

![应用程序安全运行状况](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

与其他建议一样，单击该问题建议即可查看问题的更多详细信息以及补救方法。下图中显示的示例是一个应用程序，该应用程序已被确定为不安全的 Web 应用程序。选择被视为不安全的应用程序时，会打开另一个边栏选项卡，其中包含以下可用选项：

![应用](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

该边栏选项卡会提供一个列表，其中包含对此应用程序的所有建议。单击“添加 Web 应用程序防火墙”建议即可打开“添加 Web 应用程序防火墙”边栏选项卡，其中包含的选项可用于安装第三方 WAF（Web 应用程序防火墙），如下所示。

![添加 WAF](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## 另请参阅
本文档介绍了如何使用 Azure 安全中心的监视功能。若要详细了解 Azure 安全中心，请参阅以下内容：

- [Setting security policies in Azure Security Center](security-center-policies.md)（在 Azure 安全中心设置安全策略）- 了解如何在 Azure 安全中心配置安全设置
- [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md)（管理和响应 Azure 安全中心的安全警报）- 了解如何管理和响应安全警报
- [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md)（通过 Azure 安全中心监视合作伙伴解决方案）- 了解如何监视合作伙伴解决方案的运行状况。
- [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）- 查找有关如何使用服务的常见问题
- [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章

<!---HONumber=AcomDC_0921_2016-->