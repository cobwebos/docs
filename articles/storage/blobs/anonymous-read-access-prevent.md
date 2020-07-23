---
title: 阻止对容器和 blob 的匿名公共读取访问
titleSuffix: Azure Storage
description: 了解如何针对存储帐户分析匿名请求，以及如何防止对整个存储帐户或单独容器的匿名访问。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 24d726f7600c3ba80833640be8036bf0daa2c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518718"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>阻止对容器和 blob 的匿名公共读取访问

对 Azure 存储中容器和 blob 的匿名公共读取访问是共享数据的一种简便方法，但也可能会带来安全风险。 非常重要的是，必须谨慎管理匿名访问，并了解如何评估匿名访问数据。 对于可公开访问的数据，操作复杂性、人为错误或恶意攻击会导致成本高昂的数据泄露。 Microsoft 建议仅在需要时为应用程序方案启用匿名访问。

默认情况下，具有适当权限的用户可以配置对容器和 blob 的公共访问权限。 可以在存储帐户级别阻止所有公共访问。 如果你禁止访问存储帐户的公共 blob，则不能为帐户中的容器配置公共访问权限。 已配置为公开访问的所有容器将不再接受匿名请求。 有关详细信息，请参阅[配置容器和 blob 的匿名公共读取访问](anonymous-read-access-configure.md)。

本文介绍如何针对存储帐户分析匿名请求，以及如何防止对整个存储帐户或单独容器的匿名访问。

## <a name="detect-anonymous-requests-from-client-applications"></a>检测来自客户端应用程序的匿名请求

如果你不允许对存储帐户进行公共读取访问，则可能会拒绝对当前配置为公开访问的容器和 blob 的请求。 禁用存储帐户的公共访问权限会替代该存储帐户中所有容器的公共访问设置。 如果不允许对存储帐户进行公共访问，以后对该帐户的任何匿名请求都将失败。

为了理解禁止公共访问可能会对客户端应用程序产生何种影响，Microsoft 建议你启用该帐户的日志记录和度量值，并在时间间隔内分析匿名请求的模式。 使用度量值来确定对存储帐户的匿名请求数，并使用日志来确定要匿名访问的容器。

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>监视指标资源管理器的匿名请求

若要跟踪对存储帐户的匿名请求，请在 Azure 门户中使用 Azure 指标资源管理器。 有关指标资源管理器的详细信息，请参阅[Azure 指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)入门。

按照以下步骤创建跟踪匿名请求的指标：

1. 导航到 Azure 门户中的存储帐户。 在 "**监视**" 部分下，选择 "**指标**"。
1. 选择“添加指标”。 在 "**指标**" 对话框中，指定以下值：
    1. 将 "作用域" 字段设置为存储帐户的名称。
    1. 将**指标命名空间**设置为*Blob*。 此指标仅报告对 Blob 存储的请求。
    1. 将 "**指标**" 字段设置为 "*事务*"。
    1. 将**聚合**字段设置为*Sum*。

    新度量值将显示给定时间间隔内针对 Blob 存储的事务数之和。 生成的指标显示如下图所示：

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="显示如何将指标配置为对 blob 事务求和的屏幕截图":::

1. 接下来，选择 "**添加筛选器**" 按钮，为匿名请求的指标创建筛选器。
1. 在 "**筛选器**" 对话框中，指定以下值：
    1. 将**属性**值设置为*Authentication*。
    1. 将 "**运算符**" 字段设置为等号（=）。
    1. 将 "**值**" 字段设置为 "*匿名*"。
1. 在右上角，选择要查看指标的时间间隔。 还可以通过指定从1分钟到1个月之间的时间间隔，来指示请求聚合的粒度。

配置指标后，匿名请求将开始显示在图形上。 下图显示在过去的30分钟内聚合的匿名请求。

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="显示针对 Blob 存储的聚合匿名请求的屏幕截图":::

你还可以配置警报规则，以便在针对存储帐户发出一定数量的匿名请求时通知你。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>分析日志以识别接收匿名请求的容器

Azure 存储日志捕获有关针对存储帐户发出的请求的详细信息，包括请求的授权方式。 你可以分析这些日志，以确定接收匿名请求的容器。

若要将请求记录到 Azure 存储帐户，以便评估匿名请求，可以在 Azure Monitor （预览版）中使用 Azure 存储日志记录。 有关详细信息，请参阅[监视 Azure 存储](../common/monitor-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要了解有关日志查询的详细信息，请参阅[教程： Log Analytics 查询入门](../../azure-monitor/log-query/get-started-portal.md)。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 门户中创建诊断设置

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须首先创建诊断设置，以指示要为其记录数据的请求类型和存储服务。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. [在 Azure Monitor 预览中注册 Azure 存储日志记录](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在订阅中创建一个包含你的 Azure 存储帐户的新 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 "Log Analytics" 工作区中可用。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在 "监视" 部分，选择 "**诊断设置（预览）**"。
1. 选择 " **blob** "，记录对 Blob 存储发出的请求。
1. 选择“添加诊断设置”。
1. 提供诊断设置的名称。
1. 在 "**类别详细信息**" 下的 "**日志**" 部分中，选择要记录的请求的类型。 所有匿名请求都是读取请求，因此请选择 " **StorageRead** " 以捕获匿名请求。
1. 在 "**目标详细信息**" 下，选择 "**发送到 Log Analytics**"。 选择之前创建的 "订阅" 和 "Log Analytics" 工作区，如下图所示。

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="显示如何为日志记录请求创建诊断设置的屏幕截图":::

创建诊断设置后，随后会根据该设置记录对存储帐户的请求。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。

有关 Azure Monitor 中的 Azure 存储日志中可用的字段的参考，请参阅[资源日志（预览）](../common/monitor-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-anonymous-requests"></a>匿名请求的查询日志

Azure Monitor 中的 Azure 存储日志包含用于向存储帐户发出请求的授权类型。 在日志查询中，筛选**AuthenticationType**属性以查看匿名请求。

若要检索过去7天对 Blob 存储的匿名请求的日志，请打开 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中并运行：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

你还可以根据此查询配置警报规则，以通知你有关匿名请求的信息。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/platform/alerts-log.md)。

## <a name="remediate-anonymous-public-access"></a>修正匿名公共访问

评估对存储帐户中的容器和 blob 的匿名请求后，可以采取措施限制或阻止公共访问。 如果你的存储帐户中的某些容器可能需要提供公共访问权限，则可以在存储帐户中配置每个容器的公共访问设置。 此选项提供对公共访问的最精细控制。 有关详细信息，请参阅[设置容器的公共访问级别](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)。

为增强安全性，可以禁止对整个存储帐户进行公共访问。 存储帐户的公共访问设置将覆盖该帐户中的容器的各个设置。 如果你不允许对存储帐户进行公共访问，则任何配置为允许公共访问的容器将不再以匿名方式进行访问。 有关详细信息，请参阅[允许或禁止访问存储帐户的公共读取权限](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)。

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

## <a name="next-steps"></a>后续步骤

- [配置容器和 blob 的匿名公共读取访问权限](anonymous-read-access-configure.md)
- [使用 .NET 以匿名方式访问公共容器和 blob](anonymous-read-access-client.md)
