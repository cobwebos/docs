---
title: Azure 安全中心中 IaaS 的高级数据安全性 |Microsoft Docs
description: " 了解如何在 Azure 安全中心为 IaaS 启用高级数据安全性。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: 287da68617a9527bc398df577cf8d10773fa8557
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202170"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Azure 虚拟机上 SQL server 的高级数据安全性 (公共预览版)
Azure 虚拟机上的 SQL Server 高级数据安全是适用于高级 SQL 安全功能的统一包。 目前 (公共预览版) 提供的功能可用于呈现和缓解潜在的数据库漏洞并检测可能指示数据库威胁的异常活动。 

此适用于 Azure Vm 的安全产品/服务基于[AZURE Sql 数据库高级数据安全包](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)中使用的相同基础技术。


## <a name="overview"></a>概述

高级数据安全性提供一组高级 SQL 安全功能, 其中包括漏洞评估和高级威胁防护。

* [漏洞评估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是一项易于配置的服务，可以发现、跟踪并帮助修正潜在的数据库漏洞。 它提供安全状态的可见性, 并包括用于解决安全问题并增强数据库 fortifications 的步骤。
* [高级威胁防护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)可检测异常活动, 这些活动表明访问或利用 SQL server 的异常和潜在有害尝试。 它会持续监视你的数据库中是否存在可疑活动, 并针对异常数据库访问模式提供面向操作的安全警报。 这些警报提供可疑活动的详细信息和建议的操作来调查和缓解威胁。

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Azure Vm 上的 SQL 高级数据安全入门

以下步骤使你开始了解 Azure Vm 公共预览版中的 SQL 高级数据安全。

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>在 Azure Vm 上设置 SQL 高级数据安全性

**开始之前**:需要 Log Analytics 工作区来存储正在分析的安全日志。 如果你没有, 则可以轻松创建一个, 如在[Azure 门户中创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中所述。

1. 将托管 SQL server 的 VM 连接到 Log Analytics 工作区。 有关说明, 请参阅[将 Windows 计算机连接到 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)。

1. 从 Azure Marketplace 中转到[SQL Advanced Data Security 解决方案](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity)。
(您可以使用 marketplace 搜索选项找到它, 如下图中所示。)此时将打开 " **SQL 高级数据安全性**" 页。

    ![IaaS 的高级数据安全性](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. 单击“创建”。 随即显示工作区。

    ![高级数据安全创建](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. 选择要使用的工作区, 然后单击 "**创建**"。

   ![选择工作区](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. 重新启动[VM 的 SQL server](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017)。


## <a name="explore-and-investigate-security-alerts"></a>探索并调查安全警报

你可以查看和管理当前的安全警报。

1. 单击 "**安全中心** > **安全警报**", 然后单击 "警报"。

    ![查找警报](./media/security-center-advanced-iaas-data/find-alert.png)

1. 从 "受**攻击的资源**" 列中, 单击已受到攻击的资源。

1. 若要查看警报的详细信息和操作, 以便调查当前威胁和解决将来的威胁, 请向下滚动 "**常规信息**" 页, 然后在 "**修正步骤**" 部分中, 单击 "**调查步骤**" 链接。

    ![修正步骤](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. 若要查看与触发警报相关联的日志, 请参阅**Log analytics 工作区**并执行以下步骤:

     > [!NOTE]
     > 如果在左侧菜单中未显示**Log analytics 工作区**, 请单击 "**所有服务**", 然后搜索**log analytics 工作区**。

    1. 请确保列显示 "**定价层**" 和 " **WorkspaceID** " 列。 (**Log analytics 工作区** > **编辑列**、添加**定价层**和**WorkspaceID**。)

     ![编辑列](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. 单击包含警报日志的工作区。

    1. 在 "**常规**" 菜单下, 单击 "**日志**"

    1. 单击 " **SQLAdvancedThreatProtection**表" 旁边的眼睛。 列出了这些日志。

     ![查看日志](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>为 ATP 警报设置电子邮件通知 

可以设置在生成 ASC 警报时接收电子邮件通知的收件人列表。 电子邮件包含指向 Azure 安全中心中的警报的直接链接, 其中包含所有相关的详细信息。 

1. 请参阅**安全中心** > **定价 & 设置**, 并单击相关订阅

    ![订阅设置](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 从 "**设置**" 菜单中单击 "**电子邮件通知**"。 
1. 在 "**电子邮件地址**" 文本框中, 输入接收通知的电子邮件地址。 您可以输入多个电子邮件地址, 方法是使用逗号 (,) 分隔电子邮件地址。  例如admin1@mycompany.com,admin2@mycompany.comadmin3@mycompany.com

      ![电子邮件设置](./media/security-center-advanced-iaas-data/email-settings.png)

1. 在**电子邮件通知**设置中, 设置以下选项:
  
    * **发送电子邮件通知以获得高严重性警报**:只发送用于高严重性警报的电子邮件, 而不是发送所有警报的电子邮件。
    * **同时向订阅所有者发送电子邮件通知**:也将通知发送给订阅所有者。

1. 在**电子邮件通知**屏幕的顶部, 单击 "**保存**"。

  > [!NOTE]
  > 在关闭该窗口之前, 请务必单击 "**保存**", 否则将不会保存新的**电子邮件通知**设置。

## <a name="explore-vulnerability-assessment-reports"></a>探索漏洞评估报告

漏洞评估仪表板概述了你的所有数据库中的评估结果。 您可以根据 SQL Server 版本来查看数据库的分布情况, 还可以查看失败与传递数据库的摘要, 以及根据风险分布的失败检查的总体摘要。

你可以直接从 Log Analytics 查看漏洞评估结果和报告。

1. 通过高级数据安全解决方案导航到 Log Analytics 工作区。
1. 导航到 "**解决方案**", 然后选择 " **SQL 漏洞评估**解决方案"。
1. 在**摘要**窗格中, 单击 "**查看摘要**", 然后选择你的**SQL 漏洞评估报告**。

    ![SQL 评估报表](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    报表仪表板将加载。 请确保时间范围至少设置为**最后7天**, 因为漏洞评估扫描按固定计划每7天运行一次。

    ![设置过去7天](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. 若要深入了解更多详细信息, 请单击任何仪表板元素。 例如：

   1. 单击 "**失败的检查摘要**" 部分中的漏洞检查, 查看 Log Analytics 表, 其中包含跨所有数据库进行此检查的结果。 首先列出具有结果的。

   1. 然后, 单击以查看每个漏洞的详细信息, 包括漏洞说明和影响、状态、相关的风险, 以及此数据库的实际结果。 你还可以查看用于执行此检查的实际查询, 以及用于解决此漏洞的补救信息。

    ![选择工作区](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![选择工作区](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. 你可以对漏洞评估结果数据运行任何 Log Analytics 查询, 以根据你的需要对数据进行切片和切切。

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>针对 Azure Vm 上的 SQL Server 的高级威胁防护警报
警报由异常和可能有害的访问或利用 SQL Server 的尝试生成。 这些事件可以触发以下警报：

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>异常访问模式警报 (在公共预览版中受支持)

* **从异常位置访问:** 当 SQL Server 的访问模式发生更改，有人从异常的地理位置登录到 SQL Server 时，会触发此警报。 可能的原因:
     * 攻击者或以前的恶意使用已访问你的 SQL Server。
     * 合法用户已从新位置访问了你的 SQL Server。
* **从可能有害的应用程序访问**: 当有潜在有害的应用程序访问数据库时, 会触发警报。 可能的原因:
     * 攻击者尝试使用常见攻击工具破坏您的 SQL。
     * 合法的渗透测试。
* **来自陌生主体的访问**：当 SQL Server 的访问模式发生更改，有人使用异常的主体（SQL 用户）登录到 SQL Server 时，会触发此警报。 可能的原因:
     * 攻击者或以前的恶意使用已访问你的 SQL Server。 
     * 合法用户已使用新主体从访问了你的 SQL Server。
* **暴力破解 SQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 可能的原因:
     * 攻击者尝试使用暴力破解来破坏您的 SQL。
     * 合法的渗透测试。

### <a name="potential-sql-injection-attacks-coming"></a>潜在的 SQL 注入攻击 (即将推出)

* **存在易受 SQL 注入攻击的漏洞**：当某个应用程序在数据库中生成错误的 SQL 语句时，会触发此警报。 此警报会指示可能存在易受 SQL 注入攻击的漏洞。 可能的原因:
     * 应用程序代码中的缺陷导致构造出错误的 SQL 语句
     * 应用程序代码或存储过程在构造错误的 SQL 语句时无法清理用户输入，使该语句被 SQL 注入攻击利用
* **潜在 SQL 注入**：当 SQL 攻击有效利用已识别到的应用程序漏洞时，会触发此警报。 这意味着，攻击者正在尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。
