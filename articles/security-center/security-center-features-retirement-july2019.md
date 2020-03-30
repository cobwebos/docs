---
title: 安全中心停用功能 （2019 年 7 月） |微软文档
description: 本文介绍了安全中心在 2019 年 7 月 31 日停用的功能。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 456f379e2b3c2ff411b196d45aef4663fddb0fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245403"
---
# <a name="retirement-of-security-center-features-july-2019"></a>安全中心停用功能（2019年7月）

> [!NOTE]
> 本文档详细介绍了 2019 年 7 月 31 日从 Azure 安全中心停用的功能列表。
>
>

在 2019 年 7 月的六个月中，我们对 Azure 安全中心进行了多项[改进](https://azure.microsoft.com/updates/?product=security-center)。
通过这些改进的功能，我们于 2019 年 7 月 31 日从安全中心删除了一些冗余功能和相关 API。

这些停用功能中的大多数都可以替换为 Azure 安全中心或 Azure 日志分析中的其他功能。 可以使用[Azure Sentinel（预览）](https://azure.microsoft.com/services/azure-sentinel/)实现其他功能。

已停用的安全中心功能包括：

- [事件仪表板](#menu_events)
- [搜索菜单条目](#menu_search)
- [查看"身份&访问上的经典身份链接（预览）](#menu_classicidentity)
- [安全警报地图上的安全事件映射按钮（预览）](#menu_securityeventsmap)
- [自定义警报规则（预览）](#menu_customalerts)
- [调查威胁保护安全警报中的按钮](#menu_investigate)
- [安全解决方案的子集](#menu_solutions)
- [编辑安全策略的安全配置](#menu_securityconfigurations)
- [日志分析工作区的安全和审核仪表板（最初用于 OMS 门户）](#menu_securityomsdashboard)

本文提供了每个停用功能的详细信息，以及实现替换功能可以采取的步骤。

## <a name="events-dashboard"></a>事件仪表板<a name="menu_events"></a>

安全中心使用 Microsoft 监视代理从您的计算机收集各种与安全相关的配置和事件。 它将这些事件存储在工作区中。 事件[仪表板](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)允许您查看此数据，并为您提供日志分析的入口点。

我们停用了您选择工作区时显示的事件仪表板：

![事件仪表板][2]

### <a name="events-dashboard---the-new-experience"></a>事件仪表板 - 新体验

我们鼓励您使用 Azure 日志分析的本机功能来查看工作区上的显著事件。

如果您在安全中心中创建了自定义值得注意的事件，则可以访问这些事件。 在日志分析中，转到**选择工作区** > **保存的搜索**。 您的数据不会丢失或修改。 日志分析中的同一屏幕也提供了本机值得注意的事件。

![工作区保存的搜索][3]

## <a name="search-menu-entry"></a>搜索菜单条目<a name="menu_search"></a>

Azure 安全中心当前使用 Azure 监视器日志搜索来检索和分析安全数据。 此屏幕用作日志分析搜索页的窗口，并使用户能够在其选定的工作区上运行搜索查询。 有关详细信息，请参阅[Azure 安全中心搜索](https://docs.microsoft.com/azure/security-center/security-center-search)。 我们停用了此搜索窗口：

![搜索页][4]

### <a name="search-menu-entry---the-new-experience"></a>搜索菜单条目 - 新体验

我们鼓励您使用 Azure 日志分析本机功能在工作区上执行搜索查询。 转到 Azure 日志分析并选择 **"日志**"。

![日志分析日志页面][5]

## <a name="classic-identity--access-preview"></a>经典身份&访问（预览）<a name="menu_classicidentity"></a>

安全中心中的经典标识&访问体验当前在日志分析中显示标识和访问信息的仪表板。 要查看此仪表板：

1. 选择 **"查看经典身份&访问**。

   ![标识页][6]

1. 查看**标识&访问仪表板**。

    ![标识页 - 工作区选择][7]

1. 选择工作区以在日志分析中打开 **"标识&访问**"仪表板，以查看工作区上的标识和访问信息。

   ![标识页 - 仪表板][8]

我们停用了前面步骤中显示的所有三个屏幕。 您的数据在日志分析安全解决方案中仍然可用，并且未修改或删除。

### <a name="classic-identity--access-preview---the-new-experience"></a>经典身份&访问（预览） - 新体验

日志分析仪表板在单个工作区上显示了见解。 但是，本机安全中心功能提供对所有订阅及其关联的所有工作区的可见性。 您可以访问易于使用的视图，该视图可让您关注根据安全分数排列的建议的重要性。

通过选择安全中心内的**标识&访问（预览），** 可以访问日志分析中的**标识&访问**仪表板的所有功能。

![身份页面 - 经典体验退休][9]

## <a name="security-events-map"></a>安全事件映射<a name="menu_securityeventsmap"></a>

安全中心为您提供[安全警报映射](https://docs.microsoft.com/azure/security-center/security-center-threat-intel)，以帮助识别安全威胁。 **该地图中的"转到安全事件映射"** 按钮将打开一个仪表板，允许您查看所选工作区上的原始安全事件。

我们删除了 **"转到安全事件映射"** 按钮和每个工作区仪表板。

![安全警报映射 - 按钮][10]

当您选择"**转到安全事件映射"** 按钮时，它将打开（现已停用）威胁情报仪表板。

![“威胁智能”仪表板][11]

当您选择工作区以查看其威胁智能仪表板时，您将在日志分析中打开（现已停用）安全警报映射（预览）屏幕。

![日志分析中的安全警报映射][12]

您的现有数据在日志分析安全解决方案中仍然可用，并且未修改或删除。

### <a name="security-events-map---the-new-experience"></a>安全事件映射 - 新体验

我们鼓励您使用安全中心内置的警报映射功能：**安全警报映射（预览）。** 此功能提供优化的体验，适用于所有订阅和关联的工作区。 它为您提供整个环境的高级视图，并且不专注于单个工作区。

## <a name="custom-alert-rules-preview"></a>自定义警报规则（预览）<a name="menu_customalerts"></a>

我们于 2019 年 6 月 30 日[停用了自定义警报体验](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)，因为它的基础基础结构已停用。 停用日期后，将不再生成自定义安全警报。
我们建议您启用 Azure [Sentinel](https://azure.microsoft.com/services/azure-sentinel/)并在那里重新创建自定义警报。 或者，您可以使用 Azure 监视器日志警报创建警报。

要使用 Azure 哨兵创建自定义警报，请执行以下准备：

1. [打开 Azure 哨兵](https://portal.azure.com/#create/Microsoft.ASI/preview)并选择自定义警报的存储工作区
1. 从菜单中选择**分析**
1. 按照以下[教程](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats)中有关如何在 Azure Sentinel 中创建自定义警报的说明进行操作

如果您对使用 Azure Sentinel 不感兴趣，则可以使用 Azure 监视器日志警报创建警报。 有关说明，请参阅使用 Azure 监视器 中的 Azure 监视器和[日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) [创建、查看和管理日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)。

![自定义警报][13]

有关自定义警报停用的详细信息，请参阅 Azure[安全中心（预览）中的自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)。

## <a name="security-alerts-investigation"></a>安全警报调查<a name="menu_investigate"></a>

安全中心[中的"调查"功能](https://docs.microsoft.com/azure/security-center/security-center-investigation)可帮助您对潜在的安全事件进行会审。 该功能允许您了解事件的范围并跟踪其根本原因。 我们从安全中心删除了此功能，因为它已被[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)中改进的体验所取代。

![安全事件][14]

从**安全事件**屏幕中选择"**调查**"按钮时，请在日志分析中打开调查仪表板（预览）。 我们停用了调查仪表板。

您的现有数据在日志分析安全解决方案中仍然可用，并且未修改或删除。

![日志分析中的调查仪表板][15]

### <a name="investigation---the-new-experience"></a>调查 - 新体验

我们鼓励您过渡到 Azure [Sentinel，](https://azure.microsoft.com/services/azure-sentinel/)获得丰富的调查体验。 Azure Sentinel 提供了强大的搜索和查询工具，用于查找组织数据源的安全威胁。

## <a name="subset-of-security-solutions"></a>安全解决方案子集<a name="menu_solutions"></a>

安全中心可以在[Azure 中启用集成的安全解决方案](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)。 我们从安全中心停用了以下合作伙伴解决方案。 这些解决方案在[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)中以及许多其他数据源中启用。

- [下一代防火墙和 Web 应用程序防火墙解决方案](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [集成支持通用事件格式 （CEF） 的安全解决方案](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft 高级威胁分析](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD 标识保护](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

停用后，不能从 UI 或 API 添加或修改前面列表中提及的任何解决方案类型。 Azure 安全中心将不再发现这些合作伙伴解决方案的任何新实例。

如果您有现有的连接解决方案，我们鼓励您迁移到 Azure Sentinel。

![安全中心解决方案][16]

## <a name="edit-security-configurations-for-security-policies"></a>编辑安全策略的安全配置<a name="menu_securityconfigurations"></a>

Azure 安全中心应用 [150 多种建议的规则](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)来监视安全配置，以便强化 OS。 这些规则涉及防火墙、审核、密码策略等。 如果发现计算机中的某项配置有漏洞，则安全中心会生成安全建议。 ["编辑安全配置"屏幕](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)允许客户自定义安全中心的默认操作系统安全配置。

我们停用了此预览功能。 要在停用日期后将安全配置重置回其默认值，请使用[以下说明](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization)通过 API 或 Powershell 执行此操作。

![编辑安全配置][17]

### <a name="edit-security-configurations---the-new-experience"></a>编辑安全配置 - 新体验

我们打算启用安全中心来支持[来宾配置代理](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)。 此类更新将允许更丰富的功能集，包括支持更多操作系统和集成来宾配置的 Azure 来宾策略。 启用这些更改后，您还可以大规模控制配置并自动将其应用于新资源。

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>日志分析工作区的安全和审核仪表板<a name="menu_securityomsdashboard"></a>

安全和审核仪表板最初用于 OMS 门户。 在日志分析中，仪表板提供每个工作区显著安全事件和威胁的概述、威胁智能映射以及保存在工作区中的安全事件的标识和访问评估。 我们删除了仪表板。 正如我们已在仪表板 UI 中建议的，我们建议您过渡到 Azure 安全中心。

![日志分析安全仪表板][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>安全和审核仪表板 - 新体验

我们建议您切换到 Azure 安全中心。 它跨多个订阅及其关联的工作区以及更丰富的功能集提供相同的安全概述。

您可以在安全中心的[GitHub 存储库](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)中获取填充安全和审核仪表板的原始日志分析查询。

## <a name="next-steps"></a>后续步骤

- 了解有关[Azure 安全中心](https://docs.microsoft.com/azure/security-center/)的更多详细信息。
- 了解有关[Azure 哨兵](https://docs.microsoft.com/azure/sentinel)的更多内容。

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
