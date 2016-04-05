<!-- not suitable for Mooncake -->

<properties
	pageTitle="Azure 及其对现有 Azure 服务的影响"
	description="介绍新的 Azure 及其功能对 Azure 中现有服务的影响。"
	authors="yochayk"
	writer="yochayk"
	editor="yochayk"
	manager="nirma"
	services="app-service"
	documentationCenter=""/>

<tags
	ms.service="app-service"
	ms.date="02/12/2016"
	wacn.date=""/>


# Azure 和现有的 Azure 服务

本文概括介绍了对现有 Azure 服务的更改，这是将多个 Azure 服务组合为 [Azure Web 应用](/home/features/web-site/)（新的集成产品）更改的一部分。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../includes/app-service-web-to-api-and-mobile.md)]

## 概述

[Azure Web 应用](/home/features/web-site/)是一种全新独特的的云服务，使开发人员能够创建适用于任何平台和任何设备的 Web Apps 和 Mobile Apps。Azure 是一个集成的解决方案，旨在简化重复编码函数、与企业和 SaaS 系统集成并自动执行业务流程，同时满足你对安全性、可靠性和可伸缩性的需要。

Azure 将以下现有 Azure 服务 - [网站](/home/features/web-site/)、[移动服务](/home/features/mobile-services/)和 [Biztalk 服务](/home/features/biztalk-services/)融合为单个综合服务，同时添加强大的新功能。Azure 允许托管以下应用类型：

-   Web Apps
-   Mobile Apps
-   API Apps
-   Logic Apps

下表介绍了如何将现有的 Azure 服务映射到 Azure 以及其中可用的应用类型。

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">现有的 App Service</th>
<th align="left", style="width:10%">Azure Web 应用</th>
<th align="left", style="width:80%">更改内容</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Azure 网站</td>
<td align="left">Web Apps</td>
<td align="left"><li>对于 Azure 网站，Azure 严格限制为将名称“网站”更改为“Web Apps”。
<p><li>现在，Azure 中的所有现有网站实例均已成为 Web Apps。</p>
<p><li>你可以通过 <a href="https://manage.windowsazure.cn/">Azure 管理门户</a>访问现有网站，该门户的<em>“Web Apps”</em>下列出了所有现有站点。</p>
<p><li><em>Web 托管计划</em>现成为 <em>App Service 计划</em>。<em>App Service 计划</em>可以托管任何应用类型的 Azure Web 应用，例如 Web、Mobile、Logic 或 API Apps。</p>
<p><li>Azure Web Apps 现已公开上市。</p>
<p><li><a href="/home/features/web-site/">了解有关 Web Apps 的详细信息</a>。</p></td>
</tr>
<tr class="even">
<td align="left">Azure 移动服务</td>
<td align="left">Mobile Apps</td>
<td align="left"><p><li>移动服务继续用作独立服务，并仍然受完全支持。</p>
<p><li>Mobile Apps 是 Azure Web 应用中的一种应用类型，它融合了移动服务和其他服务的所有功能。</p>
<p><li>可以轻松<a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">从移动服务迁移到 Mobile Apps</a>。</p>
<p><li>作为 Azure Web 应用的一部分，Mobile Apps 的功能远胜于移动服务，例如，与本地和 SaaS 系统集成，暂存槽、WebJobs、更好的缩放选项，等等。</p>
<p><li><a href="/home/features/web-site/mobile/">了解有关 Mobile Apps 的详细信息</a>。</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">API Apps</td>
<td align="left">
<p><li>API Apps 是 Azure 中的全新应用类型，它让你能够轻松地构建并使用云中的 API。</p>
<p><li><a href="/home/features/web-site/api/">了解有关 API Apps 的详细信息</a>。</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Logic Apps</td>
<td align="left">
<p><li>Logic Apps 是 Azure 中全新应用类型，它让你能够轻松地自动执行业务流程。</p>
<p><li><a href="/home/features/web-site/logic/">了解有关 Logic Apps 的详细信息</a>。</p></td>
</tr>
<tr class="odd">
<td align="left">Azure BizTalk 服务</td>
<td align="left">BizTalk API Apps</td>
<td align="left">
<li><p>BizTalk 服务继续用作独立服务，并仍然受完全支持。</p>
<li><p>BizTalk 服务的所有功能都将作为 API Apps 集成到 Azure，使用户能够使用 Azure Web 应用中的任何应用类型执行企业应用程序集成和 B2B 集成方案</p>
<li><p>凭借 Logic Apps 则可以使用可视化设计体验自动执行业务流程，以创建工作流</p></td>
</tr>
</tbody>
</table>

要了解详细信息，请访问 [Azure 文档](/documentation/services/web-sites/)。

<!---HONumber=Mooncake_0328_2016-->