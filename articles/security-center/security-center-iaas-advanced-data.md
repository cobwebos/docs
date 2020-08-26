---
title: 'Azure 安全中心的 SQL 计算机高级数据安全 (预览版) '
description: 了解如何在 Azure 安全中心为 SQL 计算机启用高级数据安全性
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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: e0085ef5213853a1577ec039d5e360114aa7c64e
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566261"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a> (预览版的 SQL 计算机高级数据安全性) 

适用于 SQL 计算机的 azure 安全中心高级数据安全保护在 Azure 中托管的 SQL Server、其他云环境，甚至是本地计算机。 这会扩展 Azure 本机 SQL Server 的保护，以完全支持混合环境。

此预览功能包含用于识别和缓解潜在的数据库漏洞并检测可能指示数据库威胁的异常活动的功能： 

* **漏洞评估** -扫描服务，用于发现、跟踪和帮助您修正潜在的数据库漏洞。 评估扫描概述了 SQL 计算机的安全状态以及任何安全发现的详细信息。

* [高级威胁防护](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) -用于持续监视 sql 服务器是否受到威胁（例如 sql 注入、暴力攻击和权限滥用）的检测服务。 此服务在 Azure 安全中心提供面向操作的安全警报，并提供可疑活动的详细信息、有关如何缓解威胁的指导，以及用于继续调查 Azure Sentinel 的选项。

>[!TIP]
> SQL 虚拟机的高级数据安全是 Azure 安全中心的 [高级数据安全包](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)的扩展，适用于 Azure SQL 数据库、azure SYNAPSE 和 SQL 托管实例。


## <a name="set-up-advanced-data-security-for-sql-machines"></a>为 SQL 计算机设置高级数据安全性 

为 SQL 计算机设置 Azure 安全中心的高级数据安全性涉及两个步骤：

* 在 SQL server 的主机上预配 Log Analytics 代理。 这可提供到 Azure 的连接。

* 在安全中心的 "定价和设置" 页中启用可选捆绑。

下面描述了这两种方法。

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>步骤 1。 在 SQL server 的主机上预配 Log Analytics 代理：

- **在 AZURE vm 上 SQL Server** -如果 SQL 计算机托管在 azure vm 上，则可 [自动预配 Log Analytics 代理](security-center-enable-data-collection.md#workspace-configuration)。 或者，可以按照手动步骤 [添加 AZURE VM](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines)。

- **SQL Server 在 Azure arc** 上-如果你的 SQL Server 托管在 [azure arc](https://docs.microsoft.com/azure/azure-arc/) 计算机上，则可以使用安全中心建议部署 Log Analytics 代理 "Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上 (预览版) "。 或者，可以遵循 [Azure Arc 文档](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal)中的手动过程。

- **SQL Server 本地** -如果你的 SQL Server 托管在不带 Azure Arc 的本地 Windows 计算机上，则有两个选项可用于将其连接到 azure：
    
    - **部署 Azure Arc** -可以将任何 Windows 计算机连接到安全中心。 但是，Azure Arc 在 *所有* Azure 环境中提供更深入的集成。 如果设置了 Azure Arc，你会在门户中看到 " **SQL Server – Azure Arc** " 页，并且安全警报将显示在该页面上的 "专用 **安全** " 选项卡上。 因此，第一个和推荐的选项是 [在主机上设置 Azure arc](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) 并按照上面的 **azure arc 上 SQL Server**的说明进行操作。
        
    - **不使用 azure Arc 连接 windows 计算机** -如果你选择在不使用 azure arc 的情况下连接在 windows 计算机上运行的 SQL Server，请按照 [将 windows 计算机连接到 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)中的说明进行操作。


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>步骤 2. 在安全中心的 "定价和设置" 页中启用可选捆绑：

1. 在安全中心边栏中，打开“定价和设置”页。

    - 如果使用的是 **Azure 安全中心的默认工作区** (名为 "形式 defaultworkspace-[订阅 id]-[region]" ) ，请选择相关 **订阅**。

    - 如果使用 **非默认工作区**，请选择相关 **工作区** (在筛选器中输入工作区的名称（如有必要）) ：

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. 切换 ** (预览) 计算机上的 SQL server ** 的选项为 "已启用"。 

    [![包含可选捆绑包的安全中心定价页](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    将在连接到所选工作区的所有 SQL server 上启用对计算机上的 SQL server 的高级数据安全性。 第一次重新启动 SQL Server 实例后，保护将处于完全活动状态。

    >[!TIP] 
    > 若要新建工作区，请按照[创建 Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)中的说明进行操作。


1. 还可以配置安全警报的电子邮件通知。 
    可设置在生成安全中心警报时接收电子邮件通知的收件人列表。 电子邮件包含指向 Azure 安全中心警报的直接链接，以及所有的相关详细信息。 有关详细信息，请参阅 [设置安全警报的电子邮件通知](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)。



## <a name="explore-vulnerability-assessment-reports"></a>浏览漏洞评估报告

漏洞评估服务每周扫描一次数据库。 扫描在启用服务的一周中的同一天运行。

漏洞评估仪表板概述了所有数据库中的评估结果，以及正常和不正常的数据库的摘要，以及根据风险分布的失败检查的总体摘要。

可以直接在安全中心查看漏洞评估结果。

1. 在 "安全中心" 边栏中，打开 " **建议** " 页，然后选择 " **计算机上的 SQL server **上的建议漏洞" (预览 ") 。 有关详细信息，请参阅[安全中心建议](security-center-recommendations.md)。 


    [![* * 计算机上的 SQL server 上的漏洞应 (预览版进行修正) * * 建议](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    此时显示此建议的详细视图。

    [![计算机上的 SQL server 上的 * * 漏洞的详细视图应 (预览版进行修正) * * 建议](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. 有关更多详细信息，请向下钻取：

    * 若要大致了解 (数据库的已扫描资源) 以及经过测试的安全检查列表，请选择相关服务器。

    * 若要大致了解按特定 SQL 数据库分组的漏洞，请选择相关数据库。

    在每个视图中，安全检查按照严重性排序。 单击特定安全检查即可查看详细信息窗格，其中包含说明、修正方法以及影响或基准等其他相关信息   。

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>针对计算机上的 SQL server 的高级威胁防护警报
警报由异常和可能有害的访问或利用 SQL 计算机的尝试生成。 这些事件可触发警报 [引用页的 Sql 数据库和 Sql 数据仓库的警报部分](alerts-reference.md#alerts-sql-db-and-warehouse)中显示的警报。



## <a name="explore-and-investigate-security-alerts"></a>浏览并调查安全警报

安全警报在安全中心的 "警报" 页、资源的 "安全" 选项卡或通过警报电子邮件的直接链接中提供。

1. 若要查看警报，请从安全中心的边栏中选择 " **安全警报** "，然后选择一个警报。

1. 警报各自独立，每个警报都包含详细的修正步骤和调查信息。 可以使用其他 Azure 安全中心和 Azure Sentinel 功能进一步调查，从而更深入地了解：

    * 启用 SQL Server 的审核功能以进一步调查。 如果你是 Azure Sentinel 用户，则可以将 SQL 审核日志从 Windows 安全日志事件上传到 Sentinel，并享受内容丰富的调查体验。 [了解 SQL Server 审核的详细信息](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15)。
    * 若要改善安全状况，请采纳每个警报中针对主机的安全中心建议。 这将降低未来攻击的风险。 

    [详细了解如何管理和响应警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。


## <a name="next-steps"></a>后续步骤

有关相关材料，请参阅以下文章：

- [SQL 数据库和 SQL 数据仓库的安全警报](alerts-reference.md#alerts-sql-db-and-warehouse)
- [为安全警报设置电子邮件通知](security-center-provide-security-contact-details.md)
- [了解有关 Azure Sentinel 的详细信息](https://docs.microsoft.com/azure/sentinel/)
- [Azure 安全中心的高级数据安全包](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)