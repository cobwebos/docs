---
title: "使用 Log Analytics 中的 SQL 评估解决方案优化环境 | Microsoft Docs"
description: "可以使用 SQL 评估解决方案定期评估服务器环境的风险和运行状况。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 15858f7b7436536e6bae7fcfd6a50c722d2d04a2
ms.openlocfilehash: 0b0c739f1d89b83c43314d8ace794d26abf10054


---
# <a name="optimize-your-environment-with-the-sql-assessment-solution-in-log-analytics"></a>使用 Log Analytics 中的 SQL 评估解决方案优化环境
可以使用 SQL 评估解决方案定期评估服务器环境的风险和运行状况。 本文将帮助你安装该解决方案，以便针对潜在问题采取纠正措施。

此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。 这些建议跨六个重点领域进行了分类，将帮助你快速了解风险并采取纠正措施。

提出的建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 每一项建议都会就为何问题可能对你至关重要以及如何实施建议更改提供相关指导。

可以选择对组织最重要的重点领域，并跟踪一个运行正常无风险环境的进度。

在添加解决方案并完成评估后，会在环境中基础结构的“SQL 评估”仪表板上显示有关重点领域的摘要信息。 以下部分介绍如何使用“SQL 评估”仪表板上的信息，可以在其中查看并针对 SQL 服务器基础结构采取建议的操作。

![SQL 评估磁贴的图像](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![SQL 评估仪表板的图像](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案
SQL 评估适用于 Standard、Developer 和 Enterprise 版本的所有当前支持的 SQL Server 版本。

使用以下信息安装和配置解决方案。

* 必须在装有 SQL Server 的服务器上安装代理。
* SQL 评估解决方案要求在每台具有 OMS 代理的计算机上安装 .NET Framework 4。
* 当将 Operations Manager 代理与 SQL 评估结合使用时，将需要使用 Operations Manager 运行方式帐户。 有关详细信息，请参阅下面的 [OMS 的 Operations Manager 运行方式帐户](#operations-manager-run-as-accounts-for-oms)。

  > [!NOTE]
  > MMA 代理不支持 Operations Manager 运行方式帐户。
  >
  >
* 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将 SQL 评估解决方案添加到 OMS 工作区。 无需进一步配置。

> [!NOTE]
> 添加该解决方案后，AdvisorAssessment.exe 文件将会随代理添加到服务器中。 读取配置数据，然后将其发送到云中的 OMS 服务进行处理。 逻辑应用于接收的数据，云服务记录数据。
>
>

## <a name="sql-assessment-data-collection-details"></a>SQL 评估数据集合详细信息
SQL 评估使用已启用的代理来收集 WMI 数据、注册表数据、性能数据和 SQL Server 动态管理视图结果。

下表显示代理的数据收集方法，是否需要 Operations Manager (SCOM)，以及代理收集数据的频率。

| 平台 | 直接代理 | SCOM 代理 | Azure 存储空间 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![是](./media/log-analytics-sql-assessment/oms-bullet-green.png) |![是](./media/log-analytics-sql-assessment/oms-bullet-green.png) |![否](./media/log-analytics-sql-assessment/oms-bullet-red.png) |![否](./media/log-analytics-sql-assessment/oms-bullet-red.png) |![是](./media/log-analytics-sql-assessment/oms-bullet-green.png) |7 天 |

## <a name="operations-manager-run-as-accounts-for-oms"></a>OMS 的 Operations Manager 运行方式帐户
OMS 中的 Log Analytics 使用 Operations Manager 代理和管理组来收集数据并将数据发送到 OMS 服务。 OMS 基于工作负荷的管理包生成，以便提供增值服务。 每个工作负荷需要特定于工作负荷的权限，才能在不同的安全上下文（例如域帐户）中运行管理包。 需要通过配置 Operations Manager 运行方式帐户提供凭据信息。

使用以下信息为 SQL 评估设置 Operations Manager 运行方式帐户。

### <a name="set-the-run-as-account-for-sql-assessment"></a>为 SQL 评估设置运行方式帐户
 如果已在使用 SQL Server 管理包，应使用该运行方式帐户。

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>在操作控制台中配置 SQL 运行方式帐户
> [!NOTE]
> 如果使用的是 OMS 直接代理而不是 SCOM 代理，管理包将始终运行在本地系统帐户的安全上下文中。 跳过下面的步骤 1 到 5，并通过指定 NT AUTHORITY\SYSTEM 作为用户名，运行 T-SQL 或 Powershell 示例。
>
>

1. 在 Operations Manager 中，打开操作控制台，然后单击“管理”。
2. 在“运行方式配置”下，单击“配置文件”，并打开“OMS SQL 评估运行方式配置文件”。
3. 在“运行方式帐户”页上，单击“添加”。
4. 选择包含 SQL Server 所需凭据的 Windows 运行方式帐户，或者单击“新建”创建一个帐户。

   > [!NOTE]
   > 运行方式帐户类型必须是 Windows。 在托管 SQL Server 实例的所有 Windows Server 上，运行方式帐户还必须属于本地管理员组。
   >
   >
5. 单击“保存” 。
6. 在每个 SQL Server 实例上先修改再执行以下 T-SQL 示例，以授予运行方式帐户执行 SQL 评估所需的最低权限。 但是，如果运行方式帐户已是 SQL Server 实例上 sysadmin 服务器角色的一部分，则无需执行此操作。

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
在使用你的信息更新以下脚本后，打开 PowerShell 窗口并运行该脚本：

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>了解如何设置建议的优先级
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

**升级、迁移和部署** - 该重点区域显示帮助升级、迁移并将 SQL Server 部署到现有基础结构的建议。

**操作和监视** - 该重点关注领域显示帮助简化 IT 运营、实施预防性维护并使性能最大化的建议。

**更改和配置管理** - 该重点关注领域显示相关建议，以帮助保护日常操作，确保更改不会对基础结构造成不良影响，建立更改控制过程，并跟踪和审核系统配置。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>每个重点区域的分数都应该以 100% 为目标吗？
不一定。 建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 但是，服务器基础结构各不相同，或多或少都需要一些具体的相关建议。 例如，如果虚拟机未公开到 Internet，那么一些安全建议可能没什么用。 某些可用性建议可能不太适用于提供低优先级临时数据收集和报告的服务。 对于成熟企业至关重要的问题对于一家初创公司可能不太重要。 你可能要确定哪些重点区域是你要优先考虑的，然后查看你的分数随时间的变化。

每项建议都会提供有关该建议为何重要的指导。 考虑 IT 服务的性质和组织的业务需求，应使用本指导来评估实施建议对你是否适用。

## <a name="use-assessment-focus-area-recommendations"></a>使用评估重点区域建议
在 OMS 中使用评估解决方案之前，必须先安装该解决方案。 若要了解有关安装解决方案的详细信息，请参阅[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)。 安装完成后，可以通过使用 OMS“概述”页上的“SQL 评估”磁贴来查看建议摘要。

查看概述的针对基础结构的合规性评估，然后深入分析建议。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>查看针对重点区域的建议并采取纠正措施
1. 在“概述”页上，单击“SQL 评估”磁贴。
2. 在“SQL 评估”页上，查看某个重点区域边栏选项卡中的摘要信息，然后单击其中一个查看针对该重点区域的建议。
3. 在任何重点区域页上，均可以查看针对你的环境所做的优先级建议。 单击“受影响的对象”下的建议，以查看有关为何给出此建议的详细信息。  
    ![SQL 评估建议的图像](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. 可以采取“建议的操作”中建议的纠正操作。 解决该项后，以后的评估将记录已执行的建议操作，并且将提高合规性分数。 已更正的项将显示为“通过的对象”。

## <a name="ignore-recommendations"></a>忽略建议
如果你有要忽略的建议，可以创建 OMS 用来防止建议出现在评估结果中的文本文件。

### <a name="to-identify-recommendations-that-you-will-ignore"></a>确定要忽略的建议
1. 登录到工作区，并打开“日志搜索”。 使用以下查询列出对于你环境中计算机失败的建议。

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   下面是显示日志搜索查询的屏幕截图：![失败的建议](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)
2. 选择要忽略的建议。 将 RecommendationId 的值用于接下来的过程。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>创建和使用 IgnoreRecommendations.txt 文本文件
1. 创建一个名为 IgnoreRecommendations.txt 的文件。
2. 在单独的行上粘贴或键入希望 OMS 忽略的每个建议的 RecommendationId，然后保存并关闭该文件。
3. 将以下文件夹中的文件置于每台要 OMS 忽略建议的计算机上。
   * 在具有 Microsoft Monitoring Agent（直接连接或通过 Operations Manager 连接）的计算机上 - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * 在 Operations Manager 管理服务器上 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>验证建议是否已被忽略
1. 在下一次计划评估运行后（默认情况下每 7 天），指定的建议会被标记为“已忽略”，并且不会在评估仪表板上显示。
2. 可以使用以下日志搜索查询列出所有已忽略的建议。

   ```
   Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```
3. 如果以后决定想要查看已忽略的建议，请删除任何 IgnoreRecommendations.txt 文件，或者可以从中删除 RecommendationID。

## <a name="sql-assessment-solution-faq"></a>SQL 评估解决方案常见问题
*评估的运行频率如何？*

* 评估每 7 天运行一次。

*是否有某种方法来配置评估的运行频率？*

* 现在不行。

*如果添加 SQL 评估解决方案后发现另一台服务器，那么是否会评估它？*

* 是的，一经发现，会每 7 天对其进行一次评估。

*如果服务器已停用，何时会将其从评估中删除？*

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

* 对于 SQL Server，将运行少量的 SQL 查询。 为了使它们能够运行，必须使用对 SQL 具有 VIEW SERVER STATE 权限的运行方式帐户。  此外，为了查询 WMI，本地管理员凭据是必需项。

*为何仅显示前 10 条建议？*

* 我们并没有为你提供详尽的任务普遍列表，而是建议你先着重解决按优先级排列的建议。 在解决这些建议后，其他建议将变为可用。 如果你想要查看详细列表，可以使用 OMS 日志搜索来查看所有建议。

*有没有方法来忽略建议？*

* 有的，请参阅上面的“忽略建议”[](#ignore-recommendations)部分。

## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看详细的 SQL 评估数据和建议。



<!--HONumber=Nov16_HO3-->


