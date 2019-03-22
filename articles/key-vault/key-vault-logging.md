---
title: Azure 密钥保管库日志记录 |Microsoft Docs
description: 借助本教程开始使用 Azure 密钥保管库日志记录。
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: afec42551f124890dd2cc7b03cce48c359fc88c4
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194089"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 日志记录

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

创建一个或多个密钥保管库后，你可能需要监视密钥保管库如何以及何时将访问，以及由谁。 可以通过启用 Azure 密钥保管库，它在你提供的 Azure 存储帐户中保存的信息的日志记录来执行此操作。 名为的新容器**insights 日志 auditevent**自动创建为指定的存储帐户。 用于收集有关多个密钥保管库日志，可以使用此相同的存储帐户。

您可以访问日志记录信息 10 分钟 （至少） 在密钥保管库操作之后。 但大多数情况下不用等待这么长时间。  存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。

借助本教程开始使用 Azure 密钥保管库日志记录。 将创建存储帐户、 启用日志记录，并解释收集的日志信息。  

> [!NOTE]
> 本教程不包含有关如何创建密钥保管库、密钥或机密的说明。 有关信息，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)。 或者，有关跨平台 Azure CLI 的说明，请参阅[此对应教程](key-vault-manage-with-cli2.md)。
>
> 本文提供了用于更新诊断日志记录的 Azure PowerShell 说明。 此外可以通过使用 Azure Monitor 中更新诊断日志记录**诊断日志**Azure 门户的一部分。 
>

有关密钥保管库的概述信息，请参阅[什么是 Azure 密钥保管库？](key-vault-whatis.md)。 有关密钥保管库是可用的信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="prerequisites"></a>必备组件

要完成本教程，必须准备好以下各项：

* 正在使用的现有密钥保管库。  
* Azure PowerShell，最低版本为 1.0.0。 要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 如果已安装 Azure PowerShell 并不知道版本，请从 Azure PowerShell 控制台中，输入`$PSVersionTable.PSVersion`。  
* 足够的 Azure 存储用于保存密钥保管库日志。

## <a id="connect"></a>连接到你的密钥保管库的订阅

设置密钥的日志记录的第一步是为想要记录的密钥保管库进行点 Azure PowerShell。

使用以下命令启动 Azure PowerShell 会话，并登录 Azure 帐户：  

```PowerShell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 Azure PowerShell 获取与此帐户关联的所有订阅。 默认情况下，PowerShell 使用第一个。

您可能需要指定用来创建密钥保管库的订阅。 输入以下命令以查看你的帐户的订阅：

```PowerShell
Get-AzSubscription
```

然后，若要指定与您将日志记录在密钥保管库相关联的订阅，请输入：

```PowerShell
Set-AzContext -SubscriptionId <subscription ID>
```

PowerShell 指向正确的订阅是一个重要步骤，尤其是如果你有多个订阅与帐户关联。 有关配置 Azure PowerShell 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a id="storage"></a>创建存储帐户日志

虽然可以为日志使用现有的存储帐户，我们将创建将专用于密钥保管库日志的存储帐户。 为方便起见，我们必须指定此值更高版本，我们将在名为的变量中存储详细信息**sa**。

为进一步简化管理，我们还将使用相同的资源组作为一个包含密钥保管库。 从[快速入门教程](key-vault-get-started.md)，此资源组名为**ContosoResourceGroup**，我们将继续使用亚洲东部位置。 这些值将替换为自己，（如果适用）：

```PowerShell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> 如果您决定使用现有的存储帐户，它必须与密钥保管库使用同一订阅。 并且它必须使用 Azure 资源管理器部署模型中，而不是经典部署模型。
>
>

## <a id="identify"></a>标识用于保存日志的密钥保管库

在中[快速入门教程](key-vault-get-started.md)，密钥保管库名称为**ContosoKeyVault**。 我们将继续使用该名称，并将详细信息存储在名为**kv**:

```PowerShell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>启用日志记录

若要启用的密钥保管库日志记录，我们将使用**集 AzDiagnosticSetting** cmdlet，同时我们为新的存储帐户和密钥保管库创建的变量。 我们还会设置 **-启用**标记，用于 **$true**并将类别设置为**AuditEvent** （密钥保管库日志记录的唯一类别）：

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

输出如下所示：

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

此输出确认密钥保管库，现在启用日志记录，而它会将信息保存到你的存储帐户。

（可选） 可以为你的日志设置保留策略，以便自动删除较旧的日志。 例如，通过设置来设置保留策略 **-RetentionEnabled**标记，用于 **$true**，并设置 **-RetentionInDays**参数**90**，以便自动删除早于 90 天的日志。

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

记录的内容：

* 所有经过身份验证 REST API 请求，包括由于访问权限、 系统错误或错误请求而失败的请求。
* 操作针对密钥保管库本身，包括创建、 删除、 设置密钥保管库访问策略，以及更新密钥保管库属性，例如标记。
* 对密钥保管库中密钥和机密的操作包括：
  * 创建、 修改或删除这些密钥或机密。
  * 签名、 验证、 加密、 解密、 包装和解包密钥，获取机密，并列出密钥和机密 （和其版本）。
* 导致出现 401 响应的未经身份验证的请求。 示例包括的请求不包含持有者令牌、 格式不正确或已过期，或包含无效的令牌。  

## <a id="access"></a>访问日志

密钥保管库日志存储在**insights 日志 auditevent**你提供的存储帐户中的容器。 若要查看日志，你必须下载 blob。

首先，请为容器名称创建一个变量。 你将使用此变量完成本演练的剩余部分。

```PowerShell
$container = 'insights-logs-auditevent'
```

若要列出此容器中的所有 blob，请输入：

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

输出与下面类似：

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

可从此输出中看出，blob 遵循以下命名约定：`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日期和时间值使用 UTC。

因为您可以使用相同的存储帐户收集多个资源的日志，blob 名称中的完整资源 ID 可用于访问或下载所需的 blob。 但在这样做之前，让我们先了解如何下载所有 Blob。

创建一个文件夹用于下载 blob。 例如：

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

然后获取所有 blob 的列表：  

```PowerShell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

通过此列表通过管道传递**Get AzStorageBlobContent**若要将 blob 下载到目标文件夹：

```PowerShell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

运行第二个命令时，blob 名称中的 / 分隔符会在目标文件夹下创建完整的文件夹结构。 此结构将用于下载并存储为文件的 blob。

若要选择性地下载 Blob，请使用通配符。 例如：

* 如果有多个密钥保管库，并只想要下载其中名为 CONTOSOKEYVAULT3 的密钥保管库的日志：

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 如果有多个资源组，并只想要下载其中某个资源组的日志，请使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 如果你想要下载适用于 2019 年 1 月的月份的所有日志，使用`-Blob '*/year=2019/m=01/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

现在已准备就绪，可以开始查看日志中的内容。 但在我们继续与之前，您应该知道两个命令的详细信息：

* 若要查询密钥保管库资源的诊断设置状态：`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* 若要禁用密钥保管库资源的日志记录： `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>解释 Key Vault 日志

每个 Blob 存储为文本，并格式化为 JSON Blob。 让我们看看一个示例日志条目。 运行以下命令：

```PowerShell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

它会返回日志条目与以下类似：

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

下表列出的字段名称和说明：

| 字段名 | 描述 |
| --- | --- |
| **time** |日期和时间采用 UTC。 |
| **resourceId** |Azure 资源管理器资源 ID。 对于密钥保管库日志而言，这始终是密钥保管库资源 ID。 |
| **operationName** |下一份表格中所述操作的名称。 |
| **operationVersion** |客户端请求的 REST API 版本。 |
| **category** |结果的类型。 对于密钥保管库日志**AuditEvent**是单一、 可用值。 |
| **resultType** |REST API 请求的结果。 |
| **resultSignature** |HTTP 状态。 |
| **resultDescription** |有关结果的其他描述（如果有）。 |
| **durationMs** |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| **callerIpAddress** |发出请求的客户端的 IP 地址。 |
| **correlationId** |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端（密钥保管库）日志相关联。 |
| **identity** |从所提供的 REST API 请求的令牌的标识。 这通常是"user"，"服务主体"，或组合"用户 + 应用程序 Id，"请求，请求从 Azure PowerShell cmdlet 一样。 |
| **properties** |各不相同的信息基于的操作 (**operationName**)。 在大多数情况下，此字段包含客户端信息 （客户端所传递的用户代理字符串）、 确切的 REST API 请求 URI 和 HTTP 状态代码。 此外，对象返回请求的结果 (例如， **KeyCreate**或**VaultGet**)，它还包含密钥 URI （作为"id")，保管库 URI 或机密 URI。 |

**OperationName**字段值是否在*ObjectVerb*格式。 例如：

* 所有密钥保管库操作采用`Vault<action>`格式，例如`VaultGet`和`VaultCreate`。
* 所有密钥操作采用`Key<action>`格式，例如`KeySign`和`KeyList`。
* 所有机密操作采用`Secret<action>`格式，例如`SecretGet`和`SecretListVersions`。

下表列出**operationName**值和对应的 REST API 命令：

| operationName | REST API 命令 |
| --- | --- |
| **身份验证** |通过 Azure Active Directory 终结点进行身份验证 |
| **VaultGet** |[获取有关密钥保管库的信息](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[创建或更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[删除密钥保管库](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[列出资源组中的所有密钥保管库](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[创建密钥](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[获取有关密钥的信息](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[将密钥导入保管库](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[备份密钥](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[删除密钥](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[还原密钥](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[使用密钥签名](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[使用密钥验证](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[包装密钥](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[解包密钥](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[使用密钥加密](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[使用密钥解密](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[更新密钥](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[列出保管库中的密钥](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[列出密钥的版本](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[创建机密](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[获取机密](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[更新机密](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[删除机密](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[列出保管库中的机密](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[列出机密的版本](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>使用 Azure Monitor 日志

可以使用 Azure Monitor 日志中的密钥保管库解决方案来查看 Key Vault **AuditEvent**日志。 在 Azure Monitor 日志使用日志查询来分析数据并获取所需的信息。 

有关详细信息，包括如何进行此设置，请参阅[Azure Monitor 日志中的 Azure 密钥保管库解决方案](../azure-monitor/insights/azure-key-vault.md)。 这篇文章还包含说明，如果您需要从预览，请先路由到 Azure 存储帐户日志和配置的 Azure Monitor 日志，以从此处读取，在 Azure Monitor 日志期间提供的旧 Key Vault 解决方案迁移。

## <a id="next"></a>后续步骤

.NET web 应用程序中使用 Azure 密钥保管库的教程，请参阅[从 web 应用程序使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。

有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)。

Azure 密钥保管库的 Azure PowerShell 1.0 cmdlet 列表，请参阅[Azure 密钥保管库 cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)。

密钥轮换和与 Azure 密钥保管库审核日志的教程，请参阅[使用端到端密钥轮替和审核设置密钥保管库](key-vault-key-rotation-log-monitoring.md)。
