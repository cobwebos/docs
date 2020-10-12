---
title: 连续导出可以将 Azure 安全中心的警报和建议发送到 Log Analytics 工作区或 Azure 事件中心
description: 了解如何配置安全警报和建议的连续导出 Log Analytics 工作区或 Azure 事件中心
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 8b27c3d0982e945fcabc6e7748646ea2ee1a4184
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945257"
---
# <a name="continuously-export-security-alerts-and-recommendations"></a>持续导出安全警报和建议

Azure 安全中心会生成详细的安全警报和建议。 可以通过门户或编程工具查看它们。 你可能还需要导出部分或全部信息，以便与环境中的其他监视工具进行跟踪。 

**连续导出** 使你可以完全自定义将导出的 *内容* 以及它将导出 *的位置* 。 例如，你可以对其进行配置，以便：

- 所有高严重性警报都发送到 Azure 事件中心
- 从 SQL server 的漏洞评估扫描中发现的所有介质或更高的严重性都发送到特定 Log Analytics 工作区
- 每当生成事件中心或 Log Analytics 工作区时，将会向其传递特定建议 

本文介绍如何配置 Log Analytics 工作区或 Azure 事件中心的连续导出。

> [!NOTE]
> 如果需要将安全中心与 SIEM 集成，请查看将 [警报发送到 SIEM](export-to-siem.md) 以获取选项。

> [!TIP]
> 安全中心还提供了用于执行一次性的手动导出到 CSV 的选项。 有关详细信息 [，请参阅及时导出警报和建议](#manual-one-time-export-of-alerts-and-recommendations)。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|免费|
|所需角色和权限：|<ul><li>资源组上的**安全管理员**或**所有者**</li><li>目标资源的写入权限</li><li>如果你使用下面所述的 Azure 策略 "DeployIfNotExist" 策略，你还需要分配策略的权限</li></ul>|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![是](./media/icons/yes-icon.png) 中国 Gov (到事件中心) ，其他 Gov|
|||





## <a name="set-up-a-continuous-export"></a>设置连续导出 

可以通过安全中心 REST API，或者使用提供的 Azure 策略模板，在 Azure 门户中的安全中心页面配置连续导出。 选择下面相应的选项卡，以获取每个选项的详细信息。

### <a name="use-the-azure-portal"></a>[**使用 Azure 门户**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-security-center-pages-in-azure-portal"></a>在 Azure 门户中配置安全中心页面的连续导出

无论是设置连续导出到 Log Analytics 工作区的操作还是连续导出到 Azure 事件中心的操作，都需要执行以下步骤。

1. 从安全中心的侧栏中，选择“定价和设置”。
1. 选择要为其配置数据导出的特定订阅。
1. 从该订阅的设置页的侧栏中，选择“连续导出”。
    [![Azure 安全中心的导出选项](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) 在此处可以看到导出选项。 每个可用的导出目标有一个选项卡。 
1. 选择要导出的数据类型，并从每种类型的筛选器中进行选择（例如，仅导出严重程度高的警报）。
1. （可选）如果你的选择包含以下四个建议中的一个，你可以将漏洞评估结果与它们包括在一起：
    - 应修正关于 SQL 数据库的漏洞评估结果
    - 应修正关于计算机上的 SQL 服务器的漏洞评估结果（预览版）
    - 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供技术支持）
    - 应修正虚拟机中的漏洞

    若要将结果与这些建议包括在一起，请启用“包括安全结果”选项。

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在连续导出配置中包括安全结果开关" :::

1. 从“导出目标”区域中，选择要将数据保存到其中的位置。 数据可以保存在不同订阅的目标中（例如，保存在中央事件中心实例或中央 Log Analytics 工作区中）。
1. 选择“保存”。

### <a name="use-the-rest-api"></a>[**使用 REST API**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>使用 REST API 配置连续导出

可以通过 Azure 安全中心 [自动化 API](https://docs.microsoft.com/rest/api/securitycenter/automations)来配置和管理连续导出。 使用此 API 可创建或更新规则，以便导出到以下任何可能的目标：

- Azure 事件中心
- Log Analytics 工作区
- Azure 逻辑应用 

API 提供了 Azure 门户中没有的其他功能，例如：

* **更大的卷** - API 允许你在单个订阅上创建多个导出配置。 安全中心门户 UI 中的“连续导出”页仅支持每个订阅一个导出配置。

* **其他功能** - API 提供了 UI 中未显示的其他参数。 例如，你可以将标记添加到自动化资源，并根据一组更广泛的警报和建议属性（与安全中心的门户 UI 的“连续导出”页中提供的属性相比）来定义导出。

* **更明确的作用域** - API 为导出配置的作用域提供更精细的级别。 使用 API 定义导出时，可以在资源组级别执行此操作。 如果使用安全中心门户 UI 中的“连续导出”页面，则必须在订阅级别定义它。

    > [!TIP]
    > 如果已使用 API 设置了多个导出配置，或者如果使用了仅限 API 的参数，则这些额外的功能不会在安全中心 UI 中显示。 而是会出现一个横幅，通知你存在其他配置。

在 [REST API 文档](https://docs.microsoft.com/rest/api/securitycenter/automations)中详细了解自动化 API。





### <a name="deploy-at-scale-with-azure-policy"></a>[**通过 Azure 策略大规模部署**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>使用提供的策略配置按比例连续导出

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

若要在组织中部署连续导出配置，请使用下面所述的 Azure 策略 "DeployIfNotExist" 策略来创建和配置连续导出过程。

**若要实施这些策略**

1. 从下表中选择要应用的策略：

    |目标  |策略  |策略 ID  |
    |---------|---------|---------|
    |将内容连续导出到事件中心|[为 Azure 安全中心警报和建议部署“导出到事件中心”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |将内容连续导出到 Log Analytics 工作区|[为 Azure 安全中心警报和建议配置“导出到 Log Analytics 工作区”配置](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > 还可以通过搜索 Azure 策略来查找这些内容：
    > 1. 打开 Azure 策略。
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="在连续导出配置中包括安全结果开关" **分配**"。
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="在连续导出配置中包括安全结果开关" 选项卡 (1) 提供对类似配置选项的访问，如安全中心的连续导出页面 (2) 。
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="在连续导出配置中包括安全结果开关" lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. （可选）若要将此分配应用于现有订阅，请打开 " **修正** " 选项卡，然后选择用于创建修正任务的选项。
1. 查看 "摘要" 页，然后选择 " **创建**"。

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>有关导出到 Log Analytics 工作区的信息

如果要在 Log Analytics 工作区内分析 Azure 安全中心数据或将 Azure 警报与安全中心警报一起使用，请设置 Log Analytics 工作区的连续导出。

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics 表和架构

安全警报和建议分别存储在 *SecurityAlert* 和 *SecurityRecommendations* 表中。 

包含这些表的 Log Analytics 解决方案的名称取决于是否已启用 Azure Defender：安全 ( "安全和审核" ) 或 SecurityCenterFree。 

> [!TIP]
> 若要查看目标工作区中的数据，必须启用其中一个解决方案 **安全和审核** 或 **SecurityCenterFree**。

![Log Analytics 中的 * SecurityAlert * 表](./media/continuous-export/log-analytics-securityalert-solution.png)

若要查看导出的数据类型的事件架构，请访问 [Log Analytics 表架构](https://aka.ms/ASCAutomationSchemas)。


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>在 Azure Monitor 中查看导出的警报和建议

在某些情况下，你可以选择在 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)中查看导出的安全警报和/或建议。 

Azure Monitor 提供了一种统一的警报体验，包括诊断日志、指标警报以及基于 Log Analytics 工作区查询的自定义警报。

若要在 Azure Monitor 中查看安全中心的警报和建议，请根据 Log Analytics 查询 (日志警报) 来配置警报规则：

1. 在 Azure Monitor 的 " **警报** " 页中，选择 " **新建警报规则**"。

    ![Azure Monitor 的 "警报" 页](./media/continuous-export/azure-monitor-alerts.png)

1. 在 "创建规则" 页中，配置新的规则 (采用 Azure Monitor) 中配置 [日志警报规则](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) 的相同方式：

    * 对于 " **资源**"，请选择要向其中导出安全警报和建议的 "Log Analytics" 工作区。

    * 对于 " **条件**"，选择 " **自定义日志搜索**"。 在出现的页中，配置查询、lookback 周期和频率时间段。 在搜索查询中，你可以键入 *SecurityAlert* 或 *SecurityRecommendation* ，以在你启用连续导出到 Log Analytics 功能时查询安全中心持续导出到的数据类型。 
    
    * 还可以配置要触发的 [操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 。 操作组可以触发电子邮件发送、ITSM 票证、Webhook 等。
    ![Azure Monitor 预警规则](./media/continuous-export/azure-monitor-alert-rule.png)

现在，你将看到新的 Azure 安全中心警报或建议 (，具体取决于你配置的连续导出规则和你在 Azure Monitor 警报规则中定义的条件) Azure Monitor 警报中，如果提供了 (，则会自动触发操作组) 。

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>手动导出警报和建议

若要下载警报或建议的 CSV 报表，请打开“安全警报”或“建议”页，然后选择“下载 CSV 报表”按钮。

[![将警报数据下载为 CSV 文件](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 这些报表包含当前所选订阅中的资源的警报和建议。


## <a name="faq---continuous-export"></a>常见问题解答 - 连续导出

### <a name="what-are-the-costs-involved-in-exporting-data"></a>导出数据时涉及哪些费用？

启用连续导出不会产生费用。 在 Log Analytics 工作区中引入和保留数据可能会产生费用，具体取决于你的配置。 

详细了解 [Log Analytics 工作区定价](https://azure.microsoft.com/pricing/details/monitor/)。

详细了解 [Azure 事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。




## <a name="next-steps"></a>后续步骤

本文介绍了如何配置建议和警报的连续导出。 另外还介绍了如何将警报数据下载为 CSV 文件。 

如需相关资料，请参阅以下文档： 

- 了解有关 [工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)的详细信息。
- [Azure 事件中心文档](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel 文档](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor 文档](https://docs.microsoft.com/azure/azure-monitor/)
- [导出数据类型架构](https://aka.ms/ASCAutomationSchemas)
