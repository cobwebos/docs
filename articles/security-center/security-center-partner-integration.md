---
title: 在 Azure 安全中心集成安全解决方案 | Microsoft Docs
description: 了解如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/3/2019
ms.author: rkarlin
ms.openlocfilehash: 5a25ba889ebde750937f88e447123263a82d5627
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896817"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 安全中心集成安全解决方案
本文档介绍如何管理已连接到 Azure 安全中心的安全解决方案，以及如何添加新的安全解决方案。

## <a name="integrated-azure-security-solutions"></a>集成式 Azure 安全解决方案
可以通过安全中心轻松地在 Azure 中启用集成式安全解决方案。 优势包括：

- **简化部署**：安全中心提供对集成式合作伙伴解决方案的简化预配。 对于反恶意软件和漏洞评估之类的解决方案，安全中心可以在虚拟机上预配所需的代理；对于防火墙设备，安全中心可以负责大部分必需的网络配置。
- **集成检测**：自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- **统一的运行状况监视和管理**：客户可以使用集成式运行状况事件，一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。

目前，集成式安全解决方案包括：

- Web 应用程序防火墙（[Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/products.html)、[Azure 应用程序网关](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)）
- 下一代防火墙（[Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](https://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2)、[Cisco](https://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) 和 [Palo Alto Networks](https://www.paloaltonetworks.com/products)）
- 漏洞评估（[Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) 和 [Rapid7](https://www.rapid7.com/products/insightvm/)）

> [!NOTE]
> 安全中心不会在合作伙伴虚拟设备上安装 Microsoft Monitoring Agent，因为大多数安全供应商都禁止在其设备上运行外部代理。
>
>

## <a name="how-security-solutions-are-integrated"></a>安全中心如何集成
从安全中心部署的 Azure 安全解决方案是自动连接的。 还可以连接其他安全数据源，包括：

- Azure AD Identity Protection
- 在本地或其他云中运行的计算机
- 支持通用事件格式 (CEF) 的安全解决方案
- Microsoft 高级威胁分析

![合作伙伴解决方案集成](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理集成式 Azure 安全解决方案和其他数据源

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。

2. 在 **Microsoft Azure 菜单**上选择“安全中心”。 此时会打开“安全中心 - 概览”。

3. 在“安全中心”菜单下，选择“安全解决方案”。

   ![安全中心概述](./media/security-center-partner-integration/overview.png)

在“安全解决方案”下，可以查看集成式 Azure 安全解决方案的运行状况信息，并执行基本的管理任务。 还可以连接其他类型的安全数据源，例如通用事件格式 (CEF) 的 Azure Active Directory Identity Protection 警报和防火墙日志。

### <a name="connected-solutions"></a>已连接解决方案

“已连接解决方案”部分包括目前连接到安全中心的安全解决方案，以及每个解决方案的运行状况信息。  

![已连接解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

合作伙伴解决方案的状态可能为：

* 正常（绿色）- 没有运行状况问题。
* 不正常（红色）- 有一个运行状况问题需要立即注意。
* 运行状况问题（橙色）- 解决方案已停止报告其运行状况。
* 未报告（灰色）- 解决方案未报告任何内容，如果它最近已连接且仍在部署或者没有运行状况数据可用，则可能不会报告解决方案的状态。

> [!NOTE]
> 如果没有运行状况数据可用，则安全中心会显示上次收到事件的日期和时间以指示解决方案是否正在报告。 如果没有运行状况数据可用且在过去 14 天内没有收到警报，则安全中心会指出解决方案不正常或未在报告。
>
>

1. 选择“查看”以了解其他信息和选项，其中包括：

   - **解决方案控制台**。 打开此解决方案的管理体验。
   - **链接 VM**。 打开“链接应用程序”边栏选项卡。 此处，可将资源连接到合作伙伴解决方案。
   - **删除解决方案**。
   - **配置**。

   ![合作伙伴解决方案详细信息](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>已发现解决方案

安全中心自动发现在 Azure 中运行但未连接到安全中心的安全解决方案，然后在“发现的解决方案”部分显示这些解决方案。 其中包括 Azure 解决方案，例如 [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)，以及合作伙伴解决方案。

> [!NOTE]
> 在订阅级别，安全中心标准层是已发现解决方案功能所必需的。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
>
>

在解决方案下选择“连接”，以便集成安全中心，获取有关安全警报的通知。

![已发现解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

安全中心还发现部署在订阅中的那些能够转发常见事件格式 (CEF) 日志的解决方案。 了解如何[连接安全解决方案](quick-security-solutions.md)，以便将使用 CEF 日志的安全解决方案连接到安全中心。

### <a name="add-data-sources"></a>添加数据源

“添加数据源”部分包括其他可以连接的可用数据源。 如需从任何此类源添加数据的说明，请单击“添加”。

![数据源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

### <a name="connect-external-solutions"></a>连接外部解决方案

除了从计算机收集安全数据，还可以从各种其他的安全解决方案（包括支持通用事件格式 (CEF) 的解决方案）集成安全数据。 CEF 是基于 Syslog 消息的行业标准格式，许多安全产品供应商使用它在不同平台之间进行事件集成。

本快速入门演示如何：
- 使用 CEF 日志将安全解决方案连接到安全中心
- 验证与安全解决方案的连接

#### <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/free/)。

若要逐步执行本快速入门，你必须位于安全中心的“标准”定价层。 可以免费试用安全中心标准版。 快速入门教程“[将 Azure 订阅载入到安全中心标准版](security-center-get-started.md)”详细介绍了如何升级到标准版。 若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

此外还需一台 [Linux 计算机](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux)，其 Syslog 服务已连接到安全中心。

#### <a name="connect-solution-using-cef"></a>使用 CEF 连接解决方案

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。
2. 在 **Microsoft Azure** 菜单上选择“安全中心”。 此时会打开“安全中心 - 概览”。

    ![选择安全中心](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. 在安全中心主菜单下，选择“安全解决方案”。
4. 在“安全解决方案”页的“添加数据源(3)”下，单击“通用事件格式”下的“添加”。

    ![添加数据源](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. 在“通用事件格式日志”页中展开第二步：**将 Syslog 转发配置为向 UDP 端口 25226 上的代理发送所需日志**，然后在 Linux 计算机中按以下说明操作：

    ![配置 syslog](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. 展开第三步：**将代理配置文件放置在代理计算机上**，然后在 Linux 计算机中按以下说明操作：

    ![代理配置](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. 展开第四步：**重启 syslog 守护程序和代理**，然后在 Linux 计算机中按以下说明操作：

    ![重启 syslog](./media/quick-security-solutions/quick-security-solutions-fig5.png)


#### <a name="validate-the-connection"></a>验证连接

在执行以下步骤之前，需耐心等待，直到 syslog 开始向安全中心报告。 这可能需要一些时间，具体取决于环境大小。

1.  在“安全中心”仪表板的左窗格中，单击“搜索”。
2.  选择 Syslog（Linux 计算机）连接到的工作区。
3.  键入 *CommonSecurityLog* 并单击“搜索”按钮。

以下示例显示了这些步骤的结果：![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

#### <a name="clean-up-resources"></a>清理资源
本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续学习后续的快速入门和教程，请继续运行“标准”层并让自动预配保持启用状态。 如果不打算继续或想要返回到“免费”层，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。
2. 选择要返回到“免费”层的订阅或策略。 此时会打开“安全策略”。
3. 在“策略组件”下选择“定价层”。
4. 选择“免费”，将订阅从“标准”层更改为“免费”层。
5. 选择“保存”。

如果希望禁用自动预配，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。
2. 选择希望禁用自动设置的订阅。
3. 在“安全策略 - 数据收集”下的“载入”下选择“关闭”，禁用自动预配。
4. 选择“保存”。

>[!NOTE]
> 禁用自动设置不会从已预配代理的 Azure VM 中删除 Microsoft Monitoring Agent。 禁用自动设置会限制对资源的安全监视。
>

## <a name="exporting-data-to-a-siem"></a>将数据导出到 SIEM

Azure 安全中心生成的事件经过处理后，将被发布到 Azure [活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)，这是 Azure Monitor 提供的可用日志类型之一。 Azure Monitor 提供了一个综合管道，可将任何监视数据路由到 SIEM 工具。 要实现此目的，需将数据流式传输至事件中心，然后在此处将数据拉取至合作伙伴工具。

此管道使用 [Azure 监视单一管道](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)来访问 Azure 环境中的监视数据。 这使得你能轻松设置 SIEM 和监视工具来使用数据。

以下各节介绍如何将数据配置为流式传输到事件中心。 步骤假设 Azure 订阅中已配置了 Azure 安全中心。

综合概述

![高级概述](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>公开给 SIEM 的 Azure 安全数据是什么？

在此版本中，我们将公开[安全警报。](../security-center/security-center-managing-and-responding-alerts.md) 在即将发布的版本中，我们将通过安全建议丰富数据集。

### <a name="how-to-setup-the-pipeline"></a>如何设置管道

#### <a name="create-an-event-hub"></a>创建事件中心

开始之前，需[创建事件中心命名空间](../event-hubs/event-hubs-create.md)。 此命名空间和事件中心是所有监视数据的目标。

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>将 Azure 活动日志流式传输到事件中心

请参阅以下文章：[将活动日志流式传输到事件中心](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>安装合作伙伴 SIEM 连接器 

通过 Azure Monitor 将监视数据路由到事件中心，可与合作伙伴 SIEM 和监视工具轻松集成。

请参阅以下链接，查看[支持的 SIEM](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub) 列表

### <a name="example-for-querying-data"></a>查询数据示例 

以下 Splunk 查询可用于拉取警报数据：

| **查询说明** | **查询** |
|----|----|
| 所有警报| index=main Microsoft.Security/locations/alerts|
| 按名称汇总操作计数| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| 获取警报信息：时间、名称、状态、ID 和订阅 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>后续步骤

本文介绍了如何在安全中心集成合作伙伴的解决方案。 若要详细了解安全中心，请参阅以下文章：

* [将 Microsoft Advanced Threat Analytics 连接到 Azure 安全中心](security-center-ata-integration.md)
* [将 Azure Active Directory Identity Protection 连接到 Azure 安全中心](security-center-aadip-integration.md)
* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。 了解如何监视 Azure 资源的运行状况。
* [通过安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)。 了解如何监视合作伙伴解决方案的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)。 获取安全中心使用方面的常见问题解答。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。
