---
title: 排查常见错误
description: 了解如何排查为 Kubernetes 创建策略定义、各种 SDK 和加载项时遇到的问题。
ms.date: 10/05/2020
ms.topic: troubleshooting
ms.openlocfilehash: 98b5f1658a7d3fc7c4a7db7145b92bb6065befc5
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999891"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>排查使用 Azure Policy 时出现的错误

创建策略定义、使用 SDK 或设置 [Azure policy For Kubernetes](../concepts/policy-for-kubernetes.md) 外接程序时，可能会遇到错误。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

错误详细信息的位置取决于导致错误的操作。

- 使用自定义策略时，请尝试在 Azure 门户中获取有关架构的 Lint 分析反馈，或查看生成的[符合性数据](../how-to/get-compliance-data.md)以了解资源的评估方式。
- 使用各种 SDK 时，SDK 提供有关函数失败原因的详细信息。
- 使用 Kubernetes 的外接程序时，请从群集中的 [日志记录](../concepts/policy-for-kubernetes.md#logging) 开始。

## <a name="general-errors"></a>常规错误

### <a name="scenario-alias-not-found"></a>方案：找不到别名

#### <a name="issue"></a>问题

Azure Policy 使用[别名](../concepts/definition-structure.md#aliases)映射到 Azure 资源管理器属性。

#### <a name="cause"></a>原因

策略定义中使用的别名不正确或不存在。

#### <a name="resolution"></a>解决方法

首先，验证资源管理器属性是否有别名。 使用 [Visual Studio Code 的 Azure Policy 扩展](../how-to/extension-for-vscode.md)、[Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values) 或 SDK 来查找可用别名。 如果资源管理器属性没有别名，请创建支持票证。

### <a name="scenario-evaluation-details-not-up-to-date"></a>方案：评估详细信息不是最新的

#### <a name="issue"></a>问题

资源处于“未启动”状态或符合性详细信息不是最新的。

#### <a name="cause"></a>原因

应用新策略或计划分配大约需要 30 分钟。 现有分配范围内的新资源或更新的资源大约只需 15 分钟即可使用。 标准符合性扫描每 24 小时进行一次。 有关详细信息，请参阅[评估触发器](../how-to/get-compliance-data.md#evaluation-triggers)。

#### <a name="resolution"></a>解决方法

首先，请等待一段时间来完成评估以及等待 Azure 门户或 SDK 中显示符合性结果。 若要使用 Azure PowerShell 或 REST API 开始新的评估扫描，请参阅[按需评估扫描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。

### <a name="scenario-compliance-not-as-expected"></a>方案：符合性与预期不符

#### <a name="issue"></a>问题

资源未处于预期有效的评估状态（符合或不符合） 。

#### <a name="cause"></a>原因

资源不在正确的策略分配范围内，或者策略定义未按预期执行。

#### <a name="resolution"></a>解决方法

请按照以下步骤来解决策略定义问题：

1. 首先，请等待一段时间来完成评估以及等待 Azure 门户或 SDK 中显示符合性结果。 若要使用 Azure PowerShell 或 REST API 开始新的评估扫描，请参阅[按需评估扫描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 检查分配参数和分配的作用域是否已正确设置。
1. 检查[策略定义模式](../concepts/definition-structure.md#mode)：
   - 所有资源类型的模式 "all"。
   - 如果策略定义检查标记或位置，则为 "已索引" 模式。
1. 检查资源的作用域是否已 [排除](../concepts/assignment-structure.md#excluded-scopes) 或不 [例外](../concepts/exemption-structure.md)。
1. 如果策略分配的符合性显示 `0/0` 资源，表示没有确定在分配范围内适用的资源。 检查策略定义和分配范围。
1. 对于预期符合的不合规资源，请检查 [确定不符合的原因](../how-to/determine-non-compliance.md)。 通过将定义与计算的属性值进行比较，可了解资源不符合的原因。
   - 如果 **目标值** 错误，请修改策略定义。
   - 如果 **当前值** 错误，请通过验证资源负载 `resources.azure.com` 。
1. 检查故障排除：针对其他常见问题和解决方案的 [强制性不按预期执行](#scenario-enforcement-not-as-expected) 。

如果复制和自定义的内置策略定义或自定义定义仍存在问题，请在 **创作策略** 时创建支持票证，以便正确路由问题。

### <a name="scenario-enforcement-not-as-expected"></a>方案：未按预期执行

#### <a name="issue"></a>问题

预期由 Azure Policy 处理的资源未被处理，且 [Azure 活动日志](../../../azure-monitor/platform/platform-logs-overview.md)中没有条目。

#### <a name="cause"></a>原因

已为 [enforcementMode](../concepts/assignment-structure.md#enforcement-mode)“禁用”配置了策略分配。 当强制模式处于禁用状态时，不会强制实施策略效果，并且活动日志中没有条目。

#### <a name="resolution"></a>解决方法

请按照以下步骤来解决策略分配的强制问题：

1. 首先，请等待一段时间来完成评估以及等待 Azure 门户或 SDK 中显示符合性结果。 若要使用 Azure PowerShell 或 REST API 开始新的评估扫描，请参阅[按需评估扫描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。
1. 请检查是否正确设置了分配参数和分配范围，以及是否_启用_了**enforcementMode** 。 
1. 检查[策略定义模式](../concepts/definition-structure.md#mode)：
   - 所有资源类型的模式 "all"。
   - 如果策略定义检查标记或位置，则为 "已索引" 模式。
1. 检查资源的作用域是否已 [排除](../concepts/assignment-structure.md#excluded-scopes) 或不 [例外](../concepts/exemption-structure.md)。
1. 验证资源有效负载是否与策略逻辑匹配。 这可以通过 [捕获 HAR 跟踪](../../../azure-portal/capture-browser-trace.md) 或查看 ARM 模板属性来完成。
1. 检查 [故障排除：满足](#scenario-compliance-not-as-expected) 其他常见问题和解决方案的符合性要求。

如果复制和自定义的内置策略定义或自定义定义仍存在问题，请在 **创作策略** 时创建支持票证，以便正确路由问题。

### <a name="scenario-denied-by-azure-policy"></a>方案：被 Azure Policy 拒绝

#### <a name="issue"></a>问题

拒绝创建或更新资源。

#### <a name="cause"></a>原因

向新资源或更新的资源所在的范围执行的策略分配符合设有[拒绝](../concepts/effects.md#deny)效果的策略定义的条件。 符合这些定义的资源将无法创建或更新。

#### <a name="resolution"></a>解决方法

拒绝策略分配中的错误消息包括策略定义和策略分配 ID。 如果消息中的错误信息丢失，还可在[活动日志](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)中找到。 使用此信息可获取更多详细信息，以了解资源限制和调整请求中的资源属性以使其匹配允许的值。

## <a name="template-errors"></a>模板错误

### <a name="scenario-policy-supported-functions-processed-by-template"></a>方案：策略支持的、由模板处理的函数

#### <a name="issue"></a>问题

Azure Policy 支持大量 Azure 资源管理器模板（ARM 模板）函数以及仅在策略定义中可用的函数。 资源管理器将这些函数作为部署的一部分而不是作为策略定义的一部分进行处理。

#### <a name="cause"></a>原因

如果使用受支持的函数（如 `parameter()` 或 `resourceGroup()`），可在部署时生成函数的处理结果，而不是将函数留给策略定义和 Azure Policy 引擎来处理。

#### <a name="resolution"></a>解决方法

若要传递函数使其成为策略定义的一部分，请使用 `[` 转义整个字符串，以便使属性看起来像是 `[[resourceGroup().tags.myTag]`。 转义字符会导致资源管理器在处理模板时将值视为字符串。 然后，Azure Policy 将函数放置在策略定义中，使其能够按预期的动态方式执行。 有关详细信息，请参阅 [Azure 资源管理器模板中的语法和表达式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="add-on-installation-errors"></a>加载项安装错误

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>方案：使用了 Helm Chart 的安装过程在密码处失败

#### <a name="issue"></a>问题

`helm install azure-policy-addon` 命令失败，并显示下列其中一个消息：

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

生成的密码包含 Helm Chart 要用于拆分的逗号 (`,`)。

#### <a name="resolution"></a>解决方法

使用反斜杠 (`\`) 运行 `helm install azure-policy-addon` 时，转义密码值中的逗号 (`,`)。

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>方案：使用 Helm 的安装失败，因为名称已经存在

#### <a name="issue"></a>问题

`helm install azure-policy-addon`命令失败，并出现以下消息：

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

已安装或部分安装了名称的 Helm 图表 `azure-policy-addon` 。

#### <a name="resolution"></a>解决方法

按照说明 [删除 Azure Policy For Kubernetes 外接程序](../concepts/policy-for-kubernetes.md#remove-the-add-on)，然后重新运行该 `helm install azure-policy-addon` 命令。

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>方案：使用系统分配的托管标识替换 Azure 虚拟机用户分配的标识

#### <a name="issue"></a>问题

将来宾配置策略计划分配给计算机内部的设置审核后，分配给该计算机的用户分配的托管标识将不再被分配。 仅分配系统分配的托管标识。

#### <a name="cause"></a>原因

以前在来宾配置 DeployIfNotExists 定义中使用的策略定义确保将系统分配的标识分配给计算机，但也会删除用户分配的标识分配。

#### <a name="resolution"></a>解决方法

先前导致此问题的定义将显示为 \[ 弃用 \] ，并由管理先决条件的策略定义替换，而不删除用户分配的托管标识。 需要手动操作。 删除标记为 "已弃用" 的任何现有策略分配 \[ \] ，并将其替换为与原始名称相同的已更新先决条件策略计划和策略定义。

有关详细叙述，请参阅以下博客文章：

[为来宾配置审核策略发布的重要更改](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过 [Microsoft Q&A](/answers/topics/azure-policy.html) 获得专家提供的答案。
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
