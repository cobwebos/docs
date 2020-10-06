---
title: 如何启用 Azure Key Vault 日志记录
description: 如何为 Azure Key Vault 启用日志记录，这会在提供的 Azure 存储帐户中保存信息。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 8a975673bec3b3579eaa699f873fe8c4b1481d38
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744743"
---
# <a name="how-to-enable-key-vault-logging"></a>如何启用 Key Vault 日志记录

在创建一个或多个 Key Vault 之后，可能需要监视 Key Vault 的访问方式、时间和访问者。 有关该功能的完整详细信息，请参阅 [Key Vault 日志记录](logging.md)。

## <a name="prerequisites"></a>先决条件

要完成本教程，必须满足下列要求：

* 正在使用的现有密钥保管库。  
* Azure CLI 或 Azure PowerShell。
* 足够的 Azure 存储用于保存密钥保管库日志。

如果选择在本地安装并使用 CLI，将需要 Azure CLI 版本2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 若要使用 CLI 登录到 Azure，可以键入：

```azurecli-interactive
az login
```

如果选择在本地安装并使用 PowerShell，则需要 Azure PowerShell 模块版本1.0.0 或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

```powershell-interactive
Connect-AzAccount
```

## <a name="connect-to-your-key-vault-subscription"></a>连接到 Key Vault 订阅

设置密钥日志记录的第一步是连接到包含密钥保管库的订阅。 如果有多个订阅与帐户相关联，则这一点尤其重要。

使用 Azure CLI，可以使用 [az account list](/cli/azure/account?view=azure-cli-latest#az_account_list) 命令查看所有订阅，然后使用 [az account set](/cli/azure/account?view=azure-cli-latest#az_account_set)连接到其中：

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

使用 Azure PowerShell，你可以先使用 [AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) cmdlet 列出你的订阅，然后使用 [AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) cmdlet 连接到该订阅： 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>为日志创建存储帐户

尽管可以使用现有的存储帐户来存储日志，但我们会创建一个专用于 Key Vault 日志的新存储帐户。 

为了进一步简化管理，我们还使用了包含 Key Vault 的同一个资源组。 在 [Azure CLI 快速入门](quick-create-cli.md) 和 [Azure PowerShell 快速入门](quick-create-powershell.md)中，此资源组名为 **myResourceGroup**，并且位置为 *eastus*。 将这些值替换为您自己的值（如果适用）。 

我们还需要提供存储帐户名称。 存储帐户名称必须唯一，长度为3到24个字符，并且只能使用数字和小写字母。  最后，我们将创建 "Standard_LRS" SKU 的存储帐户。

使用 Azure CLI，使用 [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) 命令。

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

使用 Azure PowerShell，请使用 [AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) cmdlet。 你将需要提供与资源组相对应的位置。

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

在任一情况下，记下存储帐户的 "id"。 Azure CLI 操作返回输出中的 "id"。 若要获取 Azure PowerShell 的 "id"，请使用 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) 并将输出分配给变量 $sa。 然后，你可以看到具有 $sa id 的存储帐户。 ("$sa。还将使用上下文 "属性，请参阅本文后面的。 ) 

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

存储帐户的 "id" 的格式为 "/subscriptions/<订阅 ID>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<唯一的存储帐户名称>"。

> [!NOTE]
> 如果决定使用现有存储帐户，则该帐户必须使用与密钥保管库相同的订阅，并且必须使用 Azure 资源管理器部署模型，而不是经典部署模型。

## <a name="obtain-your-key-vault-resource-id"></a>获取密钥保管库资源 ID

在 [CLI 快速入门](quick-create-cli.md) 和 [PowerShell 快速入门](quick-create-powershell.md)中，已创建了一个具有唯一名称的键。  请在下面的步骤中再次使用该名称。  如果你不记得密钥保管库的名称，可以使用 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) 命令 Azure PowerShell 或 [AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) cmdlet 来列出它们。

使用密钥保管库的名称查找其资源 ID。  使用 Azure CLI，使用 [az keyvault show](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show) 命令。

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

使用 Azure PowerShell，请使用 [AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) cmdlet。

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

Key vault 的资源 ID 的格式为 "/subscriptions/<订阅 ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<唯一 KeyVault 名称>"。 请注意，在下一步。

## <a name="enable-logging-using-azure-powershell"></a>使用 Azure PowerShell 启用日志记录

若要为 Key Vault 启用日志记录，我们将使用 Azure CLI [az monitor 诊断设置 create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) 命令，或使用 [AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) CMDLET 以及存储帐户 ID 和密钥保管库资源 ID。

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

在 Azure PowerShell 中，我们将使用 [AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) cmdlet，并将 " **启用** " 标志设置为 " **$true** "，并将 "类别" 设置为 " `AuditEvent` (Key Vault 日志记录的唯一类别) ：

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

（可选）可以为日志设置保留策略，以便在指定的时间后自动删除较旧的日志。 例如，可以设置自动删除超过90天的日志的设置保留策略。

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

使用 Azure PowerShell，请使用 [AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) cmdlet。 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

记录的内容：

* 所有已经过身份验证的 REST API 请求，包括由于访问权限、系统错误或错误请求而发生的失败请求。
* 对 Key Vault 本身执行的操作，包括创建、删除、设置 Key Vault 访问策略，以及更新 Key Vault 属性（例如标记）。
* 对 Key Vault 中的密钥和机密执行的操作，包括：
  * 创建、修改或删除这些密钥或机密。
  * 签名、验证、加密、解密、包装和解包密钥、获取机密、列出密钥和机密（及其版本）。
* 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。  
* 即将过期、已过期和保管库访问策略已更改的事件网格通知事件（不记录新版本事件）。 无论是否在密钥保管库上创建了事件订阅，都会记录事件。 有关详细信息，请参阅 [Key Vault 的事件网格事件架构](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault)

## <a name="access-your-logs"></a>访问日志

Key Vault 日志存储在所提供的存储帐户的 "auditevent" 容器中。 若要查看这些日志，必须下载 Blob。

首先，列出容器中的所有 blob。  使用 Azure CLI，使用 [az storage blob list](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list) 命令。

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

使用 Azure PowerShell，使用 [AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) 列出此容器中的所有 blob，并输入：

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

正如你将从 Azure CLI 命令或 Azure PowerShell cmdlet 的输出中看到的那样，blob 的名称采用格式 `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` 。 日期和时间值使用 UTC。

由于可以使用相同的存储帐户来收集多个资源的日志，Blob 名称中的完整资源 ID 适合用于仅访问或下载所需 Blob。 但在这样做之前，让我们先了解如何下载所有 Blob。

使用 Azure CLI，使用 [az storage blob 下载](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download) 命令将 blob 的名称和路径传递到要保存结果的文件的路径。

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

使用 Azure PowerShell，使用 [AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) cmdlet 获取 blob 列表，然后通过管道将该列表下载到 [AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) cmdlet，以将日志下载到所选路径。

```powershell-interactive
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

在 PowerShell 中运行第二个 cmdlet 时， **/** blob 名称中的分隔符会在目标文件夹下创建完整的文件夹结构。 你将使用此结构下载 Blob 并将其存储为文件。

若要选择性地下载 Blob，请使用通配符。 例如：

* 如果有多个密钥保管库，并只想要下载其中名为 CONTOSOKEYVAULT3 的密钥保管库的日志：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 如果有多个资源组，并只想要下载其中某个资源组的日志，请使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 如果要下载 2019 年 1 月份的所有日志，请使用 `-Blob '*/year=2019/m=01/*'`：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

现在已准备就绪，可以开始查看日志中的内容。 但在开始之前，应该了解另外两个命令：

有关如何读取日志的详细信息，请参阅 [Key Vault 日志记录：解释 Key Vault 日志](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>使用 Azure Monitor 日志

可以使用 Azure Monitor 日志中的 Key Vault 解决方案查看 Key Vault `AuditEvent` 日志。 在 Azure Monitor 日志中，可以使用日志查询来分析数据并获取所需的信息。

有关详细信息，包括如何进行设置，请参阅 [Azure Monitor 中的 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="next-steps"></a>后续步骤

- 有关概念性信息，包括如何解释 Key Vault 日志，请参阅 [Key Vault 日志记录](logging.md)
- 有关在 .NET Web 应用程序中使用 Azure Key Vault 的教程，请参阅[从 Web 应用程序使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。
- 有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](developers-guide.md)。