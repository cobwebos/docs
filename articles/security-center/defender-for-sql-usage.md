---
title: 如何使用适用于 SQL 的 Azure Defender
description: 了解如何使用 Azure 安全中心的可选 Azure Defender for SQL 计划
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2020
ms.author: memildin
ms.openlocfilehash: 76015602cbf949b166c4067ad72bace5d355d70b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933555"
---
# <a name="azure-defender-for-sql-servers-on-machines"></a>计算机上适用于 SQL server 的 Azure Defender 

此 Azure Defender 计划可检测异常活动，指示对访问或利用数据库的异常和潜在有害尝试。

出现可疑的数据库活动、潜在漏洞，或者 SQL 注入攻击以及异常的数据库访问和查询模式时，你会看到警报。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|计价|**计算机上适用于 SQL server 的 Azure Defender**按[定价页](security-center-pricing.md)中所示的方式计费|
|受保护的 SQL 版本：|Azure SQL Server (Microsoft 支持涵盖的所有版本) |
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中国 Gov，其他 Gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>为计算机上的 SQL server 设置 Azure Defender

若要启用此计划：

* 在 SQL server 的主机上预配 Log Analytics 代理。 这可提供到 Azure 的连接。

* 在安全中心的 "定价和设置" 页中启用可选计划。

下面描述了这两种方法。

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>步骤 1。 在 SQL server 的主机上预配 Log Analytics 代理：

- **在 AZURE vm 上 SQL Server** -如果 SQL 计算机托管在 azure vm 上，则可 [自动预配 Log Analytics 代理](security-center-enable-data-collection.md#workspace-configuration)。 或者，您可以按照 [添加 Azure Stack 计算机](quickstart-onboard-machines.md#add-non-azure-computers)的手动过程进行操作。
- **SQL Server 在 Azure arc** 上-如果你的 SQL Server 托管在 [azure arc](https://docs.microsoft.com/azure/azure-arc/) 计算机上，则可以使用安全中心建议部署 Log Analytics 代理 "Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上 (预览版) "。 或者，可以遵循 [Azure Arc 文档](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)中的手动过程。

- **SQL Server 本地** -如果你的 SQL Server 托管在不带 Azure Arc 的本地 Windows 计算机上，则有两个选项可用于将其连接到 azure：
    
    - **部署 Azure Arc** -可以将任何 Windows 计算机连接到安全中心。 但是，Azure Arc 在 *所有* Azure 环境中提供更深入的集成。 如果设置了 Azure Arc，你会在门户中看到 " **SQL Server – Azure Arc** " 页，并且安全警报将显示在该页面上的 "专用 **安全** " 选项卡上。 因此，第一个和推荐的选项是 [在主机上设置 Azure arc](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) 并按照上面的 **azure arc 上 SQL Server**的说明进行操作。
        
    - **不使用 azure Arc 连接 windows 计算机** -如果你选择在不使用 azure arc 的情况下连接在 windows 计算机上运行的 SQL Server，请按照 [将 windows 计算机连接到 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)中的说明进行操作。


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>步骤 2. 在安全中心的 "定价和设置" 页中启用可选计划：

1. 在安全中心的菜单中，打开 " **定价 & 设置** " 页。

    - 如果使用的是 **Azure 安全中心的默认工作区** (名为 "形式 defaultworkspace-[订阅 id]-[region]" ) ，请选择相关 **订阅**。

    - 如果使用 **非默认工作区**，请选择相关 **工作区** (在筛选器中输入工作区的名称（如有必要）) ：

        ![按标题查找非默认工作区](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. 为 **计算机上的 SQL server (预览) ** 计划设置为 **on**的选项。 

    ![具有可选计划的安全中心定价页](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)

    将在连接到所选工作区的所有 SQL server 上启用该计划。 第一次重新启动 SQL Server 实例后，保护将处于完全活动状态。

    >[!TIP] 
    > 若要新建工作区，请按照[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中的说明进行操作。


1. 还可以配置安全警报的电子邮件通知。 
    可设置在生成安全中心警报时接收电子邮件通知的收件人列表。 电子邮件包含指向 Azure 安全中心警报的直接链接，以及所有的相关详细信息。 有关详细信息，请参阅 [设置安全警报的电子邮件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)。



## <a name="explore-vulnerability-assessment-reports"></a>浏览漏洞评估报告

漏洞评估服务每周扫描一次数据库。 扫描在启用服务的一周中的同一天运行。

漏洞评估仪表板概述了所有数据库中的评估结果，以及正常和不正常的数据库的摘要，以及根据风险分布的失败检查的总体摘要。

可以直接在安全中心查看漏洞评估结果。

1. 在 "安全中心" 边栏中，打开 " **建议** " 页，然后选择 " **计算机上的 SQL server **上的建议漏洞" (预览 ") 。 有关详细信息，请参阅[安全中心建议](security-center-recommendations.md)。 

    :::image type="content" source="./media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png" alt-text="计算机上的 SQL server 上的漏洞评估结果应 (预览版进行修正) ":::

    此时显示此建议的详细视图。

    :::image type="content" source="./media/security-center-advanced-iaas-data/all-servers-view.png" alt-text="建议的详细视图":::

1. 有关更多详细信息，请向下钻取：

    * 若要大致了解 (数据库的已扫描资源) 以及经过测试的安全检查列表，请选择相关服务器。

    * 若要大致了解按特定 SQL 数据库分组的漏洞，请选择相关数据库。

    在每个视图中，安全检查按照严重性排序。 单击特定安全检查即可查看详细信息窗格，其中包含说明、修正方法以及影响或基准等其他相关信息   。

## <a name="azure-defender-for-sql-alerts"></a>Azure Defender for SQL 警报
警报由异常和可能有害的访问或利用 SQL 计算机的尝试生成。 这些事件可触发警报 [引用页面的 Sql 数据库和 Azure Synapse Analytics (以前的 Sql 数据仓库) 部分](alerts-reference.md#alerts-sql-db-and-warehouse)中显示的警报。

## <a name="explore-and-investigate-security-alerts"></a>浏览并调查安全警报

在安全中心的 "警报" 页、资源的 "安全性" 选项卡、" [Azure Defender" 仪表板](azure-defender-dashboard.md)中，或通过警报电子邮件中的直接链接提供 Azure Defender 警报。

1. 若要查看警报，请从安全中心的菜单中选择 " **安全警报** "，然后选择一个警报。

1. 警报各自独立，每个警报都包含详细的修正步骤和调查信息。 可以使用其他 Azure 安全中心和 Azure Sentinel 功能进一步调查，从而更深入地了解：

    * 启用 SQL Server 的审核功能以进一步调查。 如果你是 Azure Sentinel 用户，则可以将 SQL 审核日志从 Windows 安全日志事件上传到 Sentinel，并享受内容丰富的调查体验。 [了解 SQL Server 审核的详细信息](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15)。
    * 若要改善安全状况，请采纳每个警报中针对主机的安全中心建议。 这将降低未来攻击的风险。 

    [详细了解如何管理和响应警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。


## <a name="next-steps"></a>后续步骤

有关相关材料，请参阅以下文章：

- [SQL 数据库和 Azure Synapse Analytics (以前的 SQL 数据仓库的安全警报) ](alerts-reference.md#alerts-sql-db-and-warehouse)
- [为安全警报设置电子邮件通知](security-center-provide-security-contact-details.md)
- [了解有关 Azure Sentinel 的详细信息](https://docs.microsoft.com/azure/sentinel/)
- [Azure 安全中心的数据安全包](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)