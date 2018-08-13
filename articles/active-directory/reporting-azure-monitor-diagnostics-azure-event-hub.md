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
ms.openlocfilehash: 50e7b7c35386bafe521078308a8809c4d3038dd8
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501690"
---
# <a name="tutorial-stream-azure-ad-logs-to-an-azure-event-hub-preview"></a>教程：将 Azure AD 日志流式传输到 Azure 事件中心（预览版）

本教程介绍如何设置 Azure Monitor 诊断设置，以便将 Azure Active Directory (Azure AD) 日志流式传输到 Azure 事件中心。 根据此机制将日志与 Splunk 和 QRadar 等第三方安全信息和事件管理 (SIEM) 工具集成。

## <a name="prerequisites"></a>先决条件 

若要使用此功能，需满足以下条件：

* Azure 订阅。 如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。
* Azure AD 租户。
* 一个是 Azure AD 租户的全局管理员或安全管理员的用户。
* 在 Azure 订阅中有事件中心命名空间和事件中心。 了解如何[创建事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md)。

## <a name="archive-logs-to-an-event-hub"></a>将日志存档到事件中心

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 选择“Azure Active Directory” > “活动” > “审核日志”。 

3. 选择“导出设置”。  
    
4. 在“诊断设置”窗格中，执行下述操作之一：
    * 若要更改现有设置，请选择“编辑设置”。
    * 若要添加新设置，请选择“添加诊断设置”。  
      最多可以有三个设置。

      ![导出设置](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png)

5. 选中“流式传输到事件中心”复选框，然后选择“事件中心/配置”。

6. 选择要将日志路由到其中的 Azure 订阅和事件中心命名空间。  
    订阅和事件中心命名空间必须都与从其流式传输日志的 Azure AD 租户相关联。 也可在应将日志发送到其中的事件中心命名空间中指定一个事件中心。 如果未指定事件中心，则会使用默认名称 **insights-logs-audit** 在命名空间中创建一个事件中心。

7. 选择“确定”，退出事件中心配置。

8. 执行下列两项操作或之一：
    * 若要将审核日志发送到存储帐户，请选中“AuditLogs”复选框。 
    * 若要将登录日志发送到存储帐户，请选中“SignInLogs”复选框。

9. 选择“保存”，保存设置。

    ![诊断设置](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png)

10. 大约 15 分钟后，验证事件是否显示在事件中心。 为此，请从门户转到事件中心，然后验证“传入消息”计数是否大于零。 

    ![审核日志](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>从事件中心访问数据

数据显示在事件中心以后，即可通过两种方式来访问和读取数据：

* **配置支持的 SIEM 工具**。 若要从事件中心读取数据，大多数工具需要事件中心连接字符串和对 Azure 订阅的某些权限。 带有 Azure Monitor 集成的第三方工具包括但不限于：
    * **Splunk**：若要详细了解如何将 Azure AD 日志与 Splunk 集成，请参阅[使用 Azure Monitor 将 Azure AD 日志与 Splunk 集成](reporting-azure-monitor-diagnostics-splunk-integration.md)。
    
    * **IBM QRadar**：DSM 和 Azure 事件中心协议可以从 [IBM 支持](http://www.ibm.com/support)站点下载。 若要详细了解如何与 Azure 集成，请访问 [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) 站点。
    
    * **Sumo Logic**：若要将 Sumo Logic 设置为使用事件中心提供的数据，请参阅 [Collect logs for Azure activity logs from an event hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)（从事件中心收集 Azure 活动日志的日志）。 

* **设置自定义工具**。 如果当前的 SIEM 在 Azure Monitor 诊断中不受支持，则可使用事件中心 API 设置自定义工具。 有关详细信息，请参阅[从事件中心接收消息入门](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph)。


## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 将 Azure AD 日志与 Splunk 集成](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [解释 Azure Monitor 中的审核日志架构](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [解释 Azure Monitor 中的登录日志架构](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
