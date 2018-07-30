---
title: 教程 - 将 Azure Active Directory 日志流式传输到 Azure 事件中心（预览版）| Microsoft Docs
description: 了解如何设置 Azure 诊断，以便将 Azure Active Directory 日志推送到事件中心（预览版）
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240147"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心（预览版）

本教程介绍如何设置 Azure Monitor 诊断设置，以便将 Azure Active Directory 日志流式传输到 Azure 事件中心。 根据此机制将日志与 Splunk 和 QRadar 等第三方 SIEM 工具集成。

## <a name="prerequisites"></a>先决条件 

需要：

* Azure 订阅。 如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。
* 一个 Azure Active Directory 租户
* 一个是该租户的全局管理员或安全管理员的用户
* 在 Azure 订阅中有事件中心命名空间和事件中心。 [在此处了解如何创建一个](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md)。

## <a name="archive-logs-to-event-hub"></a>将日志存档到事件中心

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 单击“Azure Active Directory” -> “活动” -> “审核日志”。 
3. 单击“导出设置”以打开“诊断设置”边栏选项卡。 如果要更改现有设置，请单击“编辑设置”，若要要添加新设置，请单击“添加诊断设置”。 最多可以有三个设置。 
    ![导出设置](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "导出设置")

4. 勾选“流式传输到事件中心”复选框，然后单击“事件中心/配置”。
5. 选择要将日志路由到其中的 Azure 订阅和事件中心命名空间。 订阅和事件中心命名空间必须都与从其流式传输日志的 Active Directory 租户相关联。 也可在应将日志发送到其中的事件中心命名空间中指定一个事件中心。 如果未指定事件中心，则会使用默认名称 **insights-logs-audit** 在命名空间中创建一个事件中心。
6. 单击“确定”，退出事件中心配置。
7. 选中“AuditLogs”复选框可将审核日志发送到存储帐户。 
8. 选中“SignInLogs”复选框可将登录日志发送到存储帐户。
9. 单击“保存”以保存设置。
    ![诊断设置](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "诊断设置")

10. 大约 15 分钟后，验证事件是否显示在事件中心。 为此，请从门户导航到事件中心，然后验证“传入消息”计数是否大于零。 
    ![审核日志](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "审核日志")


## <a name="access-data-from-event-hubs"></a>从事件中心访问数据

数据显示在事件中心以后，即可通过两种方式来访问它。

* **配置可以读取数据的 SIEM 工具**：大多数工具需要事件中心连接字符串和对 Azure 订阅的某些权限，才能从事件中心读取数据。 下面是与 Azure Monitor 集成的工具的不完整列表：
    1. **Splunk**：若要详细了解如何将 Azure AD 日志与 Splunk 集成，请参阅[如何使用 Azure Monitor 将 Azure Active Directory 日志与 Splunk 集成](reporting-azure-monitor-diagnostics-splunk-integration.md)。
    
    2. **IBM QRadar**：Microsoft Azure DSM 和 Microsoft Azure 事件中心协议均可从 [IBM 支持网站](http://www.ibm.com/support)下载。 可以[在此处了解 Azure 集成](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)。
    
    3. **SumoLogic**：按照[这些说明](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)将 SumoLogic 设置为使用事件中心提供的数据。 

* **设置用于读取数据的自定义工具**：如果当前的 SIEM 在 Azure Monitor 诊断中不受支持，则可使用事件中心 API 设置自定义工具。 有关详细信息，请参阅[事件中心 API](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph)。


## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 诊断将 Azure Active Directory 日志与 Splunk 集成](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [解释 Azure Monitor 诊断中的审核日志架构](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解释 Azure Monitor 诊断中的登录日志架构](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)