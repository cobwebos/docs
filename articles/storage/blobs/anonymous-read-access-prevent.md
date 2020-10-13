---
title: 阻止对容器和 blob 的匿名公共读取访问
titleSuffix: Azure Storage
description: 了解如何针对存储帐户分析匿名请求，以及如何防止对整个存储帐户或单独容器的匿名访问。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 3d843440adc61b315616a05f223c5a13ebe271ed
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930826"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>阻止对容器和 blob 的匿名公共读取访问

对 Azure 存储中的容器和 blob 进行匿名公共读取访问虽然是共享数据的一种简便方法，但也可能会带来安全风险。 非常重要的是，必须谨慎管理匿名访问，并了解如何评估匿名访问数据。 对于可公开访问的数据，操作复杂性、人为错误或恶意攻击会导致成本高昂的数据泄露。 Microsoft 建议仅在需要时为应用程序方案启用匿名访问。

默认情况下，始终禁止对 blob 数据进行公共访问。 但是，存储帐户的默认配置允许具有适当权限的用户配置对存储帐户中的容器和 blob 的公共访问。 为增强安全性，无论单独容器的公共访问设置如何，都可以禁止对存储帐户进行所有公共访问。 禁止对存储帐户进行公共访问可以阻止用户启用帐户中的容器的公共访问权限。 若非有必要，Microsoft 建议禁止对存储帐户进行公共访问。 禁止公共访问有助于防止意外的匿名访问产生的数据泄露。

当你禁止访问存储帐户的公共 blob 时，Azure 存储将拒绝对该帐户的所有匿名请求。 帐户不允许公共访问后，该帐户中的容器以后无法进行公共访问。 已配置为公开访问的所有容器将不再接受匿名请求。 有关详细信息，请参阅 [配置容器和 blob 的匿名公共读取访问](anonymous-read-access-configure.md)。

本文介绍如何使用拖 (检测-修正-管理) 框架来持续管理存储帐户的公共访问权限。

## <a name="detect-anonymous-requests-from-client-applications"></a>检测来自客户端应用程序的匿名请求

如果你不允许对存储帐户进行公共读取访问，则可能会拒绝对当前配置为公开访问的容器和 blob 的请求。 禁用存储帐户的公共访问权限会替代该存储帐户中各个容器的公共访问设置。 如果不允许对存储帐户进行公共访问，以后对该帐户的任何匿名请求都将失败。

为了理解禁止公共访问可能会对客户端应用程序产生何种影响，Microsoft 建议你启用该帐户的日志记录和度量值，并在时间间隔内分析匿名请求的模式。 使用度量值来确定对存储帐户的匿名请求数，并使用日志来确定要匿名访问的容器。

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>监视指标资源管理器的匿名请求

若要跟踪对存储帐户的匿名请求，请在 Azure 门户中使用 Azure 指标资源管理器。 有关指标资源管理器的详细信息，请参阅 [Azure 指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)入门。

按照以下步骤创建跟踪匿名请求的指标：

1. 导航到 Azure 门户中的存储帐户。 在 " **监视** " 部分下，选择 " **指标**"。
1. 选择“添加指标”。 在 " **指标** " 对话框中，指定以下值：
    1. 将 "作用域" 字段设置为存储帐户的名称。
    1. 将 **指标命名空间** 设置为 *Blob*。 此指标仅报告对 Blob 存储的请求。
    1. 将 " **指标** " 字段设置为 " *事务*"。
    1. 将 **聚合** 字段设置为 *Sum*。

    新度量值将显示给定时间间隔内针对 Blob 存储的事务数之和。 生成的指标显示如下图所示：

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="显示如何将指标配置为对 blob 事务求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为匿名请求的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** " 字段设置为等号 (=) 。
    1. 将 " **值** " 字段设置为 " *匿名*"。
1. 在右上角，选择要查看指标的时间间隔。 还可以通过指定从1分钟到1个月之间的时间间隔，来指示请求聚合的粒度。

配置指标后，匿名请求将开始显示在图形上。 下图显示在过去的30分钟内聚合的匿名请求。

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="显示如何将指标配置为对 blob 事务求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为匿名请求的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** ":::

你还可以配置警报规则，以便在针对存储帐户发出一定数量的匿名请求时通知你。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>分析日志以识别接收匿名请求的容器

Azure 存储日志捕获有关针对存储帐户发出的请求的详细信息，包括请求的授权方式。 你可以分析这些日志，以确定接收匿名请求的容器。

若要将请求记录到 Azure 存储帐户，以便评估匿名请求，可以使用 Azure 存储日志记录 Azure Monitor (预览 ") 中。 有关详细信息，请参阅 [监视 Azure 存储](../common/monitor-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要了解有关日志查询的详细信息，请参阅 [教程： Log Analytics 查询入门](../../azure-monitor/log-query/get-started-portal.md)。

> [!NOTE]
> 仅在 Azure 公有云中支持 Azure Monitor 中的 Azure 存储日志记录预览。 政府云不支持 Azure Monitor 的 Azure 存储的日志记录。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 门户中创建诊断设置

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须首先创建诊断设置，以指示要为其记录数据的请求类型和存储服务。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. [在 Azure Monitor 预览中注册 Azure 存储日志记录](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在订阅中创建一个包含你的 Azure 存储帐户的新 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 "Log Analytics" 工作区中可用。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在 "监视" 部分，选择 " **诊断设置" (预览 ") **。
1. 选择 " **blob** "，记录对 Blob 存储发出的请求。
1. 选择“添加诊断设置”。
1. 提供诊断设置的名称。
1. 在 " **类别详细信息**" 下的 " **日志** " 部分中，选择要记录的请求的类型。 所有匿名请求都是读取请求，因此请选择 " **StorageRead** " 以捕获匿名请求。
1. 在 " **目标详细信息**" 下，选择 " **发送到 Log Analytics**"。 选择之前创建的 "订阅" 和 "Log Analytics" 工作区，如下图所示。

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="显示如何将指标配置为对 blob 事务求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为匿名请求的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** ":::

创建诊断设置后，随后会根据该设置记录对存储帐户的请求。 有关详细信息，请参阅 [创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。

有关 Azure Monitor 中的 Azure 存储日志中可用的字段的参考，请参阅 [资源日志 (预览版) ](../common/monitor-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-anonymous-requests"></a>匿名请求的查询日志

Azure Monitor 中的 Azure 存储日志包含用于向存储帐户发出请求的授权类型。 在日志查询中，筛选 **AuthenticationType** 属性以查看匿名请求。

若要检索过去7天对 Blob 存储的匿名请求的日志，请打开 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中并运行：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

你还可以根据此查询配置警报规则，以通知你有关匿名请求的信息。 有关详细信息，请参阅 [使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/platform/alerts-log.md)。

## <a name="remediate-anonymous-public-access"></a>修正匿名公共访问

评估对存储帐户中的容器和 blob 的匿名请求后，可以采取措施限制或阻止公共访问。 如果你的存储帐户中的某些容器可能需要提供公共访问权限，则可以在存储帐户中配置每个容器的公共访问设置。 此选项提供对公共访问的最精细控制。 有关详细信息，请参阅 [设置容器的公共访问级别](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)。

为增强安全性，可以禁止对整个存储帐户进行公共访问。 存储帐户的公共访问设置将覆盖该帐户中的容器的各个设置。 如果你不允许对存储帐户进行公共访问，则任何配置为允许公共访问的容器将不再以匿名方式进行访问。 有关详细信息，请参阅 [允许或禁止访问存储帐户的公共读取权限](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)。

如果你的方案需要某些容器需要提供公共访问权限，则可能建议将这些容器及其 blob 移动到保留用于公共访问的存储帐户中。 然后，你可以禁止对任何其他存储帐户进行公共访问。

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>验证是否不允许对 blob 进行公共访问

若要验证是否允许对特定 blob 进行公共访问，你可以尝试通过其 URL 下载 blob。 如果下载成功，则 blob 仍可公开使用。 如果 blob 无法公开访问，因为存储帐户不允许公共访问，则你将看到一条错误消息，指示此存储帐户不允许公共访问。

下面的示例演示如何使用 PowerShell 通过其 URL 来尝试下载 blob。 请记住，用自己的值替换括号中的占位符值：

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>验证是否不允许修改容器的公共访问设置

若要验证在禁止访问存储帐户的公共访问后无法修改容器的公共访问设置，可以尝试修改该设置。 如果不允许对存储帐户进行公共访问，则更改容器的公共访问设置将会失败。

下面的示例演示如何使用 PowerShell 来尝试更改容器的公共访问设置。 请记住，用自己的值替换括号中的占位符值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>验证是否不允许创建启用了公共访问的容器

如果存储帐户不允许公共访问，则你将无法创建启用公共访问的新容器。 若要验证，可以尝试创建启用了公共访问的容器。

下面的示例演示如何使用 PowerShell 来尝试创建启用了公共访问的容器。 请记住，用自己的值替换括号中的占位符值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>检查多个帐户的公共访问设置

若要在具有最佳性能的一组存储帐户中检查公共访问设置，可以使用 Azure 门户中的 Azure 资源图资源管理器。 若要了解有关使用资源图资源管理器的详细信息，请参阅 [快速入门：使用 Azure 资源关系图资源管理器运行第一个资源图形查询](/azure/governance/resource-graph/first-query-portal)。

在资源图资源管理器中运行以下查询将返回存储帐户的列表，并显示每个帐户的公共访问设置：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure 策略审核符合性

如果有大量存储帐户，则可能需要执行审核来确保将这些帐户配置为阻止公共访问。 若要审核一组存储帐户的符合性，请使用 Azure 策略。 Azure 策略是一种服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure 策略可帮助确保这些资源符合公司标准和服务级别协议。 有关详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>创建具有审核效果的策略

Azure 策略支持确定对资源评估策略规则时会发生什么情况的影响。 当资源不符合标准，但不停止请求时，审核效果将创建警告。 有关效果的详细信息，请参阅 [了解 Azure 策略影响](../../governance/policy/concepts/effects.md)。

若要为具有 Azure 门户的存储帐户的公共访问设置创建策略，请执行以下步骤：

1. 在 Azure 门户中，导航到 "Azure 策略服务"。
1. 在 " **创作** " 部分下，选择 " **定义**"。
1. 选择 " **添加策略定义** "，创建新策略定义。
1. 对于 " **定义位置** " 字段，请选择 " **更多** " 按钮以指定审核策略资源所在的位置。
1. 指定策略的名称。 您可以根据需要指定描述和类别。
1. 在 " **策略规则**" 下，将以下策略定义添加到 " **policyRule** " 部分。

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. 保存策略。

### <a name="assign-the-policy"></a>分配策略

接下来，将策略分配给资源。 策略的作用域对应于该资源及其下的所有资源。 有关策略分配的详细信息，请参阅 [Azure 策略分配结构](../../governance/policy/concepts/assignment-structure.md)。

若要将策略分配到 Azure 门户，请执行以下步骤：

1. 在 Azure 门户中，导航到 "Azure 策略服务"。
1. 在 " **创作** " 部分下，选择 " **分配**"。
1. 选择 " **分配策略** " 以创建新的策略分配。
1. 对于 " **作用域** " 字段，请选择策略分配的作用域。
1. 对于 " **策略定义** " 字段，请选择 " **更多** " 按钮，然后从列表中选择在上一部分中定义的策略。
1. 提供策略分配的名称。 说明是可选的。
1. 将 " **策略强制** 集" 设置为 " *已启用*"。 此设置不影响审核策略。
1. 选择 " **查看 + 创建** " 创建分配。

### <a name="view-compliance-report"></a>查看相容性报告

分配策略后，可以查看符合性报告。 审核策略的相容性报告提供有关不符合策略的存储帐户的信息。 有关详细信息，请参阅 [获取策略符合性数据](../../governance/policy/how-to/get-compliance-data.md)。

创建策略分配后，相容性报告可能需要几分钟的时间。

若要查看 Azure 门户中的相容性报告，请执行以下步骤：

1. 在 Azure 门户中，导航到 "Azure 策略服务"。
1. 选择“合规性”。
1. 筛选在上一步中创建的策略分配名称的结果。 该报表显示不符合策略的资源数量。
1. 你可以向下钻取到报表以获取更多详细信息，包括不合规的存储帐户的列表。

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="显示如何将指标配置为对 blob 事务求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为匿名请求的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** ":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>使用 Azure 策略强制实施授权访问

Azure 策略通过确保 Azure 资源符合要求和标准来支持云监管。 若要确保组织中的存储帐户仅允许授权的请求，你可以创建一个策略，该策略阻止创建新的存储帐户，并使用允许匿名请求的公共访问设置。 如果该帐户的公共访问设置不符合策略，则此策略还将阻止对现有帐户进行所有配置更改。

强制策略使用拒绝效果来阻止请求创建或修改存储帐户以允许公共访问。 有关效果的详细信息，请参阅 [了解 Azure 策略影响](../../governance/policy/concepts/effects.md)。

若要创建对允许匿名请求的公共访问设置具有拒绝影响的策略，请遵循 [使用 Azure 策略审核符合性](#use-azure-policy-to-audit-for-compliance)中所述的相同步骤，但在策略定义的 **policyRule** 节中提供以下 JSON：

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

创建具有拒绝效果的策略并将其分配给作用域后，用户无法创建允许公共访问的存储帐户。 用户也不能对当前允许公共访问的现有存储帐户进行任何配置更改。 尝试这样做会导致错误。 存储帐户的公共访问设置必须设置为 **false** ，才能继续创建或配置帐户。

下图显示了当你尝试创建一个存储帐户时，如果你尝试创建一个存储帐户以允许公共访问 (新帐户的默认值，则在此情况下) 会要求拒绝公共访问。

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="显示如何将指标配置为对 blob 事务求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为匿名请求的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** ":::

## <a name="next-steps"></a>后续步骤

- [配置容器和 blob 的匿名公共读取访问权限](anonymous-read-access-configure.md)
- [使用 .NET 以匿名方式访问公共容器和 blob](anonymous-read-access-client.md)
