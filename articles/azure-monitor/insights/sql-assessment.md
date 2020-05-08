---
title: 通过 Azure Monitor 优化 SQL Server 环境 |Microsoft Docs
description: 借助 Azure Monitor，可以使用 SQL 运行状况检查解决方案定期评估环境的风险和运行状况。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: b6b32f9eadc6677bad591f4040981c4c95bf1f76
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871240"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>使用 Azure Monitor 中的 SQL Server 运行状况检查解决方案优化 SQL 环境

![SQL 运行状况检查符号](./media/sql-assessment/sql-assessment-symbol.png)

可以使用 SQL 运行状况检查解决方案定期评估服务器环境的风险和运行状况。 本文将帮助你安装该解决方案，以便针对潜在问题采取纠正措施。

此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。 这些建议对六个重点领域的问题进行了分类，可帮助快速了解风险并采取纠正措施。

提出的建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 每一项建议都会就为何问题可能对你至关重要以及如何实施建议更改提供相关指导。

可以选择对组织最重要的重点领域，并跟踪一个运行正常无风险环境的进度。

在添加解决方案并完成评估后，会在环境中基础结构的“SQL 运行状况检查”  仪表板上显示有关重点领域的摘要信息。 以下部分介绍如何使用“SQL 运行状况检查”  仪表板上的信息，可以在其中查看并针对 SQL 服务器基础结构采取建议的操作。

![SQL 运行状况检查磁贴的图像](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![SQL 运行状况检查仪表板的图像](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>必备条件

* SQL 运行状况检查解决方案要求在每台装有 Microsoft Monitoring Agent (MMA) 的计算机上安装受支持版本的 .NET Framework 4.6.2。  MMA 代理由 System Center 2016 - Operations Manager 和 Operations Manager 2012 R2 以及 Azure Monitor 使用。  
* 此解决方案支持 SQL Server 版本2012、2014、2016、2017和2019。
* 一个 Log Analytics 工作区，用于在 Azure 门户中通过 Azure 市场添加 SQL 运行状况检查解决方案。 只有 Azure 订阅中的管理员或参与者才能安装该解决方案。

  > [!NOTE]
  > 添加该解决方案后，AdvisorAssessment.exe 文件会随代理添加到服务器中。 读取配置数据，然后将其发送到云中的 Azure Monitor 进行处理。 逻辑应用于接收的数据，云服务则记录数据。
  >
  >

若要针对 SQL Server 服务器执行运行状况检查，这些域控制器需要一个代理，并使用以下受支持的方法之一与 Azure Monitor 建立连接：

1. 如果该服务器尚不受 System Center 2016 - Operations Manager 或 Operations Manager 2012 R2 的监视，请安装 [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md)。
2. 如果该服务器受 System Center 2016 - Operations Manager 或 Operations Manager 2012 R2 的监视并且管理组未与 Azure Monitor 集成，则它可与 Log Analytics 共用多个宿主，以收集数据并将其转发到服务，同时仍可由 Operations Manager 监视。  
3. 否则，如果 Operations Manager 管理组已与服务集成，则在工作区中启用解决方案后，需要遵循[添加代理管理的计算机](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor)中的步骤，为数据收集服务添加域控制器。  

SQL Server 上的代理向 Operations Manager 管理组报告、收集数据、将数据转发到为其分配的管理服务器，然后将数据从管理服务器直接发送到 Azure Monitor。  数据不会写入 Operations Manager 数据库。  

如果 SQL Server 受 Operations Manager 的监视，则需要配置 Operations Manager 运行方式帐户。 有关详细信息，请参阅下面的 [Azure Monitor 的 Operations Manager 运行方式帐户](#operations-manager-run-as-accounts-for-log-analytics)。

## <a name="sql-health-check-data-collection-details"></a>SQL 运行状况检查数据集合详细信息
SQL 运行状况检查使用已启用的代理收集以下来源的数据：

* Windows 管理规范 (WMI)
* 注册表
* 性能计数器
* SQL Server 动态管理视图结果

数据从 SQL Server 收集，并每隔七天转发到 Log Analytics。

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Log Analytics 的 Operations Manager 运行方式帐户
Log Analytics 使用 Operations Manager 代理和管理组来收集数据并将数据发送到 Log Analytics 服务。 Log Analytics 基于工作负荷的管理包生成，提供增值服务。 每个工作负荷需要特定于工作负荷的权限，才能在不同的安全上下文（例如域用户帐户）中运行管理包。 需要通过配置 Operations Manager 运行方式帐户提供凭据信息。

使用以下信息为 SQL 运行状况检查设置 Operations Manager 运行方式帐户。

### <a name="set-the-run-as-account-for-sql-health-check"></a>为 SQL 运行状况检查设置运行方式帐户
 如果已在使用 SQL Server 管理包，应使用该运行方式配置。

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>在操作控制台中配置 SQL 运行方式帐户
> [!NOTE]
> 默认情况下，管理包中的工作流在本地系统帐户的安全上下文中运行。 如果使用的 Microsoft Monitoring Agent 直接连接到服务而不是直接向 Operations Manager 管理组报告，请跳过下面的步骤 1-5，运行 T-SQL 或 PowerShell 示例，并指定 NT AUTHORITY\SYSTEM 作为用户名。
>
>

1. 在 Operations Manager 中，打开操作控制台，并单击“管理”  。
2. 在“运行方式配置”  下，单击“配置文件”  ，并打开“SQL 评估运行方式配置文件”  。
3. 在“运行方式帐户”  页上，单击“添加”  。
4. 选择包含 SQL Server 所需凭据的 Windows 运行方式帐户，或者单击“新建”  创建一个帐户。

   > [!NOTE]
   > 运行方式帐户类型必须是 Windows。 在托管 SQL Server 实例的所有 Windows Server 上，运行方式帐户还必须属于本地管理员组。
   >
   >
5. 单击“保存”  。
6. 在每个 SQL Server 实例上先修改再执行以下 T-SQL 示例，以授予运行方式帐户执行运行状况检查所需的最低权限。 但是，如果运行方式帐户已是 SQL Server 实例上 sysadmin 服务器角色的一部分，则无需执行此操作。

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>使用 Windows PowerShell 配置 SQL 运行方式帐户
打开 PowerShell 窗口，使用自己的信息更新以下脚本，并运行该脚本：

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>了解如何划分建议的优先级
每项建议都指定有一个权重值，用于标识该建议的相对重要性。 仅显示十个最重要的建议。

### <a name="how-weights-are-calculated"></a>如何计算权重
权重是基于三个关键因素的聚合值：

* 所发现的问题会导致不良后果的*概率*。 概率较高相当于建议的总体分数较高。
* 问题对组织的*影响*（如果它确实会导致不良后果）。 影响较大相当于建议的总体分数更高。
* 实施建议所需的*工作*。 工作量较大相当于建议的总体分数较低。

每一项建议的权重表示为可用于每个重点区域的总分百分比。 例如，如果安全性和合规性重点区域中建议的分数为 5%，则实施该建议将使总体安全性和合规性分数增加 5%。

### <a name="focus-areas"></a>重点区域
**安全性和合规性** - 该重点区域显示针对潜在安全威胁和违规、企业策略，以及技术、法律和法规合规性要求的建议。

**可用性和业务连续性** - 该重点区域显示针对基础结构和业务保护的服务可用性、可恢复性建议。

**性能和可扩展性** - 该重点区域显示帮助组织实现 IT 基础结构扩展的建议，确保 IT 环境满足当前性能要求，并且能够应对不断变化的基础结构需求。

**升级、迁移和部署** - 该重点区域显示帮助将 SQL Server 升级、迁移和部署到现有基础结构的建议。

**操作和监视** - 该重点关注领域显示帮助简化 IT 运营、实施预防性维护并使性能最大化的建议。

**更改和配置管理** - 该重点关注领域显示相关建议，以帮助保护日常操作，确保更改不会对基础结构造成不良影响，建立更改控制过程，并跟踪和审核系统配置。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>每个重点区域的分数都应该以 100% 为目标吗？
不一定。 建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 但是，服务器基础结构各不相同，或多或少都需要一些具体的相关建议。 例如，如果虚拟机未公开到 Internet，那么一些安全建议可能没什么用。 某些可用性建议可能不太适用于提供低优先级临时数据收集和报告的服务。 对于成熟企业至关重要的问题对于一家初创公司可能不太重要。 可能要确定哪些重点区域是要优先考虑的，并查看分数随时间的变化。

每项建议都会提供有关该建议为何重要的指导。 考虑到 IT 服务的性质和组织的业务需求，应使用本指导来评估实施建议对你是否适用。

## <a name="use-health-check-focus-area-recommendations"></a>使用运行状况检查重点区域建议
在 Azure Monitor 中使用评估解决方案之前，必须先安装该解决方案。  安装该服务后，可以使用 Azure 门户中 Azure Monitor 的“概览”页上的“SQL 运行状况检查”磁贴查看建议摘要。 

查看概述的针对基础结构的合规性评估，并深入分析建议。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>查看针对重点区域的建议并采取纠正措施
1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在 Azure 门户中，单击左下角的“更多服务”  。 在资源列表中，键入“监视器”  。 开始键入时，会根据输入筛选该列表。 选择“监视器”  。
3. 在菜单的“见解”部分，选择“更多”   。  
4. 在“概述”页上，单击“SQL 运行状况检查”磁贴。  
5. 在“运行状况检查”页上，查看某个重点区域边栏选项卡中的摘要信息，并单击其中一个查看针对该重点区域的建议。 
6. 在任何重点区域页上，均可以查看针对环境所做的优先级建议。 单击“**受影响的对象**”下的建议，以查看有关为何给出此建议的详细信息。<br><br> ![SQL 运行状况检查建议图像](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. 可以采取“建议的操作”  中建议的纠正操作。 解决该项后，以后的评估将记录已执行的建议操作，并且将提高合规性分数。 已更正的项会显示为“通过的对象”  。

## <a name="ignore-recommendations"></a>忽略建议
如果有要忽略的建议，可以创建 Azure Monitor 用来防止建议出现在评估结果中的文本文件。

### <a name="to-identify-recommendations-that-you-will-ignore"></a>确定要忽略的建议
1. 在 Azure Monitor 菜单中单击“日志”。 
2. 使用以下查询列出对于环境中计算机失败的建议。

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    下面是一个显示了日志查询的屏幕截图：<br><br> ![失败的建议](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. 选择要忽略的建议。 将 RecommendationId 的值用于接下来的过程。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>创建和使用 IgnoreRecommendations.txt 文本文件
1. 创建一个名为 IgnoreRecommendations.txt 的文件。
2. 在单独的行上粘贴或键入希望 Azure Monitor 忽略的每个建议的 RecommendationId，保存并关闭该文件。
3. 将以下文件夹中的文件置于每台要让 Azure Monitor 忽略建议的计算机上。
   * 在具有 Microsoft Monitoring Agent（直接连接或通过 Operations Manager 连接）的计算机上 - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * 在 Operations Manager 管理服务器上 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * 在 Operations Manager 2016 管理服务器上 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>验证建议是否已被忽略
1. 在下一次计划评估运行后（默认情况下每 7 天运行一次），指定的建议会被标记为“已忽略”，并且不会在评估仪表板上显示。
2. 可以使用以下日志搜索查询列出所有已忽略的建议。

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. 如果以后决定想要查看已忽略的建议，请删除任何 IgnoreRecommendations.txt 文件，或者可以从中删除 RecommendationID。

## <a name="sql-health-check-solution-faq"></a>SQL 运行状况检查解决方案常见问题解答

*SQL 评估解决方案会执行哪些检查？*

* 以下查询显示了当前执行的所有检查的说明：

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
可以将结果导出到 Excel，以便深入查看。


运行状况检查的运行频率如何？ 

* 每隔七天运行检查。

是否有某种方法可配置检查的运行频率？ 

* 现在不行。

如果添加 SQL 运行状况检查解决方案后发现另一台服务器，那么它是否会被检查？ 

* 是的，一经发现，会每隔七天对其进行一次检查。

如果服务器已停用，何时会将其从运行状况检查中删除？ 

* 如果服务器 3 周未提交数据，则会被删除。

*执行数据收集的进程的名称是什么？*

* AdvisorAssessment.exe

*收集数据需要多长时间？*

* 在服务器上执行的实际数据收集需要 1 个小时。 在具有大量 SQL 实例或数据库的服务器上，可能需要更长时间。

*收集的数据类型是什么？*

* 将收集以下类型的数据：
  * WMI
  * 注册表
  * 性能计数器
  * SQL 动态管理视图 (DMV)。

*是否有某种方法来配置收集数据的时间？*

* 现在不行。

*为何需要配置运行方式帐户？*

* 对于 SQL Server，将运行少量的 SQL 查询。 为了使它们能够运行，必须使用对 SQL 具有 VIEW SERVER STATE 权限的运行方式帐户。  此外，要查询 WMI，必须提供本地管理员凭据。

*仅显示前 10 条建议的原因*

* 我们并没有提供完整详尽的任务列表，而是建议先着重解决优先级较高的建议。 在解决这些建议后，其他建议将变为可用。 如果想要查看详细列表，可以使用 Log Analytics 日志搜索来查看所有建议。

*有没有方法来忽略建议？*

* 有的，请参阅上面的 [忽略建议](#ignore-recommendations) 部分。

## <a name="next-steps"></a>后续步骤
* 参阅[日志查询](../log-query/log-query-overview.md)，了解如何分析详细的 SQL 运行状况检查数据和建议。
