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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/03/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bf56aa4bafd929010bd916d09f7025ad76823d1f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/21/2017

---
# <a name="what-is-azure-analysis-services"></a>什么是 Azure Analysis Services？
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services 在云中提供企业级数据建模。 它是完全托管的平台即服务 (PaaS)，与 Azure 数据平台服务紧密集成。 Analysis Services 使用高度优化的 OLAP 数据分析引擎，在大型、复杂且通常独立的数据源之间提供丰富的语义模型层。 将数据集中在一起即可定义语义数据模型，创建高度自定义且高效的分析，以便在新式客户端工具中提供丰富的交互式分析体验。

![数据源](./media/analysis-services-overview/aas-overview-data-sources.png)


观看[此视频](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4)，了解 Azure Analysis Services 如何适应 Microsoft 的整体 BI 功能，以及将数据模型导入到云的益处。

<!--
>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>
-->

## <a name="built-on-sql-server-analysis-services"></a>基于 SQL Server Analysis Services
Azure Analysis Services 与用户熟悉的同一个 SQL Server Analysis Services Enterprise Edition 兼容。 Azure Analysis Services 支持 1200 和 1400 兼容级别的表格模型。 支持分区、行级别安全性、双向关系和转换。 内存中模式和 DirectQuery 模式意味着，可以对大型且复杂的数据集进行闪电般快速的查询。

表格模型提供快速开发功能，其自定义程度可以很高。 面向开发人员的表格模型包括用于描述模型对象的表格对象模型 (TOM)。 TOM 通过[表格模型脚本语言 (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) 在 JSON 中公开，通过 [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 命名空间在 AMO 数据定义语言中公开。

表格 1400 模型中的新功能支持详细信息行、对象级安全性、不规则层次结构、在 SSDT 中针对数据连接提供的新式“获取数据”体验，以及许多其他的增强功能。 由于基础模型元数据相同，因此可以轻松地将现有的本地表格模型解决方案迁移到云。


## <a name="better-with-azure"></a>更好地与 Azure 配合使用
Azure Analysis Services 集成许多 Azure 数据服务，因此可以生成复杂的分析解决方案。

Azure Analysis Services 可以使用 Azure SQL 数据库、Azure SQL 数据仓库和 Azure Blob 存储中的数据。 可以使用中心辐射模型在 Azure 中生成企业数据仓库解决方案，以 SQL 数据仓库为中心，让多个 BI 模型围绕该中心并以不同的业务组或主题区域为目标。

可以使用 Azure 数据工厂来协调数据的移动和转换，后者是任何企业 BI/分析解决方案中的核心功能。 只需包括一项将数据加载到模型中的活动，即可将 Azure Analysis Services 集成到任何 Azure 数据工厂管道中。 也可通过自定义代码将 Azure 自动化和 Azure Functions 用于完成模型的轻型业务流程。

Azure Analysis Services 也可与 Azure Active Directory 紧密集成，对关键数据进行基于角色的安全访问。

## <a name="pricing"></a>定价
可在开发人员层、基本层和标准层使用 Azure Analysis Services。 每个层中的计划成本因处理能力、QPU 数和内存大小而异。 创建服务器时，将在层内选择计划。 可以在同一层内上下更改计划，或者升级到更高的层，但不能从较高的层降级到较低的层。

![升级层](./media/analysis-services-overview/aas-overview-tier-up.png)

可以通过 Azure 门户或 Set-AzureRmAnalysisServicesServer PowerShell cmdlet 即时更改层。 若要详细了解不同的计划和层并使用定价计算器来确定适合自己的计划，请参阅 [Azure Analysis Services 定价](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="scale-resources"></a>缩放资源
纵向扩展、纵向缩减或暂停服务器。 使用 Azure 门户，或者通过 PowerShell 进行完全且即时的控制。 仅为所用的部分付费。

创建新服务器时，请使用 [New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) cmdlet 来设置计划。 对于现有的服务器，请使用 [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver) cmdlet 来更改计划。 并不总是使用服务？ 可以使用门户或 [Suspend-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver) cmdlet 来暂停服务。 使用 [Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver) cmdlet 来重启服务。 只有服务器处于活动状态时，才需付费。


## <a name="regions"></a>区域
可在以下 [Azure 区域](https://azure.microsoft.com/regions/)创建 Azure Analysis Services 服务器：

| 美洲 | 欧洲 | 亚太区 |
|----------|--------|--------------|
|  巴西南部<br> 加拿大中部<br> 美国东部 2<br> 美国中北部<br> 美国中南部<br> 美国中西部<br> 美国西部 | 欧洲北部<br> 英国南部<br> 欧洲西部 |   澳大利亚西南部<br> 日本东部<br> 东南亚<br> 印度西部  |

将会不断添加新区域，因此此列表可能并不完整。 通过 Azure 门户或 Azure Resource Manager 模板创建服务器时，需选择位置。 若要获得最佳性能，请选择最接近最大用户群的位置。 请在多个区域的冗余服务器上部署模型，确保[高可用性](analysis-services-bcdr.md)。

## <a name="get-up-and-running-quickly"></a>快速启动和运行
可以通过 Azure 门户在数分钟内[创建服务器](analysis-services-create-server.md)。 另外，有了 Azure Resource Manager 模板和 PowerShell，你就可以使用声明性模板来预配服务器。 利用单个模板可以部署多个服务和其他 Azure 组件，例如存储帐户。  若要了解详细信息，请参阅[使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)。

创建服务器以后，即可直接在 Azure 门户中创建表格模型。 使用新的（预览版）Web 设计器功能，可以连接到 Azure SQL 数据库、Azure SQL 数据仓库数据源，还可以导入 Power BI Desktop .pbix 文件。 表之间的关系是自动创建的。可以直接在浏览器中创建度量值或编辑 JSON 格式的 model.bim 文件。

## <a name="migrate-existing-tabular-models"></a>迁移现有的表格模型
如果现在已经有本地 SQL Server Analysis Services 模型解决方案，则不需重大更改即可迁移到 Azure Analysis Services。 若要进行迁移，可以使用 SSDT 将模型部署到服务器。 也可以在 SSMS 中使用备份和还原或 TMSL。

如果有本地数据源，则需安装和配置[本地数据网关](analysis-services-gateway.md)。 如果已配置角色和角色成员，则可以迁移角色，但需使用 SSMS 或 PowerShell 来重新添加角色成员。


## <a name="data-sources"></a>数据源
Azure Analysis Services 支持连接到组织中的本地数据源和云中的数据源。 为混合解决方案合并来自本地和云数据源的数据。 

新的表格 1400 模型使用 SSDT 中基于 M 公式查询语言的新式“获取数据”功能。 使用“获取数据”时，可以有更多的数据转换和混合功能，还可以创建和编辑自己的高级 M 公式语言查询。 例如，可使用表格 1400 模型以 Azure Blob 存储中的数据文件为基础建模。

Azure Analysis Services 支持使用 [DirectQuery](https://docs.microsoft.com/sql/analysis-services/tabular-models/directquery-mode-ssas-tabular) 直接连接到 Azure SQL 数据库、Azure SQL 数据仓库、SQL Server、SQL Server 数据仓库、Oracle 和 Teradata 关系数据库。

若要了解详细信息，请参阅 [Azure Analysis Services 中支持的数据源](analysis-services-datasource.md)。

## <a name="use-the-tools-you-already-know"></a>使用已经熟悉的工具

![BI 开发人员工具](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>适用于 Visual Studio 的 SQL Server Data Tools (SSDT)
使用免费的[适用于 Visual Studio 的 SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 开发和部署模型。 SSDT 包括适用于快速入门的 Analysis Services 项目模板。 SSDT 现在包括适用于表格 1400 模型的新式“获取数据”数据源查询和混合功能。 如果你熟悉 Power BI Desktop 和 Excel 2016 中的“获取数据”功能，则已知道创建高度自定义的数据源查询很容易。

使用新的 SSDT 和表格 1400 模型时，不需安装 Analysis Services 的本地实例即可托管工作区数据库。 SSDT 现在包括其自己的集成式 Analysis Services 引擎和数据库。 准备就绪后，即可直接从 SSDT 将内容部署到 Azure 中的服务器。 另外，SSDT 每月进行更新，因此可以快速完成最新功能的使用入门。

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
通过使用 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 管理服务器和模型数据库。 连接到云中的服务器。 直接从 XMLA 查询窗口运行 TMSL 脚本，然后通过 TMSL 脚本自动执行任务。 新特性和功能推出迅速 - SSMS 每月进行更新。

#### <a name="powershell"></a>PowerShell
服务器资源管理任务，如创建服务器、挂起或恢复服务器操作，或更改服务级别（层），都要使用 Azure Resource Manager (AzureRM) cmdlet。 用于管理数据库的其他任务（例如添加或删除角色成员、处理或运行 TMSL 脚本）使用 SqlServer 模块中的 cmdlet。 AzureRM 和 SQLServer 模块均在 [PowerShell 库](https://www.powershellgallery.com/)中提供。


## <a name="secure"></a>安全
![数据可视化](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>身份验证
Azure Analysis services 的用户身份验证通过 [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) 处理。 用户尝试登录 Azure Analysis Services 数据库时，使用组织帐户标识，该标识对用户尝试访问的数据库具有访问权限。 这些用户标识必须是 Azure Analysis Services 服务器所在订阅的默认 Azure Active Directory 的成员。 若要了解详细信息，请参阅[身份验证和用户权限](analysis-services-manage-users.md)。

#### <a name="data-security"></a>数据安全
Azure Analysis Services 使用 Azure Blob 存储来持久保留 Analysis Services 数据库的存储和元数据。 使用 Azure Blob 服务器端加密 (SSE) 加密 Blob 中的数据文件。 使用“直接查询”模式时，仅存储元数据。 实际数据是在查询时从数据源访问的。

#### <a name="on-premises-data-sources"></a>本地数据源
通过安装和配置[本地数据网关](analysis-services-gateway.md)，实现对组织内本地驻留数据的安全访问。 网关提供在直接查询和内存模式下的数据访问。 当 Azure Analysis Services 模型连接到本地数据源时，将创建查询以及本地数据源的加密凭据。 网关云服务分析该查询，并将请求推送到 Azure 服务总线。 本地网关会针对挂起的请求轮询 Azure 服务总线。 然后，网关会获取查询，对凭据进行解密，然后连接到数据源开始执行。 随后，结果会从数据源返回到网关，然后返回到 Azure Analysis Services 数据库。

Azure Analysis Services 受 [Microsoft 联机服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和 [Microsoft 联机服务隐私声明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)约束。
有关 Azure 安全性的详细信息，请参阅 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/Security/AzureSecurity)。

## <a name="client-connections"></a>客户端连接
![数据可视化](./media/analysis-services-overview/aas-overview-clients.png)

利用新式的数据浏览和可视化工具（例如 Power BI、Excel 和其他第三方工具），最终用户可以通过交互性强且视觉效果丰富的方式来了解模型数据。

客户端使用 MSOLAP、AMO 或 ADOMD [客户端库](analysis-services-data-providers.md)连接到 Analysis Services 服务器。 Microsoft 客户端应用程序（例如 Power BI Desktop 和 Excel）会安装所有这三个客户端库。 但请记住，客户端库可能不是 Azure Analysis Services 所需要的最新版本，具体取决于更新的版本或频率。 这同样适用于自定义应用程序或其他界面，例如 AsCmd、TOM、ADOMD.NET。 这些应用程序通常需要手动安装包中的库。


## <a name="get-help"></a>获取帮助

#### <a name="documentation"></a>文档
Azure Analysis Services 的设置和管理非常简单。 可以在这里找到创建和管理服务器服务所需的所有信息。 在创建要部署到服务器的数据模型时，过程与创建部署到本地服务器的数据模型的非常相似。 [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) 中提供了内容丰富的概念、过程、教程和参考文章库。

#### <a name="videos"></a>视频
请观看[第 9 频道 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services) 部分的帮助视频。

#### <a name="blogs"></a>博客
信息会不断更新。 在 [Analysis Services 团队博客](https://blogs.msdn.microsoft.com/analysisservices/)和 [Azure 博客](https://azure.microsoft.com/blog/)上可以随时获取最新信息。

#### <a name="community"></a>社区
Analysis Services 拥有一个充满活力的用户社区。 参与 [Azure Analysis Services 论坛](https://aka.ms/azureanalysisservicesforum)上的对话。

## <a name="feedback"></a>反馈
想提出建议或功能请求？ 请务必在 [Azure Analysis Services 反馈](https://aka.ms/azureanalysisservicesfeedback)中发表你的评论。

有关于文档的建议？ 可使用 Livefyre 在每篇文章底部添加意见。

## <a name="next-steps"></a>后续步骤
现在已详细了解了 Azure Analysis Services，可以开始使用了。 了解如何在 Azure 中[创建服务器](analysis-services-create-server.md)。 服务器就绪以后，请逐步学习 [Adventure Works 教程](tutorials/aas-adventure-works-tutorial.md)，了解如何创建完全正常运行的表格模型并将其部署到服务器。

