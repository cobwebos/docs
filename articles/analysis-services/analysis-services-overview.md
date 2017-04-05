---
title: "什么是 Azure Analysis Services | Microsoft Docs"
description: "Azure 中的 Analysis Services 简介。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3539da8afe5781d74cbf723090050b767373f268
ms.lasthandoff: 03/25/2017


---
# <a name="what-is-azure-analysis-services"></a>什么是 Azure Analysis Services？
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services 基于 Microsoft SQL Server Analysis Services 中经验证的分析引擎构建，可在云中提供企业级的数据建模。 

观看此视频，了解有关 Azure Analysis Services 如何适应 Microsoft 的整体 BI 功能的详细信息，以及了解将语义模型导入到云的益处。

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> Azure Analysis Services 为**预览版**。 有一些功能还不能使用。 请务必查看本文后面的[预期预览版](#preview-expectations)。 另外，请务必关注我们的 [Azure Analysis Services 博客](https://go.microsoft.com/fwlink/?linkid=830920)，了解最新信息。
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>基于 SQL Server Analysis Services
Azure Analysis Services 与用户熟悉的同一个 SQL Server 2016 Analysis Services Enterprise Edition 兼容。 Azure Analysis Services 支持 1200 兼容级别的表格模型。 支持 DirectQuery、分区、行级别安全性、双向关系和转换。

## <a name="use-the-tools-you-already-know"></a>使用已经熟悉的工具
![BI 开发人员工具](./media/analysis-services-overview/aas-overview-dev-tools.png)

可使用与 SQL Server Analysis Services 相同的工具创建 Azure Analysis Services 数据模型。 使用最新版本的 [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 或使用 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 中的 [Azure Powershell](/powershell/azureps-cmdlets-docs) 和 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 模板创作和部署表格模型。

## <a name="connect-to-data-sources"></a>连接到数据源
部署到 Azure 中服务器的数据模型支持连接到组织或云中的本地数据源。 为混合 BI 解决方案合并来自本地和云数据源的数据。

![数据源](./media/analysis-services-overview/aas-overview-data-sources.png)

由于服务器处于云中，因此可无缝连接到云数据源。 连接本地数据源时，[本地数据网关](analysis-services-gateway.md)可确保快速、安全地连接到云中的 Analysis Services 服务器。  

 \* 预览版尚不支持某些数据源。 若要了解详细信息，请参阅本文后面的[预期预览版](#preview-expectations)。

## <a name="explore-your-data-from-anywhere"></a>随时随地浏览数据
随时随地从服务器连接和[获取数据](analysis-services-connect.md)。 Azure Analysis Services 支持从 Power BI Desktop、Excel、自定义应用和基于浏览器的工具连接。

![数据可视化](./media/analysis-services-overview/aas-overview-visualization.png)

 \* 预览版尚不支持 Power BI Embedded。

## <a name="secure"></a>安全
#### <a name="user-authentication"></a>用户身份验证
Azure Analysis services 的用户身份验证通过 [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) 处理。 用户尝试登录 Azure Analysis Services 数据库时，使用组织帐户标识，该标识对用户尝试访问的数据库具有访问权限。 这些用户标识必须是 Azure Analysis Services 服务器所在订阅的默认 Azure Active Directory 的成员。 AAD 与本地 Active Directory 之间的[目录集成](https://technet.microsoft.com/library/jj573653.aspx)是一种让本地用户访问 Azure Analysis Services 数据库的有效方法，但并非所有方案都需要此方法。

用户可通过其帐户的用户主体名称 (UPN)和密码登录。 与本地 Active Directory 同步时，用户的 UPN 通常是其组织的电子邮件地址。

通过将用户分配到 Azure 订阅中的角色来处理 Azure Analysis Services 服务器资源的管理权限。 默认情况下，订阅管理员在 Azure 中具有对服务器资源的所有者权限。 可通过 Azure Resource Manager 添加其他用户。

#### <a name="data-security"></a>数据安全
Azure Analysis Services 使用 Azure Blob 存储来持久保留 Analysis Services 数据库的存储和元数据。 使用 Azure Blob 服务器端加密 (SSE) 加密 Blob 中的数据文件。 使用直接查询模式时，仅存储元数据；在查询时从数据源访问实际数据。

#### <a name="on-premises-data-sources"></a>本地数据源
通过安装和配置[本地数据网关](analysis-services-gateway.md)，可以实现对组织内本地驻留数据的安全访问。 网关提供在直接查询和内存模式下的数据访问。 当 Azure Analysis Services 模型连接到本地数据源时，将创建查询以及本地数据源的加密凭据。 网关云服务分析该查询，并将请求推送到 Azure 服务总线。 本地网关会针对挂起的请求轮询 Azure 服务总线。 然后，网关会获取查询，对凭据进行解密，然后连接到数据源开始执行。 随后，结果会从数据源返回到网关，然后返回到 Azure Analysis Services 数据库。

Azure Analysis Services 受 [Microsoft 联机服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和 [Microsoft 联机服务隐私声明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)约束。
有关 Azure 安全性的详细信息，请参阅 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/Security/AzureSecurity)。

## <a name="get-help"></a>获取帮助
Azure Analysis Services 的设置和管理非常简单。 用户可以在这里找到创建和管理服务器所需的所有信息。 在创建要部署到服务器的数据模型时，过程与创建部署到本地服务器的数据模型的非常相似。 在[MSDN 上的 Analysis Services](https://msdn.microsoft.com/library/bb522607.aspx)中提供了内容丰富的概念、过程、教程和参考文章库。

我们还在[第 9 频道上的 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services) 中提供了大量帮助视频。

信息会不断更新。 在 [Azure Analysis Services 博客](https://go.microsoft.com/fwlink/?linkid=830920)上可以随时获取最新信息。

## <a name="community"></a>社区
Analysis Services 拥有一个充满活力的用户社区。 参与 [Azure Analysis Services 论坛](https://aka.ms/azureanalysisservicesforum)上的对话。

## <a name="feedback"></a>反馈
想提出建议或功能请求？ 请务必在 [Azure Analysis Services 反馈](https://aka.ms/azureanalysisservicesfeedback)中发表你的评论。

有关于文档的建议？ 可使用 Disqus 在每篇文章底部添加意见。

## <a name="preview-expectations"></a>预期预览版
Azure Analysis Services 当前为预览版。 应注意以下事项。

##### <a name="server-modes"></a>服务器模式
Azure Analysis Services 当前支持 1200 兼容级别表格模型的表格模式。 不支持多维和数据挖掘模式以及 Power Pivot for SharePoint 模式。

##### <a name="data-sources"></a>数据源
对于预览版，在部署到 Azure Analysis Services 服务器的表格 1200 模型中支持以下数据源。

| **云** | **本地（需要数据网关）** |
| --- | --- |
| SQL 数据库 |SQL Server |
| SQL 数据仓库 |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>数据源提供程序
Azure Analysis Services 中的数据模型可能需要不同的数据提供程序连接到数据源，而不是 SQL Server Analysis Services 中的数据模型。 数据提供程序要求取决于云中或本地的数据源以及数据模型类型；内存中或直接查询。 若要了解详细信息，请参阅[数据源连接](analysis-services-datasource.md)。

### <a name="client-connections"></a>客户端连接

客户端应用程序需要最新的客户端[数据提供程序](analysis-services-data-providers.md)，才能连接到 Azure Analysis Services。

不支持实时连接 Azure Analysis Services 服务器并保存在 OneDrive 或 SharePoint Online 上的 Excel 工作簿。

## <a name="next-steps"></a>后续步骤
现在已详细了解了 Azure Analysis Services，可以开始使用了。 了解如何在 Azure 中[创建服务器](analysis-services-create-server.md)并对其[部署表格模型](analysis-services-deploy.md)。


