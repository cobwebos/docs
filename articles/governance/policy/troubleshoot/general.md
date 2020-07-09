---
title: 排查常见错误
description: 了解如何排查为 Kubernetes 创建策略定义、各种 SDK 和外接程序时遇到的问题。
ms.date: 05/22/2020
ms.topic: troubleshooting
ms.openlocfilehash: 6d23a148521506adf0c0fc16913a32aab5eb7a30
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135577"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>使用 Azure 策略排查错误

创建策略定义、使用 SDK 或设置[Azure policy For Kubernetes](../concepts/policy-for-kubernetes.md)外接程序时，可能会遇到错误。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

错误详细信息的位置取决于导致错误的操作。

- 使用自定义策略时，请尝试在 Azure 门户中获取有关该架构的 linting 反馈，或查看生成的[符合性数据](../how-to/get-compliance-data.md)以查看资源的评估方式。
- 使用各种 SDK 时，SDK 提供有关函数失败原因的详细信息。
- 使用 Kubernetes 的外接程序时，请从群集中的[日志记录](../concepts/policy-for-kubernetes.md#logging)开始。

## <a name="general-errors"></a>常规错误

### <a name="scenario-alias-not-found"></a>方案：找不到别名

#### <a name="issue"></a>问题

Azure 策略使用[别名](../concepts/definition-structure.md#aliases)映射到 Azure 资源管理器属性。

#### <a name="cause"></a>原因

在策略定义中使用的别名不正确或不存在。

#### <a name="resolution"></a>解决方法

首先，验证资源管理器属性是否有别名。 使用[Azure Policy extension Visual Studio Code](../how-to/extension-for-vscode.md)、 [azure 资源图](../../resource-graph/samples/starter.md#distinct-alias-values)或 SDK 查找可用的别名。 如果资源管理器属性的别名不存在，请创建支持票证。

### <a name="scenario-evaluation-details-not-up-to-date"></a>方案：计算详细信息不是最新的

#### <a name="issue"></a>问题

资源处于 "未启动" 状态或符合性详细信息不是最新状态。

#### <a name="cause"></a>原因

应用新策略或计划分配需要大约30分钟。 现有分配范围内的新资源或更新的资源将于15分钟后可用。 标准符合性扫描每24小时进行一次。 有关详细信息，请参阅[计算触发器](../how-to/get-compliance-data.md#evaluation-triggers)。

#### <a name="resolution"></a>解决方法

首先，请等待适当的时间来完成评估，并在 Azure 门户或 SDK 中提供符合性结果。 若要使用 Azure PowerShell 或 REST API 启动新的评估扫描，请参阅按[需评估扫描](../how-to/get-compliance-data.md#on-demand-evaluation-scan)。

### <a name="scenario-evaluation-not-as-expected"></a>方案：评估不符合预期

#### <a name="issue"></a>问题

资源未处于_符合_或_不符合_的评估状态，这是该资源的预期。

#### <a name="cause"></a>原因

资源不在策略分配的正确范围内，或者策略定义不按预期操作。

#### <a name="resolution"></a>解决方法

- 对于预期符合的不合规资源，请先[确定不符合的原因](../how-to/determine-non-compliance.md)。 定义与计算属性值的比较表明资源不符合的原因。
- 对于应为不符合的符合性资源，请按条件读取策略定义条件并根据资源属性进行评估。 验证逻辑运算符是否将正确的条件组合在一起，并且条件不会反转。

如果策略分配符合性显示 `0/0` 资源，则未确定在分配范围内适用的资源。 检查策略定义和分配范围。

### <a name="scenario-enforcement-not-as-expected"></a>方案：不按预期执行

#### <a name="issue"></a>问题

Azure 策略预期会处理的资源不存在， [Azure 活动日志](../../../azure-monitor/platform/platform-logs-overview.md)中没有条目。

#### <a name="cause"></a>原因

已为_禁用_的[enforcementMode](../concepts/assignment-structure.md#enforcement-mode)配置策略分配。 当强制模式处于禁用状态时，不会强制实施策略效果，并且活动日志中没有条目。

#### <a name="resolution"></a>解决方法

将**enforcementMode**更新为_Enabled_。 此更改允许 Azure 策略处理此策略分配中的资源，并将条目发送到活动日志。 如果已启用**enforcementMode** ，请参阅操作课程的[评估不按预期](#scenario-evaluation-not-as-expected)。

### <a name="scenario-denied-by-azure-policy"></a>方案： Azure 策略拒绝

#### <a name="issue"></a>问题

拒绝创建或更新资源。

#### <a name="cause"></a>原因

新资源或更新资源所在范围的策略分配符合具有[拒绝](../concepts/effects.md#deny)影响的策略定义的条件。 会阻止创建或更新这些定义的资源。

#### <a name="resolution"></a>解决方法

拒绝策略分配中的错误消息包括策略定义和策略分配 Id。 如果消息中的错误信息丢失，则也可在[活动日志](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)中找到。 使用此信息可获取更多详细信息，以了解资源限制并调整请求中的资源属性以匹配允许的值。

## <a name="template-errors"></a>模板错误

### <a name="scenario-policy-supported-functions-processed-by-template"></a>方案：按模板处理的策略支持的函数

#### <a name="issue"></a>问题

Azure 策略支持大量 Azure 资源管理器模板（ARM 模板）函数和仅在策略定义中可用的函数。 资源管理器将这些函数作为部署的一部分而不是作为策略定义的一部分进行处理。

#### <a name="cause"></a>原因

使用支持的函数（如 `parameter()` 或 `resourceGroup()` ）会在部署时导致函数的处理结果，而不是离开策略定义的函数和 Azure 策略引擎来进行处理。

#### <a name="resolution"></a>解决方法

若要通过将函数传递到作为策略定义的一部分，请将整个字符串转义，使 `[` 属性类似于 `[[resourceGroup().tags.myTag]` 。 转义符会导致在处理模板时资源管理器将值视为字符串。 然后，Azure 策略将函数放置在策略定义中，使其能够按预期方式动态进行。 有关详细信息，请参阅[Azure 资源管理器模板中的语法和表达式](../../../azure-resource-manager/templates/template-expressions.md)。

## <a name="add-on-installation-errors"></a>外接程序安装错误

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>方案：使用 Helm 的安装在密码上失败

#### <a name="issue"></a>问题

`helm install azure-policy-addon`命令失败，并出现以下消息之一：

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

生成的密码包含 Helm 图表要拆分的逗号（ `,` ）。

#### <a name="resolution"></a>解决方法

`,` `helm install azure-policy-addon` 使用反斜杠（）运行时，将密码值中的逗号（）转义 `\` 。

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>方案：使用 Helm 的安装失败，因为名称已经存在

#### <a name="issue"></a>问题

`helm install azure-policy-addon`命令失败，并出现以下消息：

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

已安装或部分安装了名称的 Helm 图表 `azure-policy-addon` 。

#### <a name="resolution"></a>解决方法

按照说明[删除 Azure Policy For Kubernetes 外接程序](../concepts/policy-for-kubernetes.md#remove-the-add-on)，然后重新运行该 `helm install azure-policy-addon` 命令。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过[Microsoft Q&](/answers/topics/azure-policy.html)获取专家的答案。
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
