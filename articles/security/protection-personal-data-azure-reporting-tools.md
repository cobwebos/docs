---
title: "使用 Azure 报告工具来阐述对个人数据的保护 | Microsoft Docs"
description: "如何使用 Azure 报告服务和技术来帮助保护个人数据的隐私。"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 0ec9ceb63c3e1872e9815a7895b624276fc46123
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>使用 Azure 报告工具来阐述对个人数据的保护

本文介绍如何使用 Azure 报告服务和技术来帮助保护个人数据的隐私。

## <a name="scenario"></a>方案

总部位于美国的一家大型邮轮公司正在拓展其运营，以便在地中海、亚得里亚海和波罗的海以及英属岛屿提供路线。 为帮助实现这些目的，该公司并购了意大利、德国、丹麦和英国的几家小型邮轮公司。

该公司使用 Microsoft Azure 来处理和存储企业数据。 这些数据包括个人身份信息，例如其全球客户群的姓名、地址、电话号码和信用卡信息。 另外，还包括传统的人力资源信息，例如，有关所有地点的公司员工的地址、电话号码、纳税识别号和其他信息。 该邮轮公司还维护一个奖励和忠诚计划会员的大型数据库，其中包含用于跟踪与当前和既往客户之间的关系的个人信息。

企业员工从公司的远程办公室访问网络，世界各地的旅行社有权访问某些公司资源。

## <a name="problem-statement"></a>问题陈述

该公司必须通过多层安全策略（使用 Azure 管理和安全功能对个人数据的访问与处理实施严格控制）来保护客户和员工的隐私和个人数据，并且必须能够向内部和外部审核员展示其保护措施。

## <a name="company-goal"></a>公司目标

作为其深层防御安全策略的一部分，公司的目标是跟踪对个人数据的所有访问和处理，并确保制定了适当的文档来阐述如何保护个人数据的隐私。

## <a name="solutions"></a>解决方案

Microsoft Azure 提供综合性的监视、日志记录和诊断工具，来帮助跟踪及记录与访问和处理个人数据、地理数据流，以及第三方对个人数据的访问相关联的活动与事件。 由于云中个人数据的安全性是分担的责任，Microsoft 还为客户提供：

- 有关其自身如何处理客户数据的详细信息

- Microsoft 管理的安全措施

- 在何处以及如何发送客户数据

- Microsoft 自身隐私评审过程的详细信息

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的基于云的多租户目录和标识管理服务。 该服务的登录和审核报告功能提供详细的登录和应用程序使用活动信息，帮助确保以正确的方式访问客户和员工的个人数据。

有两种类型的活动报告：

- [审核活动报告/日志](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)提供系统活动/任务的详细记录

- [登录活动报告/日志](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)显示谁执行了审核报告中列出的每个活动

将两者结合使用，可以跟踪执行的每个任务的历史记录以及执行者是谁。 这两种类型的报告可自定义，并可以筛选。

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>如何访问审核和安全日志？

可在 Active Directory 门户中通过三种不同的方式访问审核和安全：通过 Active Directory 中的“活动”部分（选择“审核日志”或“登录”），或通过“管理”下面的“用户和组”或“企业应用程序”。 此外，可以通过 Azure Active Directory 报告 API 访问报告。 

1. 在 Azure 门户中，选择“Azure Active Directory”。

2. 在“活动”部分中，选择“审核日志”。

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. 在工具栏中单击“列”可自定义列表视图。

4.  在列表视图中选择一个项可查看其所有相关详细信息。

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Azure Active Directory 报告还提供两种类型的安全报告：“标记为有风险的用户”和“有风险的登录”，可帮助监视 Azure 环境中的潜在风险。

有关报告服务的详细信息，请参阅 [Azure Active Directory 报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)。

有关 Azure Active Directory 中提供的报告的更多细节，请访问 [Azure Active Directory 活动报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports)。 此站点包含有关如何在门户中访问和使用[审核日志活动报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)和[登录活动报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)的更多详细信息。 此外，还包含有关[标记为有风险的用户](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk)和[有风险的登录](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins)安全报告的信息。

有关如何以编程方式连接到 Azure Directory 报告的详细信息，请访问 [Azure Active Directory 审核 API 参考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)站点。

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/) 可[从 Azure Monitor 收集数据](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage)，以便将这些数据与其他数据相关联并提供更多分析信息。 Azure Monitor 收集并分析 Azure 环境的监视数据。 

Log Analytics 中的分析工具（例如日志搜索、视图和解决方案）可针对收集的所有数据运行，提供整个环境的集中分析信息。 Log Analytics 可以聚合并分析 Windows 事件日志、IIS 日志和 Syslog，帮助检测出可能会向未经授权的用户透露个人数据的潜在个人数据违规。

#### <a name="how-do-i-use-log-analytics"></a>如何使用 Log Analytics？

可在任何 Web 浏览器中通过 OMS 门户或 Azure 门户访问 Log Analytics。 Log Analytics 提供用于快速检索和整合存储库中数据的查询语言。 可以创建并保存日志搜索，以便直接在门户中分析数据。

若要在 Azure 门户中创建 Log Analytics 工作区，请执行以下操作：

1. 在 Marketplace 中的服务列表内选择“Log Analytics”。

2. 选择“创建”，指定 OMS 工作区的名称，并选择订阅、资源组、位置和定价层。

3. 单击“确定”显示工作区列表。

4. 选择某个工作区可查看其详细信息。

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

访问 [Log Analytics 文档](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)了解有关该服务的详细信息。

访问 [Log Analytics 工作区入门](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)教程创建一个评估工作区，并了解该服务的基本用法。

访问以下网页，了解有关如何建立连接以使用 Log Analytics 和上述日志的其他具体信息：

[Log Analytics 中的 Windows 事件日志数据源](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Log Analytics 中的 IIS 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Log Analytics 中的 Syslog 数据源](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure Monitor/Azure 活动日志 

[Azure Monitor](https://azure.microsoft.com/services/monitor/) 针对 Microsoft Azure 中的大多数服务提供基本级别的基础结构指标和日志。
监视功能有助于深入了解 Azure 应用程序。 Azure Monitor 依赖于 Azure 诊断扩展（Windows 或 Linux）来收集大多数应用程序级指标和日志。 [Azure 活动日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)是可以使用 Azure Monitor 查看的资源之一。 它会跟踪每个 API 调用，并提供有关 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中发生的活动的大量信息。
可以在活动日志（以前称为操作日志或审核日志）中搜索 Azure 基础结构所看到的资源的信息。 

尽管活动日志中记录的许多信息与性能和服务运行状况有关，但还有一些信息与数据的保护相关。 通过活动日志，可确定针对 Azure 订阅中的资源执行的任何写入操作 (PUT, POST, DELETE) 的“操作内容、操作者和操作时间”。

例如，当管理员删除网络安全组（可能会影响个人数据的保护）时，它就会提供一条记录。 活动日志条目在 Azure Monitor 中存储 90 天。

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>如何使用 Azure Monitor 收集的数据？

可通过许多方式来使用活动日志和其他 Azure Monitor 资源中的数据。

- 可将数据实时流式传输到其他位置。

- 可以使用 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-introduction)并设置保留策略，使数据的存储时间比默认期限更长。

- 可以使用 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)、[Azure Application Insights](https://azure.microsoft.com/services/application-insights/)、[Microsoft PowerBI](https://powerbi.microsoft.com/) 或第三方可视化工具在图形和图表中将数据可视化。

- 可以使用 Azure Monitor REST API、CLI 命令、[PowerShell](https://docs.microsoft.com/powershell/) cmdlet 或 .NET SDK 查询数据。

若要开始使用 Azure Monitor，请在 Azure 门户中选择“更多服务”。

1. 在“监视和管理”部分中向下滚动到“Monitor”。

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Monitor 会在“活动日志”视图中打开。

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

可创建并保存常见筛选器的查询，然后将最重要的查询固定到门户仪表板中，便于始终了解是否出现了符合条件的事件。

1. 可按资源组、时间跨度和事件类别筛选视图。

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. 然后，可以通过单击“固定”按钮将查询固定到门户仪表板。 这有助于为服务上的操作数据创建单一信息源。 仪表板上会显示查询名称和结果数目。

此外，可以使用 Monitor 来查看所有 Azure 资源的指标、配置诊断设置和警报，以及搜索日志。 有关如何使用 Azure Monitor 和活动日志的详细信息，请参阅 [Azure Monitor 入门](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)。

### <a name="azure-diagnostics"></a>Azure 诊断 

使用 Azure 中的诊断功能可从不同的源收集数据。 在跟踪和阐述个人数据的保护时，Windows 事件日志（包括安全日志）可能十分有用。 安全日志跟踪登录成功和失败事件、权限更改、指示特定类型的攻击的模式检测、安全相关策略的更改、安全组成员身份更改和其他多种信息。

例如，事件 ID 4695 提醒有人企图取消保护可审核的受保护数据。 此功能与数据保护 API (DPAPI) 相关，可帮助保护私钥、存储的凭据和其他机密信息等数据。

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>如何为 Windows VM 启用诊断扩展？

可以使用 PowerShell 为 Windows VM 启用诊断扩展，以收集日志数据。 启用该扩展的步骤取决于所用的部署模型（资源管理器或经典）。 若要在通过资源管理器部署模型创建的现有 VM 上启用诊断扩展，可以使用 [Set-AzureRMVMDiagnosticsExtension PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1)。

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$$diagnosticsconfig_path* 是指向包含 XML 格式诊断配置的文件的路径。 有关在 VM 上启用 Azure 诊断的其他详细说明，请参阅[使用 PowerShell 在运行 Windows 的虚拟机中启用 Azure 诊断](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)。

Azure 诊断扩展可将收集的数据传输到 Azure 存储帐户，或者发送到 Application Insights 等服务。 然后，可将数据用于审核。

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>如何存储和查看诊断数据？

必须记住，诊断数据不会永久存储，除非将其传输到 Microsoft Azure 存储模拟器或 Azure 存储中。 若要在 Azure 存储中存储和查看诊断数据，请遵循以下步骤：

1. 在 ServiceConfiguration.cscfg 文件中指定一个存储帐户。 Azure 诊断可以根据数据的类型使用 Blob 服务或表服务。 Windows 事件日志以表格格式存储。

2. 传输数据。 可以通过配置文件来请求传输诊断数据。 使用 SDK 2.4 和更低版本时，还可以编程方式发出请求。

3. 在 Visual Studio 中使用 [Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)或[服务器资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)查看数据，或者在 Azure Management Studio 中使用 [Azure 诊断管理器](https://www.cerebrata.com/products/azure-diagnostics-manager)查看数据。

有关如何执行其中每个步骤的详细信息，请参阅[在 Azure 存储中存储和查看诊断数据](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)。

### <a name="azure-storage-analytics"></a>Azure 存储分析 

存储分析记录成功和失败的存储服务请求的详细信息。 此信息可用于监视各个请求，从而有助于阐述对服务中存储的个人数据的访问。 但是，默认未对存储帐户启用存储分析日志记录。 可在 Azure 门户中启用该功能。

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>如何为存储帐户配置监视？

若要为存储帐户配置监视，请执行以下操作：

1. 在 Azure 门户中选择“存储帐户”，并选择要监视的帐户的名称。

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. 在“监视”部分中选择“诊断”。

3.  选择要为每个服务监视的指标数据的**类型**（“Blob”、“表”、“文件”）。 若要指示 Azure 存储保存针对 Blob、表和队列服务发出的读取、写入和删除请求的诊断日志，请选择“Blob 日志”、“表日志”和“队列日志”。

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. 使用底部的滑块设置以天为单位的**保留**策略（值为 1-365）。 默认值为 7 天。

5. 选择“保存”应用配置设置。

存储日志记录日志条目包含有关各个请求的以下信息：

- 时间信息，例如开始时间、端到端延迟和服务器延迟。

- 存储操作的详细信息，例如操作类型、客户端访问的存储对象的键、成功或失败状态，以及返回给客户端的 HTTP 状态代码。

- 身份验证详细信息，例如客户端使用的身份验证类型。

- 并发信息，例如 ETag 值和上次修改时间戳。

- 请求和响应消息的大小。

有关如何启用存储分析日志记录的详细说明，请参阅[在 Azure 门户中监视存储帐户](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)。

### <a name="azure-security-center"></a>Azure 安全中心 

[Azure 安全中心](https://azure.microsoft.com/services/security-center/)会监视 Azure 资源的安全状态，以预防和检测威胁，并提供响应建议。 它提供多种方式来帮助阐述用于保护个人数据隐私的安全措施。

安全运行状况监视有助于确保符合安全策略。 安全监视是对资源进行审核的前瞻性策略，可识别不符合组织标准或最佳做法的系统。 可监视以下资源的安全状态：

- 计算（虚拟机和云服务）

- 网络（虚拟网络）

- 存储和数据（服务器和数据库审核与威胁检测、TDE、存储加密）

- 应用程序（潜在的安全问题）

上述任何类别出现安全问题都可能会威胁到个人数据的隐私。

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>如何查看 Azure 资源的安全状态？

安全中心定期分析 Azure 资源的安全状态。 可在仪表板的“预防”部分中查看安全中心识别到的任何潜在安全漏洞。

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. 在“预防”部分中，选择“计算”磁贴。 此时会显示“概述”，此外还会显示所有 VM 的“虚拟机”列表及其安全状态，以及安全中心监视的 Web 角色和辅助角色的“云服务”列表。

2. 在“概述”选项卡上，选择一项建议可查看更多信息。

3. 在“虚拟机”选项卡上选择 VM 来查看更多详细信息。

在 Azure 安全中心启用数据收集后，会在部署的所有现有以及新的受支持虚拟机上自动预配 Microsoft Monitoring Agent。 通过此代理收集的数据存储在与订阅关联的现有 [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 工作区或新工作区中。

安全中心提供[威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。 这些报告提供有用的信息来帮助识别攻击者的身份、目标、当前和历史攻击活动，以及使用的计谋、工具和过程。 此外，还包含缓解和补救信息。

这些威胁报告的主要用途是帮助有效应对即时威胁，并在事后采取措施来缓解问题。 为报告和审核目的阐述事件响应时，这些报告中的信息也很有用。

威胁智能报告以 .PDF 格式提供，可通过 Azure 安全中心内每个安全警报的“执行了可疑的进程”边栏选项卡的“报告”字段中的链接来访问。

有关如何查看和使用威胁智能报告的详细信息，请参阅 [Azure 安全中心威胁智能报告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。

## <a name="next-steps"></a>后续步骤：

[Azure Active Directory 报告 API 入门](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[什么是 Log Analytics？](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Microsoft Azure 监视概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Azure 活动日志简介（视频）](https://azure.microsoft.com/resources/videos/intro-activity-log/)
