---
title: 停用的安全中心功能 (2019 年 7 月) |Microsoft Docs
description: 本文介绍将于 2019 年 7 月 31 日停用的安全中心中的功能。
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 069345f9c2d0fff0b580365153d8be13bb4ba204
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65952136"
---
# <a name="retirement-of-security-center-features-july-2019"></a>停用的安全中心功能 (2019 年 7 月)

我们做出了若干[改进](https://azure.microsoft.com/updates/?product=security-center)到过去六个月的 Azure 安全中心。
使用这些改进功能，我们要删除某些冗余功能和相关的 Api 从安全中心在 2019 年 7 月 31 日。  

大多数这些即将停用功能可以被替换为在 Azure 安全中心或 Azure Log Analytics 中的新功能。 可以使用实现其他功能[Azure Sentinel （预览版）](https://azure.microsoft.com/services/azure-sentinel/)。

若要停用的安全中心功能包括：

- [事件仪表板](#menu_events)
- [搜索菜单项](#menu_search)
- [标识和访问 （预览版） 上查看经典标识和访问链接](#menu_classicidentity)
- [安全事件映射安全警报地图 （预览版） 上的按钮](#menu_securityeventsmap)
- [自定义警报规则 （预览版）](#menu_customalerts)
- [调查威胁保护安全警报中的按钮](#menu_investigate)
- [安全解决方案的一个子集](#menu_solutions)
- [编辑安全策略的安全配置](#menu_securityconfigurations)
- [安全和审核仪表板 （最初在 OMS 门户中使用） 的 Log Analytics 工作区](#menu_securityomsdashboard)

本文提供有关每个已停用的功能和步骤可用于实现替换功能的详细的信息。

## 事件仪表板<a name="menu_events"></a>

安全中心使用 Microsoft Monitoring Agent 以从你的计算机中收集各种与安全相关的配置和事件。 它在你的工作区中存储这些事件。 [事件仪表板](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard)，您可以查看此数据并提供到 Log Analytics 的入口点。

我们正在停用在选择一个工作区时出现的事件仪表板：

![事件仪表板][2]

### <a name="events-dashboard---the-new-experience"></a>事件仪表板的新体验

我们建议你使用 Azure Log Analytics 的本机功能来查看你的工作区中值得注意的事件。

如果已在安全中心创建自定义值得注意的事件，它们将可访问。 在 Log Analytics 中，转到**选择工作区** > **保存的搜索**。 不会丢失或修改你的数据。 本机值得注意的事件，还提供从 Log Analytics 中的同一个屏幕。

![工作区保存的搜索][3]

## 搜索菜单项<a name="menu_search"></a>

目前，azure 安全中心使用 Azure Monitor 日志搜索来检索和分析安全数据。 此屏幕可作为一个到 Log Analytics 搜索页上，窗口，并使用户能够对其所选工作区运行搜索查询。 有关详细信息，请参阅[Azure 安全中心搜索](https://docs.microsoft.com/azure/security-center/security-center-search)。 我们正在停用此搜索窗口：

![搜索页][4]

### <a name="search-menu-entry---the-new-experience"></a>搜索菜单项的新体验

我们鼓励你使用 Azure Log Analytics 本机功能以你的工作区中执行搜索查询。 转到 Azure Log Analytics 并选择**日志**。

![Log Analytics 日志页][5]

## 经典的标识和访问 （预览）<a name="menu_classicidentity"></a>

安全中心中的经典的标识和访问体验当前显示在 Log Analytics 中的标识和访问信息的仪表板。 若要查看此仪表板：

1. 选择**查看经典标识和访问**。

   ![标识页][6]

1. 视图**标识和访问仪表板**。

    ![-工作区选择标识页][7]

1. 选择要打开的工作区**标识和访问**Log Analytics 查看标识和访问你的工作区的信息中的仪表板。

   ![标识页-仪表板][8]

我们正在停用前面的步骤中所示的所有三个屏幕。 你的数据，仍可在 Log Analytics 的安全解决方案，不会修改或删除。

### <a name="classic-identity--access-preview---the-new-experience"></a>经典的标识和访问 （预览版）-新体验

Log Analytics 仪表板在单个工作区上介绍了见解。 但是，安全中心的本地功能提供可见性的所有订阅和与之关联的所有工作区。 您可以访问一种简单-若要使用视图，让您专注于重要的建议根据其安全的分数。

所有功能**标识和访问**Log Analytics 中的仪表板可以通过选择达到**标识和访问 （预览）** 安全中心内。

![标识页-经典体验停用][9]

## 安全事件映射<a name="menu_securityeventsmap"></a>

安全中心提供了[安全警报映射](https://docs.microsoft.com/azure/security-center/security-center-threat-intel)以帮助识别安全威胁。 **转到安全事件映射**该映射中的按钮将打开仪表板，您可以查看在所选工作区上的原始安全事件。

我们要删除**转到安全事件映射**按钮和每个工作区的仪表板。

![安全警报图-按钮][10]

当选择**转到安全事件映射**按钮，打开威胁智能仪表板。 我们即将退休威胁智能仪表板。  

![“威胁智能”仪表板][11]

当选择一个工作区以查看其威胁智能仪表板时，Log Analytics 中打开安全警报映射 （预览版） 屏幕。 我们正在停用此屏幕。

![在 Log Analytics 中的安全警报映射][12]

现有数据，仍可在 Log Analytics 的安全解决方案和不能修改也不删除。

### <a name="security-events-map---the-new-experience"></a>安全事件映射的新体验

我们鼓励您使用内置到安全中心的警报映射功能：**安全警报映射 （预览版）** 。 此功能提供了优化的体验，并跨所有订阅和关联的工作区。 它在你的环境之间提供的高级视图，并不侧重于单个工作区。

## 自定义警报规则 （预览版）<a name="menu_customalerts"></a>

我们[停用自定义警报体验](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)于 2019 年 6 月 30 日停用及其底层基础结构因为。 到那时，您可以编辑现有的自定义警报规则，但不能添加新的。 我们建议您启用[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)自动迁移现有警报并创建新的。 或者，您可以使用 Azure Monitor 日志警报创建警报。

若要保留现有警报并将其迁移到 Azure Sentinel:

1. 打开 Azure Sentinel 并选择自定义警报的存储位置工作区。
1. 选择**Analytics**从菜单将自动迁移你的警报。

![自定义警报][13]

如果您不感兴趣过渡到 Azure Sentinel，我们建议你使用 Azure Monitor 日志警报创建警报。 有关说明，请参阅 [创建、 查看和管理通过使用 Azure Monitor 的日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)并[Azure Monitor 中的日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)。

自定义警报停用的详细信息，请参阅[Azure 安全中心 （预览版） 中的自定义警报规则](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)。

## 安全警报调查<a name="menu_investigate"></a>

[调查功能](https://docs.microsoft.com/azure/security-center/security-center-investigation)在安全中心可帮助会审潜在的安全事件。 该功能，可了解事件的范围，并跟踪其根本原因。 我们要从安全中心删除此功能，因为它已替换中改进的体验[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)。

![安全事件][14]

当选择**调查**按钮从**安全事件**屏幕上，在 Log Analytics 中打开了调查仪表板 （预览版）。 我们即将退休了调查仪表板。  

现有数据，仍可在 Log Analytics 的安全解决方案和不能修改也不删除。

![在 Log Analytics 中的调查仪表板][15]

### <a name="investigation---the-new-experience"></a>调查的新体验

我们鼓励你过渡到[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)为丰富的调查体验。 Azure Sentinel 提供了强大的搜索和查询工具，要在组织的数据源之间的安全威胁。  

## 安全解决方案的子集<a name="menu_solutions"></a>

可以启用安全中心[集成 Azure 中的安全解决方案](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)。 从安全中心，我们即将退休以下合作伙伴解决方案。 这些解决方案中启用[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)以及许多其他数据源。

- [下一代防火墙和 web 应用程序防火墙解决方案](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [支持通用事件格式 (CEF) 的安全解决方案的集成](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft 高级威胁分析](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

后停用，您将无法添加或修改任何上述列表中，从 UI 或 API 中所述的解决方案类型。

如果你有现有的已连接的解决方案，我们建议你将移动到 Azure Sentinel。

![安全中心解决方案][16]

## 编辑安全策略的安全配置<a name="menu_securityconfigurations"></a>

Azure 安全中心监视安全配置。 应用一组[150 多种建议规则](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 用于强化 OS。 这些规则与防火墙、 审核、 密码策略和的详细信息。 如果发现计算机中的某项配置有漏洞，则安全中心会生成安全建议。 [编辑安全配置屏幕](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config)允许客户自定义安全中心中的默认 OS 安全配置。

我们正在停用此预览功能。 如果停用日期之后，你想要将安全配置重置回其默认值，就可以通过 API 或 Powershell 使用[说明](https://aka.ms/ascresetsecurityconfigurations)

![编辑安全配置][17]

### <a name="edit-security-configurations---the-new-experience"></a>编辑安全配置的新体验

我们想要启用安全中心，以支持[来宾的配置代理](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)。 此类更新将允许更丰富的功能集，包括支持多个操作系统和来宾配置的 Azure 来宾中策略的集成。 这些更改才会启用后，您还必须能够控制在规模较大的配置和自动将它们应用于新的资源。

## Log Analytics 工作区的安全和审核仪表板<a name="menu_securityomsdashboard"></a>

最初在 OMS 门户中使用的安全和审核仪表板。 在 Log Analytics 仪表板提供值得注意的安全事件和威胁、 威胁智能地图和保存工作区中的安全事件的标识和访问评估的每个工作区的概述。 我们要删除仪表板。 根据我们已建议在仪表板 UI 中，我们建议你过渡到 Azure 安全中心。

![Log Analytics 安全仪表板][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>安全和审核仪表板的新体验

我们建议您先切换到 Azure 安全中心。 它跨多个订阅提供相同的安全概述和工作区关联，以及更丰富的功能设置。

可以获取初始填充中的安全和审核仪表板的 Log Analytics 查询[GitHub 存储库](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards)安全中心。

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)的详细信息。
- 详细了解如何[Azure Sentinel](https://docs.microsoft.com/azure/sentinel)。

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
