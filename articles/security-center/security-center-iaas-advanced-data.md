---
title: Azure 安全中心中 IaaS 的高级数据安全性 |微软文档
description: " 了解如何在 Azure 安全中心中为 IaaS 启用高级数据安全性。 "
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
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282726"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure 虚拟机上的 SQL 服务器的高级数据安全性（预览）
Azure 虚拟机上的 SQL 服务器的高级数据安全性是高级 SQL 安全功能的统一包。 此预览功能包括用于识别和缓解潜在数据库漏洞以及检测可能指示数据库受到威胁的异常活动的功能。 

Azure VM SQL 服务器的此安全产品基于[Azure SQL 数据库高级数据安全包](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)中使用的相同基本技术。


## <a name="overview"></a>概述

高级数据安全提供了一组高级 SQL 安全功能，包括漏洞评估和高级威胁保护。

* [漏洞评估](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)是一项易于配置的服务，可以发现、跟踪并帮助修正潜在的数据库漏洞。 它提供了安全状态的可见性，并包括解决安全问题和增强数据库防御工事的步骤。
* [高级威胁防护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)可检测异常活动，指示访问或利用 SQL 服务器的异常和潜在有害尝试。 它持续监视数据库的可疑活动，并在异常数据库访问模式上提供面向操作的安全警报。 这些警报提供可疑活动详细信息，并建议采取行动调查和减轻威胁。

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>开始在 Azure VM 上使用 SQL 的高级数据安全性

以下步骤可开始在 Azure VM 公共预览版上使用 SQL 的高级数据安全性。

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>为 Azure VM 上的 SQL 设置高级数据安全性

在订阅/工作区级别为虚拟机上的 SQL 服务器启用高级数据安全性：
 
1. 从安全中心的侧边栏，打开**定价&设置**页面。

1. 选择要为其启用 Azure VM 上的 SQL 高级数据安全性的订阅或工作区。

1. 将**VM 上 SQL 服务器（预览版）** 的选项切换为已启用。 

    （单击屏幕截图展开）

    [![安全中心建议和警报，如 Windows 管理中心所示](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    SQL 服务器的高级数据安全性将在连接到所选工作区或所选订阅的默认工作区的所有 SQL 服务器上启用。

    >[!NOTE]
    > 首次重新启动 SQL 服务器后，解决方案将完全处于活动状态。 

要创建新工作区，请按照[创建日志分析工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中的说明操作。

要将 SQL Server 的主机连接到工作区，请按照[将 Windows 计算机连接到 Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)中的说明进行操作。


## <a name="set-up-email-notification-for-security-alerts"></a>为安全警报设置电子邮件通知 

您可以将收件人列表设置为在生成安全中心警报时接收电子邮件通知。 电子邮件包含指向 Azure 安全中心警报的直接链接，其中包含所有相关详细信息。 

1. 转到**安全中心** > **定价&设置**，然后单击相关订阅

    ![订阅设置](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. 在"设置"菜单中，单击 **"电子邮件通知**"。 
1. 在 **"电子邮件地址**文本"框中，输入电子邮件地址以接收通知。 您可以通过用逗号 （，） 分隔电子邮件地址来输入多个电子邮件地址。  例如admin1@mycompany.com，admin2@mycompany.comadmin3@mycompany.com

    ![电子邮件设置](./media/security-center-advanced-iaas-data/email-settings.png)

1. 在**电子邮件通知**设置中，设置以下选项：
  
    * **发送高严重性警报的电子邮件通知**：不要为所有警报发送电子邮件，而是仅发送高严重性警报。
    * **还会向订阅所有者发送电子邮件通知**：也向订阅所有者发送通知。

1. 在**电子邮件通知**屏幕的顶部，单击"**保存**"。

  > [!NOTE]
  > 请务必在关闭窗口之前单击 **"保存**"，否则将不会保存新的**电子邮件通知**设置。

## <a name="explore-vulnerability-assessment-reports"></a>浏览漏洞评估报告

漏洞评估仪表板提供所有数据库评估结果的概览。 您可以根据 SQL Server 版本查看数据库的分布，以及失败数据库与传递数据库的摘要以及根据风险分布进行失败检查的总体摘要。

您可以直接从安全中心查看漏洞评估结果。

1. 从安全中心的侧边栏中，在"资源安全"下，选择 **"数据存储&存储**"。

1. 选择建议在**VM 中的 SQL 数据库中的漏洞应修复（预览）。** 有关详细信息，请参阅[安全中心建议](security-center-recommendations.md)。 

    [![[VM 中 SQL 数据库中的漏洞应修复（预览）] 建议](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    将显示此建议的详细视图。

    [![应修复 VM 中 SQL 数据库上的 [漏洞]的详细视图（预览）* 建议](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 要向下钻取以了解更多详细信息，

    * 有关扫描的资源（数据库）和已测试的安全检查列表的概述，请单击感兴趣的服务器。
    [![由 SQL 服务器分组的漏洞](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * 有关由特定 SQL 数据库分组的漏洞的概述，请单击感兴趣的数据库。
    [![由 SQL 服务器分组的漏洞](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    在每个视图中，安全检查按**严重性**排序。 单击特定的安全检查以查看包含 **"说明**"、"如何**修复**它"的详细信息窗格以及其他相关信息，如**影响**或**基准。**

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Azure VM 警报上的 SQL 服务器的高级威胁保护
警报是由访问或利用 SQL 服务器的异常且可能有害的尝试生成的。 这些事件可以触发以下警报：

### <a name="anomalous-access-pattern-alerts-preview"></a>异常访问模式警报（预览）

* **从异常位置访问：** 当 SQL 服务器的访问模式发生更改时，将触发此警报，此时有人从异常地理位置登录到 SQL 服务器。 可能的原因：
    * 攻击者或以前的恶意雇佣人员已访问您的 SQL Server。
    * 合法用户已从新位置访问 SQL Server。
* **从可能有害的应用程序访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 可能的原因：
    * 攻击者试图使用常用攻击工具破坏您的 SQL。
    * 操作中的合法渗透测试。
* **从不熟悉的主体访问**：当 SQL Server 的访问模式发生更改，有人使用异常的主体（SQL 用户）登录到 SQL Server 时，会触发此警报。 可能的原因：
    * 攻击者或以前的恶意雇佣人员已访问您的 SQL Server。 
    * 合法用户已使用新主体访问 SQL Server。
* **暴力破解 SQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 可能的原因：
    * 攻击者试图使用暴力破坏您的 SQL。
    * 操作中的合法渗透测试。

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>潜在的 SQL 注入攻击（在 SQL Server 2019 中支持）

* **SQL 注入的漏洞**：当应用程序在数据库中生成错误的 SQL 语句时触发此警报。 此警报会指示可能存在易受 SQL 注入攻击的漏洞。 可能的原因：
    * 应用程序代码中的缺陷导致构造出错误的 SQL 语句
    * 应用程序代码或存储过程在构造错误的 SQL 语句时无法清理用户输入，使该语句被 SQL 注入攻击利用
* **潜在的 SQL 注入**：当 SQL 攻击有效利用已识别到的应用程序漏洞时，会触发此警报。 这意味着，攻击者正在尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。


### <a name="unsafe-command-supported-in-sql-server-2019"></a>不安全命令（在 SQL Server 2019 中支持）

* **潜在不安全操作**：在执行高度特权且可能不安全的命令时触发此警报。 可能的原因：
    * 启用建议禁用用于更好安全状态的命令。
    * 攻击者试图利用 SQL 访问或升级权限。   


## <a name="explore-and-investigate-security-alerts"></a>探索和调查安全警报

您的数据安全警报可在安全中心的警报页面、资源的安全选项卡中或通过警报电子邮件中的直接链接提供。

1. 查看警报：

    * 在安全中心 - 单击侧边栏**的安全警报**并选择警报。
    * 在资源范围内 - 打开相关资源页，并从边栏单击 **"安全**"。 

1. 警报设计为自包含，每个警报都有详细的补救步骤和调查信息。 您可以使用其他 Azure 安全中心和 Azure 哨兵功能进行更广泛的视图进一步调查：

    * 启用 SQL Server 的审核功能以进行进一步调查。 如果您是 Azure Sentinel 用户，则可以将 SQL 审核日志从 Windows 安全日志事件上载到 Sentinel 并享受丰富的调查体验。
    * 要改进安全状态，请使用安全中心对每个警报中指示的主机的建议。 这将降低未来攻击的风险。 


## <a name="next-steps"></a>后续步骤

有关相关材料，请参阅以下文章：

- [如何修正建议](security-center-remediate-recommendations.md)