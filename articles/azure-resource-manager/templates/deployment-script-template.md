---
title: 在模板中使用部署脚本 | Microsoft Docs
description: 使用 Azure 资源管理器模板中的部署脚本。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: jgao
ms.openlocfilehash: 4094e610bb290fc11656dc192f3d0a495f679dc5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291807"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>在模板中使用部署脚本（预览版）

了解如何使用 Azure 资源模板中的部署脚本。 使用名为 `Microsoft.Resources/deploymentScripts` 的新资源类型，用户可以在模板部署中执行部署脚本并查看执行结果。 这些脚本可用于执行自定义步骤，如：

- 将用户添加到目录
- 执行数据平面操作，例如，复制 blob 或种子数据库
- 查找并验证许可证密钥
- 创建自签名证书
- 在 Azure AD 中创建对象
- 从自定义系统中查找 IP 地址块

部署脚本的优势：

- 易于编码、使用和调试。 你可以在最喜欢的开发环境中开发部署脚本。 脚本可以嵌入模板或外部脚本文件中。
- 可以指定脚本语言和平台。 当前，支持 Linux 环境上的 Azure PowerShell 和 Azure CLI 部署脚本。
- 允许指定用于执行脚本的标识。 当前，仅支持 [Azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。
- 允许将命令行参数传递给脚本。
- 可以指定脚本输出，并将其传递回部署。

部署脚本资源仅在 Azure 容器实例可用的区域中可用。  请参阅 [Azure 容器实例在 Azure 区域的资源可用性](../../container-instances/container-instances-region-availability.md)。

> [!IMPORTANT]
> 脚本执行和故障排除需要一个存储帐户和一个容器实例。 可以选择指定现有存储帐户，如果不指定，脚本服务会自动创建存储帐户和容器实例。 当部署脚本执行达到某个最终状态时，脚本服务通常会删除这两个自动创建的资源。 在这些资源删除之前，这些资源会一直向你收费。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="prerequisites"></a>先决条件

- 具有目标资源组的参与者角色的用户分配的托管标识****。 此标识用来执行部署脚本。 若要在资源组之外执行操作，需要授予其他权限。 例如，如果要创建新的资源组，请在订阅级别分配标识。

  > [!NOTE]
  > 脚本服务将在后台创建一个存储帐户（除非指定一个现有的存储帐户）和一个容器实例。  如果订阅尚未注册 Azure 存储帐户 (Microsoft.Storage) 和 Azure 容器实例 (Microsoft.ContainerInstance) 资源提供程序，则需要在订阅级别具有参与者角色的用户分配的托管标识。

  若要创建标识，请参阅[使用 Azure 门户创建用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)或[使用 Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 或[使用 Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。 部署模板时需要此标识 ID。 标识符的格式为：

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  使用以下 CLI 或 PowerShell 脚本，通过提供资源组名称和标识名称来获取 ID。

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- Azure PowerShell 或 Azure CLI**** ****。 请参阅支持的[Azure PowerShell 版本](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)的列表。 请参阅支持的[Azure CLI 版本](https://mcr.microsoft.com/v2/azure-cli/tags/list)的列表。

    >[!IMPORTANT]
    > 部署脚本使用 Microsoft 容器注册表 (MCR) 中可用的 CLI 映像。 认证部署脚本的 CLI 映像大约需要一个月的时间。 不要使用 30 天内发布的 CLI 版本。 若要查找映像的发行日期，请参阅 [Azure CLI 发行说明](/cli/azure/release-notes-azure-cli?view=azure-cli-latest)。 如果使用了不受支持的版本，错误消息中会列出受支持的版本。

    部署模板时不需要这些版本。 但在本地测试部署脚本时需要这些版本。 请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 可以使用预配置的 Docker 映像。  请参阅[配置开发环境](#configure-development-environment)。

## <a name="sample-templates"></a>示例模板

下面的 json 是一个示例。  最新模板架构可在[此处](/azure/templates/microsoft.resources/deploymentscripts)找到。

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> 此示例用于演示目的。  scriptContent 和 primaryScriptUri 不能共存于模板中**** ****。

属性值详细信息：

- **标识**：部署脚本服务使用用户分配的托管标识来执行脚本。 当前，仅支持用户分配的托管标识。
- **kind**：指定脚本类型。 当前，支持 Azure PowerShell 和 Azure CLI 脚本。 值为 AzurePowerShell 和 AzureCLI**** ****。
- **forceUpdateTag**：在模板部署之间更改此值将强制重新执行部署脚本。 使用需要设置为参数的 defaultValue 的 newGuid() 或 utcNow() 函数。 若要了解详细信息，请参阅[多次运行脚本](#run-script-more-than-once)。
- **containerSettings**：指定该设置，用于自定义 Azure 容器实例。  containerGroupName 用于指定容器组名称****。  如果未指定，则将自动生成组名称。
- **storageAccountSettings**：指定设置以使用现有的存储帐户。 如果未指定，将自动创建存储帐户。 请参阅[使用现有的存储帐户](#use-existing-storage-account)。
- **azPowerShellVersion**/**azCliVersion**：指定要使用的模块版本。 有关支持的 PowerShell 和 CLI 版本的列表，请参阅[先决条件](#prerequisites)。
- **arguments**：指定参数值。 请以空格分隔这些值。

    部署脚本通过调用[CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)系统调用将参数拆分为字符串数组。 这是必需的，因为参数作为[命令属性](/rest/api/container-instances/containergroups/createorupdate#containerexec)传递到 Azure 容器实例，而 command 属性是字符串数组。

    如果参数包含转义字符，请使用[JsonEscaper](https://www.jsonescaper.com/)对字符进行双重转义。 将原始的转义字符串粘贴到该工具中，然后选择 "**转义**"。  该工具输出一个双转义字符串。 例如，在前面的示例模板中，参数为 **-name \\ "John Dole \\ "**。  转义字符串的**名称为 \\ \\ \\ "John dole \\ \\ \\ "**。

    若要将 object 类型的 ARM 模板参数作为参数传递，请使用[string （）](./template-functions-string.md#string)函数将该对象转换为字符串，然后使用[replace （）](./template-functions-string.md#replace)函数替换任何** \\ "** into ** \\ \\ \\ "**。 例如：

    ```json
    replace(string(parameters('tables')), '\"', '\\\"')
    ```

    若要查看示例模板，请[在此处](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)选择。

- **environmentVariables**：指定环境变量以传递到脚本。 有关详细信息，请参阅[开发部署脚本](#develop-deployment-scripts)。
- **scriptContent**：指定脚本内容。 若要运行外部脚本，请改用 `primaryScriptUri`。 有关示例，请参阅[使用内联脚本](#use-inline-scripts)和[使用外部脚本](#use-external-scripts)。
- **primaryScriptUri**：为具有受支持的文件扩展名的主部署脚本指定一个可公开访问的 Url。
- **supportingScriptUris**：指定一个可公开访问的 Url 数组，它们指向在 `ScriptContent` 或 `PrimaryScriptUri` 中调用的支持性文件。
- **timeout**：指定 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)中指定的脚本执行最大允许时间。 默认值为 **P1D**。
- **cleanupPreference**。 指定在脚本执行达到最终状态时清理部署资源的首选项。 默认设置为 Always，这意味着不管最终状态如何（成功、失败、已取消），都会删除资源****。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。
- **retentionInterval**：指定在部署脚本执行达到最终状态后，服务保留部署脚本资源的时间间隔。 在此持续时间到期时，将删除部署脚本资源。 持续时间基于 [ISO 8601 模式](https://en.wikipedia.org/wiki/ISO_8601)。 默认值为**P1D**，这表示一天。 当 cleanupPreference 设置为 OnExpiration 时使用此属性**。 当前未启用 OnExpiration 属性**。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

### <a name="additional-samples"></a>其他示例

- [示例 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)：创建密钥保管库并使用部署脚本将证书分配到密钥保管库。
- [示例 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)：在订阅级别创建资源组，在资源组中创建密钥保管库，然后使用部署脚本将证书分配到密钥保管库。
- [示例 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)：创建用户分配的托管标识，将参与者角色分配给资源组级别的标识，创建密钥保管库，然后使用部署脚本将证书分配到密钥保管库。

> [!NOTE]
> 建议提前创建用户分配的标识并授予权限。 如果在运行部署脚本的同一模板中创建标识并授予权限，则可能会出现与登录和权限相关的错误。 权限生效需要一段时间。

## <a name="use-inline-scripts"></a>使用内联脚本

以下模板具有一个使用 `Microsoft.Resources/deploymentScripts` 类型定义的资源。 突出显示的部分是内联脚本。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> 由于内联部署脚本是用双引号括起来的，因此部署脚本内的字符串需要使用 **&#92;** 进行转义或者使用单引号括起来。 还可以考虑使用字符串替换，如先前的 JSON 示例所示。

该脚本采用一个参数，并输出参数值。 DeploymentScriptOutputs 用于存储输出****。  在输出部分，值行显示了如何访问存储的值****。 `Write-Output` 用于调试目的。 若要了解如何访问输出文件，请参阅[部署脚本的监视和故障排除](#monitor-and-troubleshoot-deployment-scripts)。  有关属性说明，请参阅[示例模板](#sample-templates)。

若要运行脚本，请选择“尝试”以打开 Cloud Shell，然后将以下代码粘贴到 Shell 窗格中。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

输出如下所示：

![资源管理器模板部署脚本 hello world 输出](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>使用外部脚本

除了内联脚本以外，还可以使用外部脚本文件。 仅支持文件扩展名为 ps1 的主 PowerShell 脚本****。 对于 CLI 脚本，主脚本可以具有任何扩展名（或没有扩展名），只要这些脚本是有效的 bash 脚本。 若要使用外部脚本文件，请将 `scriptContent` 替换为 `primaryScriptUri`。 例如：

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

若要查看示例，请选择[此处](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)。

外部脚本文件必须是可访问的。  若要保护存储在 Azure 存储帐户中的脚本文件，请参阅[使用 SAS 令牌部署专用 ARM 模板](./secure-template-with-sas-token.md)。

你负责确保部署脚本（PrimaryScriptUri 或 SupportingScriptUris）所引用的脚本的完整性**** ****。  仅引用你信任的脚本。

## <a name="use-supporting-scripts"></a>使用支持脚本

可以将复杂的逻辑分成一个或多个支持脚本文件。 使用 `supportingScriptURI` 属性可根据需要向支持脚本文件提供 URI 数组：

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

可从内联脚本和主脚本文件中调用支持脚本文件。 支持脚本文件对文件扩展名没有限制。

支持文件在运行时复制到 azscripts/azscriptinput。 使用相对路径从内联脚本和主脚本文件中引用支持文件。

## <a name="work-with-outputs-from-powershell-script"></a>处理 PowerShell 脚本的输出

以下模板显示了如何在两个 DeploymentScripts 资源之间传递值：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

在第一个资源中，定义一个名为 $DeploymentScriptOutputs 的变量，并使用它来存储输出值****。 若要访问模板内另一个资源的输出值，请使用：

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>使用 CLI 脚本的输出

与 PowerShell 部署脚本不同，CLI/bash 支持不通过公开常见变量来存储脚本输出，而使用名为 AZ_SCRIPTS_OUTPUT_PATH 的环境变量来存储脚本输出文件所在的位置****。 如果从资源管理器模板运行部署脚本，则 Bash shell 会自动设置此环境变量。

部署脚本输出必须保存在 AZ_SCRIPTS_OUTPUT_PATH 位置，并且输出必须是有效的 JSON 字符串对象。 必须将该文件的内容保存为键值对。 例如，字符串数组存储为 { "MyResult": [ "foo", "bar"] }。  仅存储数组结果是无效的，例如 [ "foo", "bar" ]。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

在先前的示例中使用了 [jq](https://stedolan.github.io/jq/)。 它带有容器映像。 请参阅[配置开发环境](#configure-development-environment)。

## <a name="use-existing-storage-account"></a>使用现有存储帐户

脚本执行和故障排除需要一个存储帐户和一个容器实例。 可以选择指定现有存储帐户，如果不指定，脚本服务会自动创建存储帐户和容器实例。 使用现有存储帐户的要求：

- 支持的存储帐户类型如下：

    | SKU             | 支持的类型     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage、StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage、StorageV2 |
    | Standard_RAGRS  | Storage、StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    这些组合支持文件共享。  有关详细信息，请参阅[创建 Azure 文件共享](../../storage/files/storage-how-to-create-file-share.md)和[存储帐户类型](../../storage/common/storage-account-overview.md)。
- 尚不支持存储帐户防火墙规则。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md)。
- 部署脚本的用户分配的托管标识必须具有管理存储帐户的权限，包括读取、创建和删除文件共享。

若要指定现有存储帐户，请将以下 json 添加到 `Microsoft.Resources/deploymentScripts` 的属性元素中：

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**：指定存储帐户的名称。
- **storageAccountKey**：指定存储帐户密钥之一。 可以使用 [`listKeys()`](./template-functions-resource.md#listkeys) 函数检索密钥。 例如：

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

有关完整的 `Microsoft.Resources/deploymentScripts` 定义示例，请参阅[示例模板](#sample-templates)。

使用现有存储帐户时，脚本服务将创建一个具有唯一名称的文件共享。 有关脚本服务清理文件共享的方式，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="develop-deployment-scripts"></a>开发部署脚本

### <a name="handle-non-terminating-errors"></a>处理非终止错误

可通过在部署脚本中使用 $ErrorActionPreference 变量来控制 PowerShell 响应非终止错误的方式****。 如果未在部署脚本中设置该变量，则脚本服务将使用默认值“Continue”****。

不管为 $ErrorActionPreference 设置了什么值，当脚本遇到错误时，脚本服务会将资源预配状态设置为“失败”****。

### <a name="pass-secured-strings-to-deployment-script"></a>将安全字符串传递到部署脚本

通过在容器实例中设置环境变量 (EnvironmentVariable)，可为容器运行的应用程序或脚本提供动态配置。 部署脚本以与 Azure 容器实例相同的方式处理非安全和安全环境变量。 有关详细信息，请参阅[在容器实例中设置环境变量](../../container-instances/container-instances-environment-variables.md#secure-values)。

环境变量允许的最大大小为 64KB。

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>监视部署脚本并排除其故障

脚本服务将创建一个[存储帐户](../../storage/common/storage-account-overview.md)（除非指定一个现有的存储帐户）和一个[容器实例](../../container-instances/container-instances-overview.md)以执行脚本。 如果这些资源是由脚本服务自动创建的，则两个资源的资源名称后缀均为 azscripts****。

![资源管理器模板部署脚本资源名称](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

用户脚本、执行结果和 stdout 文件存储在存储帐户的文件共享中。 存在名为 azscripts 的文件夹****。 在该文件夹中，还有两个用于输入文件和输出文件的文件夹：azscriptinput 和 azscriptoutput**** ****。

输出文件夹包含 **executionresult.json** 和脚本输出文件。 可以在 executionresult.json 中看到脚本执行错误消息****。 仅当成功执行脚本时，才会创建输出文件。 输入文件夹包含一个系统 PowerShell 脚本文件和一些用户部署脚本文件。 可以使用修订后的文件替换用户部署脚本文件，然后从 Azure 容器实例重新运行部署脚本。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

部署部署脚本资源后，资源会在 Azure 门户中的资源组下列出。 以下屏幕截图显示了部署脚本资源的 "概述" 页：

![资源管理器模板部署脚本门户概述](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

"概述" 页显示资源的一些重要信息，如**预配状态**、**存储帐户**、**容器实例**和**日志**。

从左侧菜单中，您可以查看部署脚本内容、传递给脚本的参数和输出。  您还可以导出包含部署脚本的部署脚本模板。

### <a name="use-powershell"></a>使用 PowerShell

使用 Azure PowerShell，你可以在订阅或资源组范围内管理部署脚本：

- [AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript)：获取或列出部署脚本。
- [AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog)：获取部署脚本执行的日志。
- [AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript)：删除部署脚本及其关联的资源。
- [AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog)：将部署脚本执行的日志保存到磁盘。

AzDeploymentScript 输出类似于：

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>使用 Azure CLI

使用 Azure CLI，你可以在订阅或资源组范围内管理部署脚本：

- [az 部署-脚本删除](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-delete)：删除部署脚本。
- [az deployment-scripts list](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-list)：列出所有部署脚本。
- [az deployment-script show](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show)：检索部署脚本。
- [az deployment-script show-log](/cli/azure/deployment-scripts?view=azure-cli-latest#az-deployment-scripts-show-log)：显示部署脚本日志。

List 命令输出类似于：

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>使用 Rest API

可以使用 REST API 在资源组级别和订阅级别获取部署脚本资源部署信息：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

下面的示例使用 [ARMClient](https://github.com/projectkudu/ARMClient)：

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

输出类似于：

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

以下 REST API 返回日志：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

它仅在删除部署脚本资源之前有效。

要在门户中查看 deploymentScripts 资源，请选择“显示隐藏的类型”****：

![资源管理器模板部署脚本、显示隐藏的类型、门户](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>清理部署脚本资源

脚本执行和故障排除需要一个存储帐户和一个容器实例。 可以选择指定现有存储帐户，否则脚本服务会自动创建存储帐户和容器实例。 当部署脚本执行达到某个最终状态时，脚本服务会删除这两个自动创建的资源。 在这些资源删除之前，这些资源会一直向你收费。 有关定价信息，请参阅[容器实例定价](https://azure.microsoft.com/pricing/details/container-instances/)和 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

这些资源的生命周期由模板中的以下属性控制：

- **cleanupPreference**：当脚本执行达到最终状态时清理首选项。 支持的值包括：

  - **Always**：脚本执行达到最终状态后，将删除自动创建的资源。 如果使用现有存储帐户，脚本服务将删除在存储帐户中创建的文件共享。 由于在清理资源后，deploymentScripts 资源可能仍然存在，因此在删除资源之前，脚本服务将保留脚本执行结果，例如 stdout、输出、返回值等。
  - **OnSuccess**：仅当脚本执行成功时才删除自动创建的资源。 如果使用现有存储帐户，则脚本服务仅在脚本执行成功时才删除文件共享。 你仍可以访问资源来查找调试信息。
  - **OnExpiration**：仅当**retentionInterval**设置为 "已过期" 时才删除自动创建的资源。 如果使用现有存储帐户，脚本服务将删除文件共享，但保留存储帐户。

- **retentionInterval**：指定将保留脚本资源的时间间隔，超过此时间间隔后，脚本资源将过期并被删除。

> [!NOTE]
> 不建议将脚本服务生成的存储帐户和容器实例用于其他目的。 根据脚本的生命周期，可能会删除这两个资源。

## <a name="run-script-more-than-once"></a>多次运行脚本

部署脚本执行操作是一个幂等操作。 如果未更改 deploymentScripts 资源属性（包括内联脚本），则在重新部署模板时将不会执行该脚本。 部署脚本服务将模板中的资源名称与同一资源组中的现有资源进行比较。 如果要多次执行相同的部署脚本，有两个选项可供选择：

- 更改 deploymentScripts 资源的名称。 例如，使用 [utcNow](./template-functions-date.md#utcnow) 模板函数作为资源名称或资源名称的一部分。 更改资源名称将创建一个新的 deploymentScripts 资源。 这对于保留脚本执行历史记录很有帮助。

    > [!NOTE]
    > utcNow 函数只能在参数的默认值中使用。

- 在 `forceUpdateTag` 模板属性中指定其他值。  例如，使用 utcNow 作为值。

> [!NOTE]
> 编写幂等的部署脚本。 这样可以确保即使它们再次意外运行，也不会引起系统更改。 例如，如果使用部署脚本创建 Azure 资源，请在创建资源之前验证该资源确实不存在，以便脚本成功执行，否则不必再次创建该资源。

## <a name="configure-development-environment"></a>配置开发环境

你可以使用预配置的容器映像作为部署脚本开发环境。 有关详细信息，请参阅为[模板中的部署脚本配置开发环境](./deployment-script-template-configure-dev.md)。

脚本成功测试后，可以将其作为模板中的部署脚本使用。

## <a name="deployment-script-error-codes"></a>部署脚本错误代码

| 错误代码 | 说明 |
|------------|-------------|
| DeploymentScriptInvalidOperation | 模板中的部署脚本资源定义包含无效的属性名称。 |
| DeploymentScriptResourceConflict | 无法删除处于非终端状态且执行未超过1小时的部署脚本资源。 或者，无法同时用相同的资源标识符（相同的订阅、资源组名称和资源名称）重新运行相同的部署脚本，但不能同时执行不同的脚本正文内容。 |
| DeploymentScriptOperationFailed | 部署脚本操作在内部失败。 请联系 Microsoft 支持部门。 |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | 尚未为现有存储帐户指定访问密钥。|
| DeploymentScriptContainerGroupContainsInvalidContainers | 由部署脚本服务创建的容器组已从外部修改，并添加了无效的容器。 |
| DeploymentScriptContainerGroupInNonterminalState | 两个或多个部署脚本资源在同一资源组中使用相同的 Azure 容器实例名称，其中一个资源尚未完成其执行。 |
| DeploymentScriptStorageAccountInvalidKind | BlobBlobStorage 或 BlobStorage 类型的现有存储帐户不支持文件共享，因此不能使用。 |
| DeploymentScriptStorageAccountInvalidKindAndSku | 现有的存储帐户不支持文件共享。 有关支持的存储帐户类型的列表，请参阅[使用现有存储帐户](#use-existing-storage-account)。 |
| DeploymentScriptStorageAccountNotFound | 存储帐户不存在或者已被外部进程或工具删除。 |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | 指定的存储帐户包含一个服务终结点。 不支持具有服务终结点的存储帐户。 |
| DeploymentScriptStorageAccountInvalidAccessKey | 为现有存储帐户指定的访问密钥无效。 |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | 存储帐户密钥格式无效。 请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。 |
| DeploymentScriptExceededMaxAllowedTime | 部署脚本执行时间超过了部署脚本资源定义中指定的超时值。 |
| DeploymentScriptInvalidOutputs | 部署脚本输出不是有效的 JSON 对象。 |
| DeploymentScriptContainerInstancesServiceLoginFailure | 用户分配的托管标识在10次尝试后无法登录，间隔为1分钟。 |
| DeploymentScriptContainerGroupNotFound | 由部署脚本服务创建的容器组已由外部工具或进程删除。 |
| DeploymentScriptDownloadFailure | 未能下载支持脚本。 请参阅[使用支持脚本](#use-supporting-scripts)。|
| DeploymentScriptError | 用户脚本出现错误。 |
| DeploymentScriptBootstrapScriptExecutionFailed | 启动脚本出现错误。 启动脚本是协调部署脚本执行的系统脚本。 |
| DeploymentScriptExecutionFailed | 执行部署脚本时出现未知错误。 |
| DeploymentScriptContainerInstancesServiceUnavailable | 创建 Azure 容器实例（ACI）时，ACI 引发了服务不可用错误。 |
| DeploymentScriptContainerGroupInNonterminalState | 当创建 Azure 容器实例（ACI）时，另一个部署脚本正在同一作用域（同一订阅、资源组名称和资源名称）中使用相同的 ACI 名称。 |
| DeploymentScriptContainerGroupNameInvalid | 指定的 Azure 容器实例名称（ACI）不符合 ACI 要求。 请参阅[排查 Azure 容器实例中的常见问题](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment)。|

## <a name="next-steps"></a>后续步骤

本教程已介绍部署脚本的用法。 完成部署脚本教程：

> [!div class="nextstepaction"]
> [教程：使用 Azure 资源管理器模板中的部署脚本](./template-tutorial-deployment-script.md)
