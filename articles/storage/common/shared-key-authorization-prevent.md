---
title: '通过共享密钥 (预览阻止授权) '
titleSuffix: Azure Storage
description: 若要要求客户端使用 Azure AD 来授权请求，你可以禁止请求使用共享密钥 (预览版) 授权的存储帐户。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 16080440a9458753992c62309ce75ed241fb64d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715120"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>阻止对 Azure 存储帐户进行共享密钥授权 (预览) 

必须授权对 Azure 存储帐户的每个安全请求。 默认情况下，可以使用 Azure Active Directory (Azure AD) 凭据或使用共享密钥授权的帐户访问密钥对请求进行授权。 在这两种类型的授权中，Azure AD 通过共享密钥提供高级安全和易用性，并由 Microsoft 推荐。 若要要求客户端使用 Azure AD 来授权请求，你可以禁止请求使用共享密钥 (预览版) 授权的存储帐户。

如果你不允许对存储帐户进行共享密钥授权，Azure 存储将拒绝对该帐户的所有后续请求，这些请求使用帐户访问密钥获得授权。 仅授权有 Azure AD 的安全请求将会成功。 有关使用 Azure AD 的详细信息，请参阅 [使用 Azure Active Directory 授予对 blob 和队列的访问权限](storage-auth-aad.md)。

> [!WARNING]
> Azure 存储支持 Azure AD 仅向 Blob 和队列存储请求授权。 如果你不允许对存储帐户使用共享密钥授权，则使用共享密钥授权的 Azure 文件或表存储的请求将失败。
>
> 在预览期间，不允许使用共享密钥授权时，对使用共享访问签名的 Azure 文件或表存储的请求 (SAS) 使用帐户访问密钥生成的令牌将会成功。 有关详细信息，请参阅 [关于预览](#about-the-preview)。
>
> 禁止对存储帐户进行共享密钥访问不会影响与 Azure 文件的 SMB 连接。
>
> Microsoft 建议你在禁止通过共享密钥访问帐户之前将任何 Azure 文件或表存储数据迁移到单独的存储帐户，或者不将此设置应用于支持 Azure 文件或表存储工作负荷的存储帐户。

本文介绍如何检测通过共享密钥授权发送的请求，以及如何修正存储帐户的共享密钥授权。 若要了解如何注册预览版，请参阅 [关于预览](#about-the-preview)。

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>检测客户端应用程序使用的授权类型

如果你不允许对存储帐户进行共享密钥授权，则从使用帐户访问密钥进行共享密钥授权的客户端发出的请求将会失败。 若要了解在进行此更改之前禁用共享密钥授权可能会对客户端应用程序产生何种影响，请启用存储帐户的日志记录和指标。 然后，你可以在一段时间内分析你的帐户的请求模式，以确定请求的授权方式。

使用度量值来确定存储帐户接收的、使用共享密钥或共享访问签名授权的请求数)  (SAS 的请求数。 使用日志来确定发送这些请求的客户端。

有关在预览过程中使用共享访问签名来解释请求的详细信息，请参阅 [关于预览](#about-the-preview)。

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>监视通过共享密钥授权的请求数

若要跟踪对存储帐户请求的授权方式，请使用 Azure 门户中的 Azure 指标资源管理器。 有关指标资源管理器的详细信息，请参阅 [Azure 指标资源管理器](../../azure-monitor/platform/metrics-getting-started.md)入门。

按照以下步骤创建一个度量值，用于跟踪使用共享密钥或 SAS 发出的请求：

1. 导航到 Azure 门户中的存储帐户。 在 " **监视** " 部分下，选择 " **指标**"。
1. 选择“添加指标”。 在 " **指标** " 对话框中，指定以下值：
    1. 将 " **作用域** " 字段设置为存储帐户的名称。
    1. 将 **指标命名空间** 设置为 " *帐户*"。 此指标将报告针对存储帐户的所有请求。
    1. 将 " **指标** " 字段设置为 " *事务*"。
    1. 将 **聚合** 字段设置为 *Sum*。

    新度量值将显示在给定时间间隔内针对存储帐户的事务数之和。 生成的指标显示如下图所示：

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="显示如何将指标配置为与共享密钥或 SAS 进行求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为授权类型的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** " 字段设置为等号 (=) 。
    1. 在 " **值** " 字段中，选择 " *帐户密钥* 和 *SAS*"。
1. 在右上角，选择要查看指标的时间范围。 还可以通过指定从1分钟到1个月之间的时间间隔，来指示请求聚合的粒度。 例如，将 **时间范围** 设置为30天，将 **时间粒度** 设置为1天，以查看在过去30天内按天聚合的请求。

配置指标后，将开始在图形上显示对存储帐户的请求。 下图显示了使用共享密钥授权的请求，或使用 SAS 令牌发出的请求。 请求在过去的30天内按天聚合。

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="显示如何将指标配置为与共享密钥或 SAS 进行求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为授权类型的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** ":::

你还可以配置警报规则，以便在使用共享密钥授权的特定数量的请求针对你的存储帐户发出请求时通知你。 有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../../azure-monitor/platform/alerts-metric.md)。

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>分析日志以识别向共享密钥或 SAS 请求的客户端

Azure 存储日志捕获有关针对存储帐户发出的请求的详细信息，包括请求的授权方式。 你可以通过分析日志来确定哪些客户端使用共享密钥或 SAS 令牌对请求进行授权。

若要将请求记录到 Azure 存储帐户，以便评估这些请求的授权方式，你可以使用 Azure 存储日志记录 Azure Monitor (预览 ") "。 有关详细信息，请参阅 [监视 Azure 存储](../common/monitor-storage.md)。

Azure Monitor 中的 Azure 存储日志记录支持使用日志查询来分析日志数据。 若要查询日志，可以使用 Azure Log Analytics 工作区。 若要了解有关日志查询的详细信息，请参阅 [教程： Log Analytics 查询入门](../../azure-monitor/log-query/get-started-portal.md)。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>在 Azure 门户中创建诊断设置

若要使用 Azure Monitor 记录 Azure 存储数据并使用 Azure Log Analytics 对其进行分析，必须首先创建诊断设置，以指示要为其记录数据的请求类型和存储服务。 若要在 Azure 门户中创建诊断设置，请执行以下步骤：

1. [在 Azure Monitor 预览中注册 Azure 存储日志记录](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。
1. 在订阅中创建一个包含你的 Azure 存储帐户的新 Log Analytics 工作区，或使用现有 Log Analytics 工作区。 配置存储帐户的日志记录后，日志将在 "Log Analytics" 工作区中可用。 有关详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。
1. 导航到 Azure 门户中的存储帐户。
1. 在 "监视" 部分，选择 " **诊断设置" (预览 ") **。
1. 选择要为其记录请求的 Azure 存储服务。 例如，选择 " **blob** " 将请求记录到 blob 存储中。
1. 选择“添加诊断设置”。
1. 提供诊断设置的名称。
1. 在 " **类别详细信息**" 下的 " **日志** " 部分中，选择 " **StorageRead**"、" **StorageWrite**" 和 " **StorageDelete** "，将所有数据请求记录到所选服务。
1. 在 " **目标详细信息**" 下，选择 " **发送到 Log Analytics**"。 选择之前创建的 "订阅" 和 "Log Analytics" 工作区，如下图所示。

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="显示如何将指标配置为与共享密钥或 SAS 进行求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为授权类型的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** ":::

可以在存储帐户中为每种类型的 Azure 存储资源创建诊断设置。

创建诊断设置后，随后会根据该设置记录对存储帐户的请求。 有关详细信息，请参阅 [创建诊断设置以收集 Azure 中的资源日志和指标](../../azure-monitor/platform/diagnostic-settings.md)。

有关 Azure Monitor 中的 Azure 存储日志中可用的字段的参考，请参阅 [资源日志 (预览版) ](../common/monitor-storage-reference.md#resource-logs-preview)。

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>通过共享密钥或 SAS 发出请求的查询日志

Azure Monitor 中的 Azure 存储日志包含用于向存储帐户发出请求的授权类型。 若要检索在过去7天内通过共享密钥或 SAS 授权发出的请求的日志，请打开 Log Analytics 工作区。 接下来，将以下查询粘贴到新的日志查询中，并运行该查询。 此查询显示通过共享密钥或 SAS 授权的最常发送的请求数的10个 IP 地址：

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

你还可以根据此查询配置警报规则，以通知你使用共享密钥或 SAS 授权的请求。 有关详细信息，请参阅 [使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/platform/alerts-log.md)。

## <a name="remediate-authorization-via-shared-key"></a>通过共享密钥修正授权

在分析了对存储帐户请求的授权方式之后，你可以采取措施阻止通过共享密钥进行访问。 但首先，你需要更新使用共享密钥授权的所有应用程序，以便改用 Azure AD。 可以按照 [检测客户端应用程序使用的授权类型](#detect-the-type-of-authorization-used-by-client-applications) 跟踪转换中所述来监视日志和指标。 有关将 Azure AD 用于 blob 和队列数据的详细信息，请参阅 [使用 Azure Active Directory 授予对 blob 和队列的访问权限](storage-auth-aad.md)。

当你确信可以安全拒绝通过共享密钥授权的请求时，可以将存储帐户的 **AllowSharedKeyAccess** 属性设置为 **false**。

默认情况下， **AllowSharedKeyAccess** 属性不会设置，并且不会返回值，除非你显式设置该值。 当属性值为 **null** 或为 **true**时，存储帐户允许通过共享密钥授权的请求。

> [!WARNING]
> 如果任何客户端当前正在使用共享密钥访问存储帐户中的数据，则 Microsoft 建议你先将这些客户端迁移到 Azure AD，然后再对存储帐户禁用共享密钥访问。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要为 Azure 门户中的存储帐户禁用共享密钥授权，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 找到 "**设置**" 下的**配置**设置。
1. 将 " **允许共享密钥访问** " 设置为 " **已禁用**"。

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="显示如何将指标配置为与共享密钥或 SAS 进行求和的屏幕截图&quot;:::

1. 接下来，选择 &quot; **添加筛选器** &quot; 按钮，为授权类型的指标创建筛选器。
1. 在 &quot; **筛选器** &quot; 对话框中，指定以下值：
    1. 将 **属性** 值设置为 *Authentication*。
    1. 将 &quot; **运算符** ":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要为具有 Azure CLI 的存储帐户禁用共享密钥授权，请安装 Azure CLI 2.9.1 或更高版本。 有关详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 接下来，为新的或现有的存储帐户配置 **allowSharedKeyAccess** 属性。

下面的示例演示如何设置 **allowSharedKeyAccess** 属性和 Azure CLI。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

禁用共享密钥授权后，使用共享密钥授权向存储帐户发出请求将失败，错误代码为 403 (禁止访问) 。 Azure 存储将返回错误，指示不允许在存储帐户上使用基于密钥的授权。

### <a name="verify-that-shared-key-access-is-not-allowed"></a>验证是否不允许使用共享密钥访问权限

若要验证是否不再允许使用共享密钥授权，可以尝试使用帐户访问密钥调用数据操作。 下面的示例尝试使用访问密钥创建容器。 当不允许对存储帐户进行共享密钥授权时，此调用将失败。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> 如果已将存储帐户和容器配置为匿名公共读取访问，则匿名请求未获授权，并将继续。 有关详细信息，请参阅 [配置容器和 blob 的匿名公共读取访问](../blobs/anonymous-read-access-configure.md)。

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>检查多个帐户的共享密钥访问设置

若要检查具有最佳性能的一组存储帐户中的共享密钥访问设置，可以使用 Azure 门户中的 Azure 资源图资源管理器。 若要了解有关使用资源图资源管理器的详细信息，请参阅 [快速入门：使用 Azure 资源关系图资源管理器运行第一个资源图形查询](/azure/governance/resource-graph/first-query-portal)。

在资源图资源管理器中运行以下查询将返回存储帐户的列表，并显示每个帐户的共享密钥访问设置：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>了解禁止共享密钥如何影响 SAS 令牌

如果存储帐户不允许使用共享密钥，Azure 存储会根据 SAS 类型和请求的目标服务来处理 SAS 令牌。 下表显示了每种类型的 SAS 的授权方式，以及当存储帐户的 **AllowSharedKeyAccess** 属性为 **False**时，Azure 存储将如何处理该 sas。

| SAS 类型 | 授权类型 | AllowSharedKeyAccess 为 false 时的行为 |
|-|-|-|
| 仅 (Blob 存储的用户委托 SAS)  | Azure AD | 允许请求。 Microsoft 建议尽可能使用用户委托 SAS，以实现高级安全性。 |
| 服务 SAS | 共享密钥 | 拒绝对 Blob 存储的请求。 请求允许用于队列和表存储以及 Azure 文件。 有关详细信息，请参阅**关于预览**部分中的[AllowSharedKeyAccess 为 false 中的队列、表和文件允许使用 SAS 令牌的请求](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false)。 |
| 帐户 SAS | 共享密钥 | 拒绝对 Blob 存储的请求。 请求允许用于队列和表存储以及 Azure 文件。 有关详细信息，请参阅**关于预览**部分中的[AllowSharedKeyAccess 为 false 中的队列、表和文件允许使用 SAS 令牌的请求](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false)。 |

有关共享访问签名的详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](storage-sas-overview.md)。

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>考虑与其他 Azure 工具和服务的兼容性

许多 Azure 服务使用共享密钥授权来与 Azure 存储进行通信。 如果你不允许对存储帐户进行共享密钥授权，则这些服务将无法访问该帐户中的数据，并且你的应用程序可能会受到负面影响。

某些 Azure 工具提供了使用 Azure AD 授权访问 Azure 存储的选项。 下表列出了一些常用的 Azure 工具，并说明了它们是否可以使用 Azure AD 向 Azure 存储授权请求。

| Azure 工具 | 向 Azure 存储 Azure AD 授权 |
|-|-|
| Azure 门户 | 支持。 有关使用 Azure AD 帐户从 Azure 门户进行授权的信息，请参阅 [选择如何授予对 Azure 门户中 blob 数据的访问权限](../blobs/authorize-blob-access-portal.md)。 |
| AzCopy | 支持 Blob 存储。 有关授权 AzCopy 操作的信息，请参阅 AzCopy 文档中的 [选择如何提供授权凭据](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) 。 |
| Azure 存储资源管理器 | 仅支持 Blob 存储和 Azure Data Lake Storage Gen2。 Azure AD 不支持对队列存储的访问。 请确保选择正确的 Azure AD 租户。 有关详细信息，请参阅 [存储资源管理器入门](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | 支持。 有关如何使用 Azure AD 为 blob 或队列操作授权 PowerShell 命令的信息，请参阅 [使用 Azure AD 凭据运行 powershell 命令以访问 blob 数据](../blobs/authorize-active-directory-powershell.md) 或 [使用 Azure AD 凭据运行 powershell 命令以访问队列数据](../queues/authorize-active-directory-powershell.md)。 |
| Azure CLI | 支持。 有关如何使用 Azure AD Azure CLI 命令来访问 blob 和队列数据的信息，请参阅 [使用 Azure AD 凭据运行 Azure CLI 命令以访问 blob 或队列数据](authorize-data-operations-cli.md)。 |
| Azure IoT 中心 | 支持。 有关详细信息，请参阅 [IoT 中心对虚拟网络的支持](../../iot-hub/virtual-network-support.md)。 |
| Azure Cloud Shell | Azure Cloud Shell 是 Azure 门户中的集成外壳。 Azure Cloud Shell 在存储帐户中的 Azure 文件共享中保存持久性的文件。 如果该存储帐户不允许进行共享密钥授权，则这些文件将无法访问。 有关详细信息，请参阅 [连接 Microsoft Azure 文件存储](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage)。 <br /><br /> 若要运行 Azure Cloud Shell 中的命令以管理不允许进行共享密钥访问的存储帐户，请先确保已通过 Azure 基于角色的访问控制向你授予这些帐户所需的权限 (Azure RBAC) 。 有关详细信息，请参阅 [什么是 AZURE RBAC) 的 azure 基于角色的访问控制 (？](../../role-based-access-control/overview.md)。 |

## <a name="about-the-preview"></a>关于此预览版

Azure 公有云中提供禁止使用共享密钥授权的预览。 仅支持使用 Azure 资源管理器部署模型的存储帐户。 有关哪些存储帐户使用 Azure 资源管理器部署模型的信息，请参阅 [存储帐户的类型](storage-account-overview.md#types-of-storage-accounts)。

若要注册预览版，请参阅 [Azure 存储允许共享密钥访问有限的公共预览版](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u)。

> [!IMPORTANT]
> 此预览版仅用于非生产用途。

预览版包括以下各节所述的限制。

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>指标和日志记录报告使用 SAS 发出的所有请求，而不考虑它们的授权方式

Azure 指标和日志记录 Azure Monitor 不区分预览版中不同类型的共享访问签名。 Azure 中的 **sas** 筛选器指标资源管理器和 azure 存储中的 **sas** 字段，以 Azure Monitor 通过任何类型的 SAS 授权的报表请求进行日志记录。 但是，不同类型的共享访问签名以不同的方式获得授权，并且当不允许共享密钥访问时，行为会有所不同：

- 当 **AllowSharedKeyAccess** 属性设置为 **false**时，服务 SAS 令牌或帐户 Sas 令牌使用共享密钥授权，并将不允许对 Blob 存储的请求使用。
- 向 **AllowSharedKeyAccess** 属性设置为 **false**时，向 Blob 存储的请求 Azure AD 授予了用户委托 SAS。

评估到存储帐户的流量时，请记住 " [检测客户端应用程序使用的授权类型"](#detect-the-type-of-authorization-used-by-client-applications) 中所述的指标和日志可能包括使用用户委托 SAS 发出的请求。 有关在 **AllowSharedKeyAccess** 属性设置为 **False**时 AZURE 存储如何响应 sas 的详细信息，请参阅 [了解如何禁用共享密钥会影响 sas 令牌](#understand-how-disallowing-shared-key-affects-sas-tokens)。

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>当 AllowSharedKeyAccess 为 false 时，允许对包含 SAS 令牌的请求使用队列、表和文件

如果在预览期间不允许对存储帐户进行共享密钥访问，则会继续允许目标为 "队列"、"表" 或 "Azure 文件" 资源的共享访问签名。 此限制适用于服务 SAS 令牌和帐户 SAS 令牌。 这两种类型的 SAS 都是通过共享密钥授权的。

## <a name="next-steps"></a>后续步骤

- [授予访问 Azure 存储中的数据的权限](storage-auth.md)
- [使用 Azure Active Directory 授予对 Blob 和队列的访问权限](storage-auth-aad.md)
- [通过共享密钥进行授权](/rest/api/storageservices/authorize-with-shared-key)