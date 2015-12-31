<properties
	pageTitle="Azure 中的 SharePoint Server 2013 场 | Microsoft Azure"
	description="查找描述如何在 Microsoft Azure 中设置开发/测试环境或生产 SharePoint Server 2013 场的文章。"
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="10/20/2015"
	wacn.date=""/>

# Azure 基础结构服务中托管的 SharePoint 场

[AZURE.INCLUDE [learn-about-deployment-models-both-include](../includes/learn-about-deployment-models-both-include.md)]

在 Microsoft Azure 基础结构服务中设置你的第一个或下一个开发/测试或生产 SharePoint 场，在其中你可以利用配置的简便性和相关功能快速扩展场以包括关键功能的新增能力或优化。

> [AZURE.NOTE]Microsoft 已发布 SharePoint Server 2016 IT Preview。要使此预览版易于安装和测试，可以使用预安装了 SharePoint Server 2016 IT Preview 及其必备组件的 Azure 虚拟机库映像。有关详细信息，请参阅[在 Azure 中测试 SharePoint Server 2016 IT Preview](http://azure.microsoft.com/blog/test-sharepoint-server-2016-it-preview-4/)。

## 基本 SharePoint 开发/测试场

此自动创建的环境由仅限云的 Azure 虚拟网络中的三个服务器组成：分别是域控制器、SQL Server 和 SharePoint 服务器。

请参阅 Azure 预览门户的 Azure 应用商店中的 [SharePoint 2013 非HA 场](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)项。这将为面向 Internet 的 SharePoint 网站创建基本开发/测试场。有关其他详细信息，请参阅[创建 SharePoint 服务器场](/documentation/articles/virtual-machines-sharepoint-farm-azure-preview)。

你还可以使用 Azure 资源管理器模板。请参阅[部署包含 3 个服务器的 SharePoint 场](/documentation/articles/virtual-machines-workload-template-sharepoint#deploy-a-three-server-sharepoint-farm)。

> [AZURE.NOTE]Azure 预览门户的 Azure 应用商店中的 **SharePoint 服务器场**项已删除。

## 高可用性 SharePoint 开发/测试场

此自动创建的环境由仅限云的 Azure 虚拟网络中的九个服务器组成：两个用于域控制器、三个用于 SQL Server 群集、两个应用程序层 SharePoint 服务器和两个 Web 层 SharePoint 服务器。

请参阅 Azure 预览门户的 Azure 应用商店中的 [SharePoint 2013 HA 场](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)项。这将为面向 Internet 的 SharePoint 网站创建高可用性开发/测试场。有关其他详细信息，请参阅[创建 SharePoint 服务器场](/documentation/articles/virtual-machines-sharepoint-farm-azure-preview)。

你还可以使用 Azure 资源管理器模板。请参阅[部署包含 9 个服务器的 SharePoint 场](/documentation/articles/virtual-machines-workload-template-sharepoint#deploy-a-nine-server-sharepoint-farm)。

> [AZURE.NOTE]Azure 预览门户的 Azure 应用商店中的 **SharePoint 服务器场**项已删除。

## 混合云开发/测试场

使用[混合云开发/测试环境中的 SharePoint Intranet 场](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)，可以创建模拟的混合云配置，以托管简单的两层 SharePoint 场，你可以从 Internet 上你所在位置使用该场测试 Azure 中托管的 Intranet SharePoint 场。

此配置使用经典部署模型。

## 高度可用的 Intranet SharePoint 生产场

借助于[使用 Azure 中的 SQL Server AlwaysOn 可用性组部署 SharePoint 2013](/documentation/articles/virtual-machines-workload-intranet-sharepoint-overview)，你可以在 Azure 中构建出生产就绪的高可用性 Intranet SharePoint Server 2013 场。

此配置使用经典部署模型。

## 其他资源

[适用于 SharePoint 2013 的 Microsoft Azure 体系结构](https://technet.microsoft.com/library/dn635309.aspx)

[在 Microsoft Azure 中使用 SharePoint Server 2013 的 Internet 站点](https://technet.microsoft.com/library/dn635307.aspx)

[Microsoft Azure 中的 SharePoint Server 2013 灾难恢复](https://technet.microsoft.com/library/dn635313.aspx)

[使用 Microsoft Azure Active Directory 进行 SharePoint 2013 身份验证](https://technet.microsoft.com/library/dn635311.aspx)

[在 Microsoft Azure 中部署 Office 365 目录同步 (DirSync)](https://technet.microsoft.com/library/dn635310.aspx)

<!---HONumber=Mooncake_1221_2015-->