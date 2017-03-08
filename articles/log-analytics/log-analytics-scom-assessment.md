---
title: "使用 Azure Log Analytics 优化 System Center Operations Manager 环境 | Microsoft 文档"
description: "可以使用 System Center Operations Manager 评估解决方案定期评估服务器环境的风险和运行状况。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: f812ff8fb2b32f89e24d640e0eae8f2da9858a18
ms.lasthandoff: 02/28/2017


---

# <a name="optimize-your-environment-with-the-system-center-operations-manager-assessment-preview-solution"></a>使用 System Center Operations Manager 评估（预览版）解决方案优化环境

可以使用 System Center Operations Manager 评估解决方案定期评估 System Center Operations Manager 服务器环境的风险和运行状况。 本文将帮助你安装、配置和使用该解决方案，以便针对潜在问题采取纠正措施。

此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。 这些建议跨四个重点领域进行了分类，将帮助你快速了解风险并采取纠正措施。

提出的建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 每一项建议都会就为何问题可能对你至关重要以及如何实施建议更改提供相关指导。

可以选择对组织最重要的重点领域，并跟踪一个运行正常无风险环境的进度。

在添加解决方案并完成评估后，将在基础结构的“System Center Operations Manager 评估”仪表板上显示有关重点领域的摘要信息。 以下部分介绍如何使用“System Center Operations Manager 评估”仪表板上的信息，可以在其中查看并针对 SCOM 基础结构采取建议的操作。

![System Center Operations Manager 解决方案磁贴](./media/log-analytics-scom-assessment/scom-tile.png)

![“System Center Operations Manager 评估”仪表板](./media/log-analytics-scom-assessment/scom-dashboard01.png)

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案

该解决方案适用于 Microsoft System Operations Manager 2012 R2 和 2012 SP1。

使用以下信息安装和配置解决方案。

 - 在 OMS 中使用评估解决方案之前，必须先安装该解决方案。 若要了解有关安装解决方案的详细信息，请参阅[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)。

 - 将解决方案添加到工作区以后，仪表板上的“System Center Operations Manager 评估”磁贴会显示“需要更多的配置”这样一条消息。 单击该磁贴，然后按照页面中所述的配置步骤操作

 ![System Center Operations Manager 仪表板磁贴](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

 可以通过脚本配置 System Center Operations Manager，只需执行 OMS 中解决方案的配置页提及的步骤即可。

 而若要通过 SCOM 控制台配置评估，则需按相同顺序执行以下步骤
1. [设置 System Center Operations Manager 评估的运行方式帐户](#operations-manager-run-as-accounts-for-oms)  
2. [配置 System Center Operations Manager 评估规则](#configure-the-assessment-rule)

# <a name="system-center-operations-manager-assessment-data-collection-details"></a>System Center Operations Manager 评估数据收集详细信息

System Center Operations Manager 评估使用启用的服务器，通过 Windows PowerShell、SQL 查询和文件信息收集器收集 WMI 数据、注册表数据、EventLog 数据和 Operations Manager 数据。

下表显示了 System Center Operations Manager 评估的数据收集方法，以及代理收集数据的频率。

| 平台 | 直接代理 | SCOM 代理 | Azure 存储空间 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  ![否](./media/log-analytics-scom-assessment/oms-bullet-red.png) | ![否](./media/log-analytics-scom-assessment/oms-bullet-red.png)  | ![否](./media/log-analytics-scom-assessment/oms-bullet-red.png)  |  ![是](./media/log-analytics-scom-assessment/oms-bullet-green.png) | ![否](./media/log-analytics-scom-assessment/oms-bullet-red.png)  | 七天 |

## <a name="operations-manager-run-as-accounts-for-oms"></a>OMS 的 Operations Manager 运行方式帐户

OMS 基于工作负荷的管理包生成，提供增值服务。 每个工作负荷需要特定于工作负荷的权限，才能在不同的安全上下文（例如域帐户）中运行管理包。 配置 Operations Manager 运行方式帐户以提供凭据信息。

使用以下信息为 System Center Operations Manager 评估设置 Operations Manager 运行方式帐户。

### <a name="set-the-run-as-account"></a>设置运行方式帐户

1. 在 Operations Manager 控制台中，转到“管理”选项卡。
2. 在“运行方式配置”下面，单击“帐户”。
3. 创建运行方式帐户：根据向导创建一个 Windows 帐户。 要使用的帐户应是一个已识别的且满足以下所有先决条件的帐户：

    >[!NOTE]
    运行方式帐户必须满足以下要求：
    - 在环境中的所有服务器的本地管理员组中都有一个域帐户成员（所有 Operations Manager 角色 - 管理服务器、OpsMgr 数据库、数据仓库、报表、Web 控制台、网关）
    - 正在评估管理组的 Operation Manager 管理员角色
    - 执行[脚本](#sql-script-to-grant-granular-permissions-to-the-run-as-account)，向 Operations Manager 使用的 SQL 实例上的帐户授予具体权限。
      注意：如果此帐户已经有 sysadmin 权限，则跳过脚本执行操作。

4. 在“分发安全性”下面，选择“更安全”。
5. 指定要将帐户分发到的管理服务器。
3. 返回“运行方式配置”并单击“配置文件”。
4. 搜索“SCOM 评估配置文件”。
5. 配置文件名应为：“Microsoft System Center Advisor SCOM 评估运行方式配置文件”。
6. 右键单击该配置文件并更新其属性，然后添加最近在步骤 3 中创建的运行方式帐户。

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>向运行方式帐户授予具体权限的 SQL 脚本

执行以下 SQL 脚本，向 Operations Manager 使用的 SQL 实例上的运行方式帐户授予所需权限。

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```


### <a name="configure-the-assessment-rule"></a>配置评估规则

System Center Operations Manager 评估解决方案的管理包中包含一个名为“Microsoft System Center Advisor SCOM 评估运行评估规则”的规则。 此规则负责运行评估。 若要启用该规则并配置频率，请使用以下过程。

Microsoft System Center Advisor SCOM 评估运行评估规则默认已禁用。 若要运行评估，必须在管理服务器上启用该规则。 使用以下步骤。

#### <a name="enable-the-rule-for-a-specific-management-server"></a>为特定的管理服务器启用规则

1. 在 Operations Manager 控制台的“创作”工作区的“规则”窗格中，搜索规则“Microsoft System Center Advisor SCOM 评估运行评估规则”。
2. 在搜索结果中，选择包含“类型: 管理服务器”字样的规则。
3. 右键单击该规则，然后单击“重写” > “对于类为管理服务器的特定对象”。
4.    在可用管理服务器列表中，选择要在其上运行该规则的管理服务器。
5.    请务必将“已启用”参数值的重写值更改为 **True**。  
    ![重写参数](./media/log-analytics-scom-assessment/rule.png)

继续在此窗口中操作，使用下一个过程配置运行频率。

#### <a name="configure-the-run-frequency"></a>配置运行频率

评估已配置为每隔 10,080 分钟（即七天）运行一次，这是默认间隔。 最小可以将该值重写为 1440 分钟（即一天）。 该值表示连续运行评估的最小间隔时间。 若要重写该间隔，请使用以下步骤。

1. 在 Operations Manager 控制台的“创作”工作区的“规则”窗格中，搜索规则“Microsoft System Center Advisor SCOM 评估运行评估规则”。
2. 在搜索结果中，选择包含“类型: 管理服务器”字样的规则。
3. 右键单击该规则，然后单击“重写规则” > “对于类为管理服务器的所有对象”。
4. 将“间隔”参数值更改为所需的间隔值。 在以下示例中，该值设置为 1440 分钟（一天）。  
    ![间隔参数](./media/log-analytics-scom-assessment/interval.png)  
    如果设置的值小于 1440 分钟，该规则将按一天的间隔运行。 在本示例中，规则将忽略间隔值，按一天的频率运行。


## <a name="understanding-how-recommendations-are-prioritized"></a>了解如何设置建议的优先级

每项建议都指定有一个权重值，用于标识该建议的相对重要性。 仅显示 10 个最重要的建议。

### <a name="how-weights-are-calculated"></a>如何计算权重

权重是基于三个关键因素的聚合值：

- 所发现的问题会导致不良后果的*概率*。 概率较高相当于建议的总体分数较高。
- 问题对组织的*影响*（如果它确实会导致不良后果）。 影响较大相当于建议的总体分数更高。
- 实施建议所需的*工作*。 工作量较大相当于建议的总体分数较低。

每一项建议的权重表示为可用于每个重点区域的总分百分比。 例如，如果“可用性和业务连续性”重点区域中的建议评分为 5%，则实施该建议可将总体可用性和业务连续性的评分提高 5%。

### <a name="focus-areas"></a>重点区域

**可用性和业务连续性** - 该重点区域显示针对基础结构和业务保护的服务可用性、可恢复性建议。

**性能和可扩展性** - 该重点区域显示帮助组织实现 IT 基础结构扩展的建议，确保 IT 环境满足当前性能要求，并且能够应对不断变化的基础结构需求。

**升级、迁移和部署** - 该重点区域显示帮助升级、迁移并将 SQL Server 部署到现有基础结构的建议。

**操作和监视** - 该重点关注领域显示帮助简化 IT 运营、实施预防性维护并使性能最大化的建议。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>每个重点区域的分数都应该以 100% 为目标吗？

不一定。 建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 但是，服务器基础结构各不相同，或多或少都需要一些具体的相关建议。 例如，如果虚拟机未公开到 Internet，那么一些安全建议可能没什么用。 某些可用性建议可能不太适用于提供低优先级临时数据收集和报告的服务。 对于成熟企业至关重要的问题对于一家初创公司可能不太重要。 你可能要确定哪些重点区域是你要优先考虑的，然后查看你的分数随时间的变化。

每项建议都会提供有关该建议为何重要的指导。 考虑 IT 服务的性质和组织的业务需求，请使用本指导来评估实施建议对你是否适用。

## <a name="use-assessment-focus-area-recommendations"></a>使用评估重点区域建议

在 OMS 中使用评估解决方案之前，必须先安装该解决方案。 若要了解有关安装解决方案的详细信息，请参阅[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)。 安装完成后，可以通过使用 OMS“概述”页上的“System Center Operations Manager 评估”磁贴来查看建议摘要。

查看概述的针对基础结构的合规性评估，然后深入分析建议。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>查看针对重点区域的建议并采取纠正措施

1. 在“概述”页上，单击“System Center Operations Manager 评估”磁贴。
2. 在“System Center Operations Manager 评估”页上，查看某个重点区域边栏选项卡中的摘要信息，然后单击其中一个查看针对该重点区域的建议。
3. 在任何重点区域页上，均可以查看针对你的环境所做的优先级建议。 单击“受影响的对象”下的建议，以查看有关为何给出此建议的详细信息。  
    ![重点区域](./media/log-analytics-scom-assessment/focus-area.png)
4. 可以采取“建议的操作”中建议的纠正操作。 解决该项后，以后的评估将记录已执行的建议操作，并且将提高合规性分数。 已更正的项将显示为“通过的对象”。

## <a name="ignore-recommendations"></a>忽略建议

如果有要忽略的建议，可以创建 OMS 用来防止建议出现在评估结果中的文本文件。

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>确定要忽略的建议

1. 登录到工作区，并打开“日志搜索”。 使用以下查询列出对于你环境中计算机失败的建议。

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    下面是显示日志搜索查询的屏幕截图：  
    ![日志搜索](./media/log-analytics-scom-assessment/scom-log-search.png)

2. 选择要忽略的建议。 将 RecommendationId 的值用于接下来的过程。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>创建和使用 IgnoreRecommendations.txt 文本文件

1. 创建一个名为 IgnoreRecommendations.txt 的文件。
2. 在单独的行上粘贴或键入希望 OMS 忽略的每个建议的 RecommendationId，然后保存并关闭该文件。
3. 将以下文件夹中的文件置于每台要 OMS 忽略建议的计算机上。
4. 在 Operations Manager 管理服务器上 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server。

### <a name="to-verify-that-recommendations-are-ignored"></a>验证建议是否已被忽略

1. 在下一次计划评估运行后（默认情况下每隔七天运行一次），指定的建议会被标记为“已忽略”，不会在评估仪表板上显示。
2. 可以使用以下日志搜索查询列出所有已忽略的建议。

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

3. 如果以后决定想要查看已忽略的建议，请删除任何 IgnoreRecommendations.txt 文件，或者可以从中删除 RecommendationID。

## <a name="system-center-operations-manager-assessment-solution-faq"></a>System Center Operations Manager 评估解决方案常见问题

*我将评估解决方案添加到了我的 OMS 工作区。但没有看到建议。为什么看不到呢？* 添加解决方案后，使用以下步骤在 OMS 仪表板上查看建议。  

- [设置 System Center Operations Manager 评估的运行方式帐户](#operations-manager-run-as-accounts-for-oms)  
- [配置 System Center Operations Manager 评估规则](#configure-the-assessment-rule)


*是否有某种方法来配置评估的运行频率？* 是的。 请参阅[配置运行频率](#configure-the-run-frequency)。

*如果添加 System Center Operations Manager 评估解决方案后发现另一台服务器，那么是否会评估它？* 是的，发现之后，即会对它进行评估 - 默认情况下每隔七天评估一次。

*执行数据收集的进程的名称是什么？* AdvisorAssessment.exe

*AdvisorAssessment.exe 进程在哪个位置运行？* AdvisorAssessment.exe 在启用评估规则的管理服务器的 HealthService 下运行。 使用该进程可以通过远程数据收集来发现整个环境。

*收集数据需要多长时间？* 在服务器上收集数据大约需要一小时。 在包含许多 Operations Manager 实例或数据库的环境中，时间可能更长。

*如果将评估间隔设置为小于 1440 分钟会发生什么情况？* 评估预先配置的最高运行频率为每天一次。 如果将间隔值重写为小于 1440 分钟的值，评估将使用 1440 分钟作为间隔值。

*如何知道是否存在不符合先决条件的情况？* 如果运行了评估但未看到结果，则有可能是评估不符合某些先决条件。 可以在日志搜索中执行查询 `Type=Operation Solution=SCOMAssessment` 和 `Type=SCOMAssessmentRecommendation FocusArea=Prerequisites`，确定不符合哪些先决条件。

*先决条件错误中包含一条 `Failed to connect to the SQL Instance (….).` 消息。问题出在哪里？* 收集数据的进程 AdvisorAssessment.exe 在管理服务器的 HealthService 下运行。 在评估过程中，该进程会尝试连接到 Operations Manager 数据库所在的 SQL Server。 如果防火墙规则阻止与 SQL Server 实例建立连接，则可能会出现此错误。

*收集的数据类型是什么？* 通过 Windows PowerShell、SQL 查询和文件信息收集器收集以下类型的数据：- WMI 数据 - 注册表数据 - EventLog 数据 - Operations Manager 数据。

*为何需要配置运行方式帐户？* 该解决方案将对 Operations Manager 服务器运行各种 SQL 查询。 为了运行这些查询，必须使用一个具有所需权限的运行方式帐户。 此外，查询 WMI 需要用到本地管理员凭据。

*为何仅显示前 10 条建议？* 我们不会提供名目繁多的详尽任务列表，只是建议先按优先级着重实施建议的方法。 在解决这些建议后，其他建议将变为可用。 如果想要查看详细的列表，可以使用日志搜索查看所有建议。

*有没有方法来忽略建议？* 是的，请参阅[忽略建议](#Ignore-recommendations)。


## <a name="next-steps"></a>后续步骤

- 使用[搜索日志](log-analytics-log-searches.md)查看详细的 System Center Operations Manager 评估数据和建议。

