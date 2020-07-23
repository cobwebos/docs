---
title: 为存储帐户配置 (TLS) 的传输层安全性的最低要求版本
titleSuffix: Azure Storage
description: 将存储帐户配置为要求对 Azure 存储发出请求的客户端 (TLS) 的最低版本的传输层安全性。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209281"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>为存储帐户配置 (TLS) 的传输层安全性的最低要求版本

客户端应用程序和 Azure 存储帐户之间的通信是使用传输层安全性 (TLS) 进行加密的。 TLS 是一种标准的加密协议，通过 Internet 确保客户端和服务之间的隐私和数据完整性。 有关 TLS 的详细信息，请参阅[传输层安全性](https://en.wikipedia.org/wiki/Transport_Layer_Security)。

Azure 存储目前支持三种版本的 TLS 协议：1.0、1.1 和1.2。 TLS 1.2 是最安全的 TLS 版本。 Azure 存储使用公有 HTTPs 终结点上的 TLS 1.2，但仍支持使用 TLS 1.0 和 TLS 1.1 来实现向后兼容性。

默认情况下，Azure 存储帐户允许客户端使用最早版本的 TLS、TLS 1.0 和更高版本发送和接收数据。 若要强制实施更严格的安全措施，可以将存储帐户配置为要求客户端使用较新版本的 TLS 发送和接收数据。 如果存储帐户需要最小版本的 TLS，则使用早期版本发出的任何请求都将失败。

本文介绍如何将存储帐户配置为要求客户端使用最低版本的 TLS 发送请求。 有关如何在从客户端应用程序发送请求时指定 TLS 的特定版本的信息，请参阅[配置客户端应用程序 (tls) 的传输层安全性](transport-layer-security-configure-client-version.md)。

## <a name="detect-the-tls-version-used-by-client-applications"></a>检测客户端应用程序使用的 TLS 版本

当你为你的存储帐户强制实施最低 TLS 版本时，可能会拒绝使用较早版本的 TLS 发送数据的客户端发出的请求。 为了了解如何配置最低 TLS 版本可能会影响客户端应用程序，Microsoft 建议你为 Azure 存储帐户启用日志记录，并在时间间隔后分析日志，以确定要使用的 TLS 客户端应用程序的版本。

若要将请求记录到 Azure 存储帐户并确定客户端使用的 TLS 版本，可以使用 Azure 存储日志记录 Azure Monitor (预览 ") "。 有关详细信息，请参阅[监视 Azure 存储](monitor-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要了解有关日志查询的详细信息，请参阅[教程： Log Analytics 查询入门](../../azure-monitor/log-query/get-started-portal.md)。

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须首先创建诊断设置，以指示要为其记录数据的请求类型和存储服务。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. [在 Azure Monitor 预览中注册 Azure 存储日志记录](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在订阅中创建一个包含你的 Azure 存储帐户的新 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 "Log Analytics" 工作区中可用。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在 "监视" 部分，选择 "**诊断设置" (预览 ") **。
1. 选择要为其记录请求的 Azure 存储服务。 例如，选择 " **blob** " 将请求记录到 blob 存储中。
1. 选择“添加诊断设置”。
1. 提供诊断设置的名称。
1. 在 "**类别详细信息**" 下的 "**日志**" 部分中，选择要记录的请求的类型。 可以记录读取、写入和删除请求。 例如，选择**StorageRead**和**StorageWrite**会记录对所选服务的读取和写入请求。
1. 在 "**目标详细信息**" 下，选择 "**发送到 Log Analytics**"。 选择之前创建的 "订阅" 和 "Log Analytics" 工作区，如下图所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="显示如何为日志记录请求创建诊断设置的屏幕截图":::

创建诊断设置后，随后会根据该设置记录对存储帐户的请求。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。

有关 Azure Monitor 中的 Azure 存储日志中可用的字段的参考，请参阅[资源日志 (预览版) ](monitor-storage-reference.md#resource-logs-preview)。

### <a name="query-logged-requests-by-tls-version"></a>按 TLS 版本查询记录的请求

Azure Monitor 中的 Azure 存储日志包含用于向存储帐户发送请求的 TLS 版本。 使用**TlsVersion**属性检查已记录请求的 TLS 版本。

若要检索过去7天的日志，并确定每个 TLS 版本对 Blob 存储发出的请求数，请打开 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中，并运行该查询。 请记住，用自己的值替换括号中的占位符值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

结果显示了每个 TLS 版本发出的请求数的计数：

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="显示要返回 TLS 版本的 log analytics 查询结果的屏幕截图":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>按调用方 IP 地址和用户代理标头查询记录的请求

Azure Monitor 中的 Azure 存储日志还包括调用方 IP 地址和用户代理标头，以帮助你评估哪些客户端应用程序访问了存储帐户。 你可以分析这些值，以确定是否必须将客户端应用程序更新为使用较新的 TLS 版本，或者是否可以在客户端的请求未使用最低 TLS 版本发送的情况下使其失败。

若要检索过去7天的日志并确定哪些客户端使用 TLS 1.2 之前的 TLS 版本发出请求，请将以下查询粘贴到新的日志查询中并运行。 请记住，用自己的值替换括号中的占位符值：

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>配置帐户的最低 TLS 版本

若要为存储帐户配置最低 TLS 版本，请使用 Azure 门户或 Azure CLI 设置该帐户的**minimumTlsVersion**版本。 此属性适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 有关详细信息，请参阅[存储帐户概述](storage-account-overview.md)。

# <a name="portal"></a>[Portal](#tab/portal)

若要为具有 Azure 门户的存储帐户配置最低 TLS 版本，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 选择**配置**设置。
1. 在 "**最低 tls 版本**" 下，使用下拉选择访问此存储帐户中的数据所需的最低 tls 版本，如下图所示。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="显示如何在 Azure 门户中配置 TLS 的最低版本的屏幕截图":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要为具有 Azure CLI 的存储帐户配置最小 TLS 版本，请先调用[az resource show](/cli/azure/resource#az-resource-show)命令获取存储帐户的资源 ID。 接下来，调用[az resource update](/cli/azure/resource#az-resource-update)命令设置存储帐户的**minimumTlsVersion**属性。 **MinimumTlsVersion**的有效值为 `TLS1_0` 、 `TLS1_1` 和 `TLS1_2` 。

下面的示例将最小 TLS 版本设置为1.2。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> 更新存储帐户的最低 TLS 版本后，最多可能需要30秒才能完全传播更改。

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>检查帐户所需的最低 TLS 版本

若要确定为存储帐户配置的最低所需 TLS 版本，请检查 Azure 资源管理器**minimumTlsVersion**属性。 若要同时检查大量存储帐户的此属性，请使用 Azure 资源图资源管理器。

默认情况下， **minimumTlsVersion**属性不会设置，并且不会返回值，除非你显式设置该值。 如果属性值为 null，存储帐户将默认为允许以 TLS 版本1.0 或更高版本发送的请求。

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>检查单个存储帐户所需的最低 TLS 版本

若要使用 Azure CLI 检查单个存储帐户所需的最小 TLS 版本，请调用[az resource show](/cli/azure/resource#az-resource-show)命令并查询**minimumTlsVersion**属性：

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>检查一组存储帐户所需的最小 TLS 版本

若要在具有最佳性能的一组存储帐户中检查所需的最低 TLS 版本，可以使用 Azure 门户中的 Azure 资源图资源管理器。 若要了解有关使用资源图资源管理器的详细信息，请参阅[快速入门：使用 Azure 资源关系图资源管理器运行第一个资源图形查询](/azure/governance/resource-graph/first-query-portal)。

在资源图资源管理器中运行以下查询将返回存储帐户的列表，并显示每个帐户的最低 TLS 版本：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>测试客户端的最低 TLS 版本

若要测试存储帐户所需的最小 TLS 版本是否禁止使用较早版本发出的调用，可以将客户端配置为使用较早版本的 TLS。 有关将客户端配置为使用特定 TLS 版本的详细信息，请参阅[配置客户端应用程序 (TLS) 的传输层安全性](transport-layer-security-configure-client-version.md)。

当客户端使用不满足为帐户配置的最低 TLS 版本的 TLS 版本访问存储帐户时，Azure 存储将返回错误代码400错误 (错误请求) ，并返回一条消息，指示不允许对此存储帐户发出请求。

## <a name="next-steps"></a>后续步骤

- [为客户端应用程序配置 (TLS) 的传输层安全性](transport-layer-security-configure-client-version.md)
- [适用于 Blob 存储的安全建议](../blobs/security-recommendations.md)
