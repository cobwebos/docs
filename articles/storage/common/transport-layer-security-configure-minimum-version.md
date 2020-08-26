---
title: '为传入请求强制执行最低要求的传输层安全版本 (TLS) '
titleSuffix: Azure Storage
description: 将存储帐户配置为要求对 Azure 存储发出请求的客户端 (TLS) 的最低版本的传输层安全性。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 2439bec08c16ce109b271844dc72b8fd2569aa07
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755902"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>为存储帐户请求强制执行最低要求的传输层安全性版本 (TLS) 

客户端应用程序和 Azure 存储帐户之间的通信是使用传输层安全性 (TLS) 进行加密的。 TLS 是一种标准的加密协议，通过 Internet 确保客户端和服务之间的隐私和数据完整性。 有关 TLS 的详细信息，请参阅 [传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)。

Azure 存储目前支持三种版本的 TLS 协议：1.0、1.1 和1.2。 Azure 存储使用公有 HTTPS 终结点上的 TLS 1.2，但仍支持使用 TLS 1.0 和 TLS 1.1 来实现向后兼容性。

默认情况下，Azure 存储帐户允许客户端使用最早版本的 TLS、TLS 1.0 和更高版本发送和接收数据。 若要强制实施更严格的安全措施，可以将存储帐户配置为要求客户端使用较新版本的 TLS 发送和接收数据。 如果存储帐户需要最小版本的 TLS，则使用较旧版本发出的任何请求都将失败。

本文介绍如何使用拖 (检测-修正-) 框架来持续管理存储帐户的安全 TLS。

有关如何在从客户端应用程序发送请求时指定 TLS 的特定版本的信息，请参阅 [配置客户端应用程序 (tls) 的传输层安全性](transport-layer-security-configure-client-version.md)。

## <a name="detect-the-tls-version-used-by-client-applications"></a>检测客户端应用程序使用的 TLS 版本

当你为你的存储帐户强制实施最低 TLS 版本时，可能会拒绝使用较旧版本的 TLS 发送数据的客户端发出的请求。 为了了解如何配置最低 TLS 版本可能会影响客户端应用程序，Microsoft 建议你为 Azure 存储帐户启用日志记录，并在时间间隔后分析日志，以检测 TLS 客户端应用程序正在使用的版本。

若要将请求记录到 Azure 存储帐户并确定客户端使用的 TLS 版本，可以使用 Azure 存储日志记录 Azure Monitor (预览 ") "。 有关详细信息，请参阅 [监视 Azure 存储](monitor-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要了解有关日志查询的详细信息，请参阅 [教程： Log Analytics 查询入门](../../azure-monitor/log-query/get-started-portal.md)。

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须首先创建诊断设置，以指示要为其记录数据的请求类型和存储服务。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. [在 Azure Monitor 预览中注册 Azure 存储日志记录](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在订阅中创建一个包含你的 Azure 存储帐户的新 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 "Log Analytics" 工作区中可用。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在 "监视" 部分，选择 " **诊断设置" (预览 ") **。
1. 选择要为其记录请求的 Azure 存储服务。 例如，选择 " **blob** " 将请求记录到 blob 存储中。
1. 选择“添加诊断设置”。
1. 提供诊断设置的名称。
1. 在 " **类别详细信息**" 下的 " **日志** " 部分中，选择要记录的请求的类型。 可以记录读取、写入和删除请求。 例如，选择 **StorageRead** 和 **StorageWrite** 会记录对所选服务的读取和写入请求。
1. 在 " **目标详细信息**" 下，选择 " **发送到 Log Analytics**"。 选择之前创建的 "订阅" 和 "Log Analytics" 工作区，如下图所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="显示如何为日志记录请求创建诊断设置的屏幕截图":::

创建诊断设置后，随后会根据该设置记录对存储帐户的请求。 有关详细信息，请参阅 [创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。

有关 Azure Monitor 中的 Azure 存储日志中可用的字段的参考，请参阅 [资源日志 (预览版) ](monitor-storage-reference.md#resource-logs-preview)。

### <a name="query-logged-requests-by-tls-version"></a>按 TLS 版本查询记录的请求

Azure Monitor 中的 Azure 存储日志包含用于向存储帐户发送请求的 TLS 版本。 使用 **TlsVersion** 属性检查已记录请求的 TLS 版本。

若要确定在过去七天内针对不同版本的 TLS 对 Blob 存储发出的请求数，请打开 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中，并运行该查询。 请记住，用自己的值替换括号中的占位符值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

结果显示了每个 TLS 版本发出的请求数的计数：

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="显示要返回 TLS 版本的 log analytics 查询结果的屏幕截图":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>按调用方 IP 地址和用户代理标头查询记录的请求

Azure Monitor 中的 Azure 存储日志还包括调用方 IP 地址和用户代理标头，以帮助你评估哪些客户端应用程序访问了存储帐户。 你可以分析这些值，以确定是否必须将客户端应用程序更新为使用较新的 TLS 版本，或者是否可以在客户端的请求未使用最低 TLS 版本发送的情况下使其失败。

若要确定在过去七天内哪些客户端使用的 TLS 版本低于 TLS 1.2，请将以下查询粘贴到新的日志查询中并运行。 请记住，用自己的值替换括号中的占位符值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>使用最小版本的 TLS 修正安全风险

当你确信来自使用较旧版本 TLS 的客户端的流量是最少的，或者如果使用较旧版本的 TLS 发出的请求可接受，则可以在你的存储帐户上开始强制执行最低 TLS 版本。 要求客户端使用最低版本的 TLS 向存储帐户发出请求是将数据的安全风险降至最低的策略的一部分。

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>为存储帐户配置最低 TLS 版本

若要为存储帐户配置最低 TLS 版本，请设置帐户的 **MinimumTlsVersion** 版本。 此属性适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 有关 Azure 资源管理器部署模型的详细信息，请参阅 [存储帐户概述](storage-account-overview.md)。

> [!NOTE]
> 默认情况下， **minimumTlsVersion** 属性不会设置，并且不会返回值，除非你显式设置该值。 如果属性值为 **null**，则存储帐户允许以 TLS 版本1.0 或更高版本发送的请求。

# <a name="portal"></a>[门户](#tab/portal)

若要为具有 Azure 门户的存储帐户配置最低 TLS 版本，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 选择**配置**设置。
1. 在 " **最低 tls 版本**" 下，使用下拉选择访问此存储帐户中的数据所需的最低 tls 版本，如下图所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="显示如何在 Azure 门户中配置 TLS 的最低版本的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 配置存储帐户的最低 TLS 版本，请安装 [Azure PowerShell 版本 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) 或更高版本。 接下来，为新的或现有的存储帐户配置 **MinimumTLSVersion** 属性。 **MinimumTlsVersion**的有效值为 `TLS1_0` 、 `TLS1_1` 和 `TLS1_2` 。

以下示例创建了一个存储帐户并将 **MinimumTLSVersion** 设置为 tls 1.1，然后更新该帐户并将 **MINIMUMTLSVERSION** 设置为 tls 1.2。 该示例还检索每个事例中的属性值。 请记住，用自己的值替换括号中的占位符值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName \
    -AccountName $accountName \
    -Location $location \
    -SkuName Standard_GRS \
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName \
    -AccountName $accountName \
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要为具有 Azure CLI 的存储帐户配置最低 TLS 版本，请安装 Azure CLI 2.9.0 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 接下来，为新的或现有的存储帐户配置 **minimumTlsVersion** 属性。 **MinimumTlsVersion**的有效值为 `TLS1_0` 、 `TLS1_1` 和 `TLS1_2` 。

以下示例创建了一个存储帐户，并将 **minimumTLSVersion** 设置为 TLS 1.1。 然后更新帐户并将 **minimumTLSVersion** 属性设置为 TLS 1.2。 该示例还检索每个事例中的属性值。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[模板](#tab/template)

若要为带有模板的存储帐户配置最低 TLS 版本，请创建 **MinimumTLSVersion** 属性设置为、或的模板 `TLS1_0` `TLS1_1` `TLS1_2` 。 以下步骤介绍如何在 Azure 门户中创建模板。

1. 在 Azure 门户中，选择“创建资源”。
1. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
1. 选择 " **使用自定义模板 (部署")  (预览) **"，选择" **创建**"，然后选择 **" 在编辑器中生成自己的模板 "** 模板部署。
1. 在模板编辑器中，粘贴以下 JSON 以创建新帐户，并将最低 TLS 版本设置为 TLS 1.2。 请记得将尖括号中的占位符替换为自己的值。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. 保存模板。
1. 指定资源组参数，然后选择 " **查看** " 和 "创建" 按钮以部署模板并创建配置了 **MinimumTLSVersion** 属性的存储帐户。

---

> [!NOTE]
> 更新存储帐户的最低 TLS 版本后，最多可能需要30秒才能完全传播更改。

配置最低 TLS 版本需要 Azure 存储资源提供程序的2019-04-01 版或更高版本。 有关详细信息，请参阅 [Azure 存储资源提供程序 REST API](/rest/api/storagerp/)。

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>检查多个帐户的所需最小 TLS 版本

若要在具有最佳性能的一组存储帐户中检查所需的最低 TLS 版本，可以使用 Azure 门户中的 Azure 资源图资源管理器。 若要了解有关使用资源图资源管理器的详细信息，请参阅 [快速入门：使用 Azure 资源关系图资源管理器运行第一个资源图形查询](/azure/governance/resource-graph/first-query-portal)。

在资源图资源管理器中运行以下查询将返回存储帐户的列表，并显示每个帐户的最低 TLS 版本：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>测试客户端的最低 TLS 版本

若要测试存储帐户所需的最小 TLS 版本是否禁止使用较旧版本发出的调用，可以将客户端配置为使用较旧版本的 TLS。 有关将客户端配置为使用特定 TLS 版本的详细信息，请参阅 [配置客户端应用程序 (TLS) 的传输层安全性](transport-layer-security-configure-client-version.md)。

当客户端使用不满足为帐户配置的最低 TLS 版本的 TLS 版本访问存储帐户时，Azure 存储将返回错误代码400错误 (错误请求) ，并返回一条消息，指示不允许对此存储帐户发出请求。

## <a name="use-azure-policy-to-audit-for-compliance"></a>使用 Azure 策略审核符合性

如果有大量存储帐户，则可能需要执行审核，以确保所有帐户都配置为组织所需的最小 TLS 版本。 若要审核一组存储帐户的符合性，请使用 Azure 策略。 Azure 策略是一种服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure 策略可帮助确保这些资源符合公司标准和服务级别协议。 有关详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md)。

### <a name="create-a-policy-with-an-audit-effect"></a>创建具有审核效果的策略

Azure 策略支持确定对资源评估策略规则时会发生什么情况的影响。 当资源不符合标准，但不停止请求时，审核效果将创建警告。 有关效果的详细信息，请参阅 [了解 Azure 策略影响](../../governance/policy/concepts/effects.md)。

若要使用 Azure 门户为最低 TLS 版本创建策略，请执行以下步骤：

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
              "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
              "equals": "TLS1_2"
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

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="显示最低 TLS 版本的审核策略的相容性报告的屏幕截图":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>使用 Azure 策略强制实施最低 TLS 版本

Azure 策略通过确保 Azure 资源符合要求和标准来支持云监管。 若要为组织中的存储帐户强制执行最低 TLS 版本要求，你可以创建一个策略，该策略阻止创建新的存储帐户，该帐户将最小 TLS 要求设置为与策略所规定的 TLS 版本相比。 如果该帐户的最低 TLS 版本设置不符合策略，则此策略还将阻止对现有帐户进行所有配置更改。

强制策略使用拒绝效果来阻止请求创建或修改存储帐户，使最低 TLS 版本不再符合组织的标准。 有关效果的详细信息，请参阅 [了解 Azure 策略影响](../../governance/policy/concepts/effects.md)。

若要创建一个策略，该策略对小于 TLS 1.2 的最低 TLS 版本具有拒绝影响，请遵循 [使用 Azure 策略审核符合性](#use-azure-policy-to-audit-for-compliance)中所述的相同步骤，但在策略定义的 **policyRule** 节中提供以下 JSON：

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
          "field":"Microsoft.Storage/storageAccounts/minimumTlsVersion",
          "equals": "TLS1_2"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

创建具有拒绝效果的策略并将其分配给作用域后，用户无法创建最低 TLS 版本低于1.2 的存储帐户。 用户也不能对当前需要早于1.2 的最低 TLS 版本的现有存储帐户进行任何配置更改。 尝试这样做会导致错误。 必须将存储帐户所需的最低 TLS 版本设置为1.2，才能继续创建或配置帐户。

下图显示了在以下情况下发生的错误：尝试使用设置为 TLS 1.0 的最小 TLS 版本创建存储帐户 (新帐户的默认值) 如果策略具有拒绝效果，则需要将最低 TLS 版本设置为 TLS 1.2。

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="显示在违反策略时创建存储帐户时出现的错误的屏幕截图":::

## <a name="network-considerations"></a>网络注意事项

当客户端向存储帐户发送请求时，客户端首先会在处理任何请求之前，先与存储帐户的公共终结点建立连接。 建立连接后，将检查最低 TLS 版本设置。 如果请求使用的 TLS 比设置指定的更早版本，则连接将继续成功，但请求最终将失败。 有关 Azure 存储的公共终结点的详细信息，请参阅 [资源 URI 语法](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax)。

## <a name="next-steps"></a>后续步骤

- [为客户端应用程序配置 (TLS) 的传输层安全性](transport-layer-security-configure-client-version.md)
- [适用于 Blob 存储的安全建议](../blobs/security-recommendations.md)
