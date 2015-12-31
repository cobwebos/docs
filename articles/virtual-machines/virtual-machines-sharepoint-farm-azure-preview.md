<properties
	pageTitle="创建 SharePoint 服务器场 | Microsoft Azure"
	description="使用 Azure 预览门户应用商店快速创建新的基本或高可用性 SharePoint Server 2013 场。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="10/29/2015"
	wacn.date=""/>


# 创建 SharePoint 服务器场

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-rm-include.md)]经典模型。
 
使用 Microsoft Azure 预览门户应用商店，可以快速创建预配置的 SharePoint Server 2013 场。当你需要基本或高可用性 SharePoint farm 以用于开发和测试环境，或者当你将 SharePoint Server 2013 评估作为组织的协作解决方案时，这可为你节约很多时间。

> [AZURE.NOTE]Azure 预览门户的 Azure 应用商店中的 **SharePoint 服务器场**项已删除。

基本 SharePoint 场在此配置中包括 3 个虚拟机。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/Non-HAFarm.png)

使用该场配置可为 SharePoint 应用程序开发或你的第一次 SharePoint 2013 评估简化设置。

若要创建基本（三服务器）SharePoint 场，请执行以下操作：

1. 单击[此处](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)。
2. 单击“部署”。
3. 在“SharePoint 2013 非 HA 场”窗格中，单击“创建”。
4. 在“创建 SharePoint 2013 非 HA 场”窗格的 7 个步骤中指定设置，然后单击“创建”。

高可用性 SharePoint 场在此配置中包括 9 个虚拟机。

![sharepointfarm](./media/virtual-machines-sharepoint-farm-azure-preview/HAFarm.png)

使用该场配置可测试更高的客户端负载、外部 SharePoint 站点的高可用性以及 SharePoint 场的 SQL Server AlwaysOn。还可以将此配置用于高可用性环境中的 SharePoint 应用程序开发。

若要创建高可用性（九服务器）SharePoint 场，请执行以下操作：

1. 单击[此处](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)。
2. 单击“部署”。
3. 在“SharePoint 2013 HA 场”窗格中，单击“创建”。
4. 在“创建 SharePoint 2013 HA 场”窗格的 7 个步骤中指定设置，然后单击“创建”。

> [AZURE.NOTE]Microsoft 已发布 SharePoint Server 2016 IT Preview。要使此预览版易于安装和测试，可以使用预安装了 SharePoint Server 2016 IT Preview 及其必备组件的 Azure 虚拟机库映像。有关详细信息，请参阅[在 Azure 中测试 SharePoint Server 2016 IT Preview](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)。

## 管理 SharePoint 场

你可以通过远程桌面连接来管理这些场的服务器。有关详细信息，请参阅[登录到虚拟机](/documentation/articles/virtual-machines-windows-tutorial#log-on-to-the-virtual-machine)。

在管理中心 SharePoint 站点中，可以配置“我的站点”、SharePoint 应用程序和其他功能。有关详细信息，请参阅[配置 SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx)。

> [AZURE.NOTE]Azure 预览门户可以在具有面向 Internet 的网络影响力、仅限云的虚拟网络中创建这两种场。没有站点到站点 VPN 或 ExpressRoute 连接重新连回到你的组织网络。

## 其他资源

[Azure 基础结构服务中托管的 SharePoint 场](/documentation/articles/virtual-machines-sharepoint-infrastructure-services)

[在混合云中设置 SharePoint Intranet 场用于测试](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[适用于 SharePoint 2013 的 Microsoft Azure 体系结构](https://technet.microsoft.com/library/dn635309.aspx)

<!---HONumber=Mooncake_1221_2015-->