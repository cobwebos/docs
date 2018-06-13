---
title: Azure 密钥保管库日志记录 | Microsoft Docs
description: 借助本教程开始使用 Azure 密钥保管库日志记录。
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/16/2017
ms.author: barclayn
ms.openlocfilehash: e7dcb3778de31258f4aa3c946ffa214d87cb858a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32178817"
---
# <a name="azure-key-vault-logging"></a>Azure 密钥保管库日志记录
在大多数区域中提供了 Azure 密钥保管库。 有关详细信息，请参阅 [密钥保管库定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="introduction"></a>介绍
在创建一个或多个密钥保管库之后，可能需要监视密钥保管库的访问方式、时间和访问者。 为此，可以启用密钥保管库日志记录，以便在提供的 Azure 存储帐户中保存信息。 系统自动指定的存储帐户创建了名为 **insights-logs-auditevent** 的新容器，可以使用同一个存储帐户来收集多个密钥保管库的日志。

最多在执行密钥保管库操作 10 分钟后，就能访问其日志记录信息。 但大多数情况下不用等待这么长时间。  存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。

请借助本教程开始使用 Azure 密钥保管库日志记录，并创建存储帐户，启用日志记录，以及解释收集到的日志信息。  

> [!NOTE]
> 本教程不包含有关如何创建密钥保管库、密钥或机密的说明。 有关这方面的信息，请参阅 [Get started with Azure Key Vault](key-vault-get-started.md)（Azure 密钥保管库入门）。 或者，有关跨平台命令行接口说明，请参阅 [此对应教程](key-vault-manage-with-cli2.md)。
>
> 目前，无法在 Azure 门户中配置 Azure 密钥保管库。 请改用这些 Azure PowerShell 说明。
>
>

有关 Azure 密钥保管库的概述信息，请参阅 [什么是 Azure 密钥保管库？](key-vault-whatis.md)

## <a name="prerequisites"></a>先决条件
要完成本教程，必须准备好以下各项：

* 正在使用的现有密钥保管库。  
* Azure PowerShell， **最低版本为 1.0.1**。 要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 如果已安装了 Azure PowerShell，但不知道版本，请在 Azure PowerShell 控制台中键入 `(Get-Module azure -ListAvailable).Version`。  
* 足够的 Azure 存储用于保存密钥保管库日志。

## <a id="connect"></a>连接到订阅
启动 Azure PowerShell 会话，并使用以下命令登录 Azure 帐户：  

    Connect-AzureRmAccount

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 Azure PowerShell 会获取与此帐户关联的所有订阅，并按默认使用第一个订阅。

如果有多个订阅，可能需要指定用来创建 Azure 密钥保管库的特定订阅。 键入以下命令查看帐户的订阅：

    Get-AzureRmSubscription

然后，键入以下命令以指定与要记录的密钥保管库关联的订阅：

    Set-AzureRmContext -SubscriptionId <subscription ID>

> [!NOTE]
> 这是很重要的一步，在有多个订阅与帐户相关联的情况下特别有用。 如果跳过此步骤，则在注册 Microsoft.Insights 时可能会出错。
>   
>

有关配置 Azure PowerShell 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a id="storage"></a>为日志创建新的存储帐户
尽管可以使用现有的存储帐户来保存日志，但我们将专门创建一个新的存储帐户来保存密钥保管库日志。 为方便起见，在稍后遇到必须指定此帐户的情况时，我们会将详细信息存储到名为 **sa**的变量中。

为了进一步简化管理，我们还使用了包含密钥保管库的同一个资源组。 在 [入门教程](key-vault-get-started.md)中，此资源组的名称为 **ContosoResourceGroup**，我们将继续使用“东亚”位置。 请根据情况将这些值替换成自己的值：

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> 如果决定使用现有存储帐户，则必须使用与密钥保管库相同的订阅，此外还必须使用 Resource Manager 部署模型而不是经典部署模型。
>
>

## <a id="identify"></a>标识用于保存日志的密钥保管库
在入门教程中，密钥保管库名为 **ContosoKeyVault**，因此继续使用该名称，并将详细信息存储到名为 **kv** 的变量中：

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>启用日志记录
为了启用密钥保管库日志记录，我们将使用 Set-AzureRmDiagnosticSetting cmdlet 并配合针对新存储帐户和密钥保管库创建的变量。 还将 **-Enabled** 标志设置为 **$true**，并将类别设置为 AuditEvent（密钥保管库日志记录的唯一类别）：

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

此命令的输出包含：

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


此输出确认密钥保管库日志记录现已启用，系统会将信息保存到存储帐户。

还可以选择性地为日志设置保留期策略，以便自动删除较旧的日志。 例如，使用 **-RetentionEnabled** 标志将保留期策略设置为 **$true**，并将 **-RetentionInDays** 参数设置为 **90**，以便自动删除 90 天以上的日志。

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

记录的内容：

* 记录所有已经过身份验证的 REST API 请求，包括由于访问权限、系统错误或错误请求而发生的失败请求。
* 对密钥保管库本身执行的操作，包括创建、删除、设置密钥保管库访问策略，以及更新密钥保管库属性（例如标记）。
* 对密钥保管库中的密钥和机密执行的操作，包括创建、修改或删除这些密钥或机密，以及签名、验证、加密、解密、包装和解包密钥、获取机密、列出密钥和机密及其版本。
* 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。  

## <a id="access"></a>访问日志
密钥保管库日志存储在提供的存储帐户的 **insights-logs-auditevent** 容器中。 若要列出此容器中的所有 Blob，请键入：

首先，请为容器名称创建一个变量。 该变量会在余下的演练中全程使用。

    $container = 'insights-logs-auditevent'

若要列出此容器中的所有 Blob，请键入：

    Get-AzureStorageBlob -Container $container -Context $sa.Context
输出如下所示：

**容器 URI：https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Name**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

从此输出中可以看到，blob 遵循以下命名约定：**resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

日期和时间值使用 UTC。

由于可以使用相同的存储帐户来收集多个资源的日志，Blob 名称中的完整资源 ID 很适合用于访问或下载所需 Blob。 但在这样做之前，让我们先了解如何下载所有 Blob。

首先，创建一个文件夹用于下载 Blob。 例如：

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

然后获取所有 blob 的列表：  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

通过 'Get-AzureStorageBlobContent' 以管道传送此列表，将 Blob 下载到目标文件夹：

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

运行第二个命令时，Blob 名称中的 **/** 分隔符会在目标文件夹下创建完整文件夹结构，此结构用于下载 Blob 并将其存储为文件。

若要选择性地下载 Blob，请使用通配符。 例如：

* 如果有多个密钥保管库，并只想要下载其中名为 CONTOSOKEYVAULT3 的密钥保管库的日志：

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* 如果有多个资源组，并只想要下载其中某个资源组的日志，请使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* 如果要下载 2016 年 1 月份的所有日志，请使用 `-Blob '*/year=2016/m=01/*'`：

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

现在已准备就绪，可以开始查看日志中的内容。 但在开始之前，可能还需要了解 Get-AzureRmDiagnosticSetting 的另外两个参数：

* 若要查询密钥保管库资源的诊断设置状态：`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* 若要禁用密钥保管库资源的日志记录： `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>解释 Key Vault 日志
每个 Blob 存储为文本，并格式化为 JSON Blob。 以下是运行 `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`后得到的示例日志条目：

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


下表列出了字段的名称和描述。

| 字段名称 | 说明 |
| --- | --- |
| time |日期和时间 (UTC)。 |
| resourceId |Azure 资源管理器资源 ID。 对于密钥保管库日志，这始终是密钥保管库资源 ID。 |
| operationName |下一份表格中所述操作的名称。 |
| operationVersion |客户端请求的 REST API 版本。 |
| category |对于密钥保管库日志而言，AuditEvent 是唯一可用值。 |
| resultType |REST API 请求的结果。 |
| resultSignature |HTTP 状态。 |
| resultDescription |有关结果的其他描述（如果有）。 |
| durationMs |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| callerIpAddress |发出请求的客户端的 IP 地址。 |
| correlationId |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端（密钥保管库）日志相关联。 |
| identity |发出 REST API 请求时提供的令牌中的标识。 与通过 Azure PowerShell cmdlet 发出请求一样，这通常是“用户”、“服务主体”，或者“用户+应用程序 ID”的组合。 |
| 属性 |此字段根据操作 (operationName) 包含不同的信息。 在大多数情况下，包含客户端信息（客户端传递的 useragent 字符串）、确切的 REST API 请求 URI 和 HTTP 状态代码。 此外，在根据请求（例如，KeyCreate 或 VaultGet）返回对象时，此字段还将包含密钥 URI（“id”形式）、保管库 URI 或机密 URI。 |

**operationName** 字段值采用 ObjectVerb 格式。 例如：

* 所有密钥保管库操作采用“Vault`<action>`”格式，例如 `VaultGet` 和 `VaultCreate`。
* 所有密钥操作采用“Key`<action>`”格式，例如 `KeySign` 和 `KeyList`。
* 所有机密操作采用“Secret`<action>`”格式，例如 `SecretGet` 和 `SecretListVersions`。

下表列出了 operationName 和对应的 REST API 命令。

| operationName | REST API 命令 |
| --- | --- |
| 身份验证 |通过 Azure Active Directory 终结点 |
| VaultGet |[获取有关密钥保管库的信息](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| VaultPut |[创建或更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultDelete |[删除密钥保管库](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| VaultPatch |[更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[列出资源组中的所有密钥保管库](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| KeyCreate |[创建密钥](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| KeyGet |[获取有关密钥的信息](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| KeyImport |[将密钥导入保管库](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| KeyBackup |[备份密钥](https://msdn.microsoft.com/library/azure/dn878058.aspx)。 |
| KeyDelete |[删除密钥](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| KeyRestore |[还原密钥](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| KeySign |[使用密钥签名](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| KeyVerify |[使用密钥验证](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| KeyWrap |[包装密钥](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| KeyUnwrap |[解包密钥](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| KeyEncrypt |[使用密钥加密](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| KeyDecrypt |[使用密钥解密](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| KeyUpdate |[更新密钥](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| KeyList |[列出保管库中的密钥](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| KeyListVersions |[列出密钥的版本](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| SecretSet |[创建机密](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| SecretGet |[获取机密](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| SecretUpdate |[更新机密](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| SecretDelete |[删除机密](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| SecretList |[列出保管库中的机密](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| SecretListVersions |[列出机密的版本](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>使用 Log Analytics

可以在 Log Analytics 中使用 Azure 密钥保管库解决方案来查看 Azure 密钥保管库 AuditEvent 日志。 有关详细信息，包括如何进行设置，请参阅 [Log Analytics 中的Azure Key Vault 解决方案](../log-analytics/log-analytics-azure-key-vault.md)。 如果需要从 Log Analytics 预览版提供的旧 Key Vault 解决方案进行迁移，且之前在该方案中，首先将日志路由到了 Azure 存储帐户，并将 Log Analytics 配置为了从此处读取，则本文也可提供指导。

## <a id="next"></a>后续步骤
有关在 Web 应用程序中使用 Azure 密钥保管库的教程，请参阅 [从 Web 应用程序使用 Azure 密钥保管库](key-vault-use-from-web-application.md)。

有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)。

有关 Azure 密钥保管库的 Azure PowerShell 1.0 cmdlet 列表，请参阅 [Azure 密钥保管库 Cmdlet](/powershell/module/azurerm.keyvault/#key_vault)。

有关使用 Azure 密钥保管库进行密钥轮替和日志审核的教程，请参阅 [How to setup Key Vault with end to end key rotation and auditing](key-vault-key-rotation-log-monitoring.md)（如何使用端到端密钥轮替和审核设置密钥保管库）。
