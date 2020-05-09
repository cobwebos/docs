---
title: 在模板中使用部署脚本 |Microsoft Docs
description: 使用 Azure 资源管理器模板中的部署脚本。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: jgao
ms.openlocfilehash: 5b938e2072daec56261e529ab8a2a8b15b55d143
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872331"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>在模板中使用部署脚本（预览）

了解如何使用 Azure 资源模板中的部署脚本。 使用名`Microsoft.Resources/deploymentScripts`为的新资源类型，用户可以在模板部署中执行部署脚本并查看执行结果。 这些脚本可用于执行自定义步骤，如：

- 将用户添加到目录
- 执行数据平面操作，例如，复制 blob 或种子数据库
- 查找并验证许可证密钥
- 创建自签名证书
- 在 Azure AD 中创建对象
- 查找自定义系统中的 IP 地址块

部署脚本的优点：

- 易于编码、使用和调试。 你可以在最喜欢的开发环境中开发部署脚本。 脚本可以嵌入在模板或外部脚本文件中。
- 可以指定脚本语言和平台。 目前，支持 Linux 环境中的 Azure PowerShell 和 Azure CLI 部署脚本。
- 允许指定用于执行脚本的标识。 目前仅支持[Azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。
- 允许将命令行参数传递给脚本。
- 可以指定脚本输出，并将其传递回部署。

部署脚本资源仅在 Azure 容器实例可用的区域中可用。  请参阅[azure 区域中 Azure 容器实例的资源可用性](../../container-instances/container-instances-region-availability.md)。

> [!IMPORTANT]
> 脚本执行和故障排除需要一个存储帐户和一个容器实例。 你可以选择指定现有的存储帐户，否则脚本服务会自动创建存储帐户和容器实例。 在部署脚本以终端状态执行时，脚本服务通常会删除两个自动创建的资源。 在这些资源删除之前，这些资源会一直向你收费。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="prerequisites"></a>先决条件

- **用户分配的托管标识，其中包含参与者的角色到目标资源组**。 此标识用来执行部署脚本。 若要在资源组外部执行操作，需要授予其他权限。 例如，如果要创建新的资源组，请将该标识分配到订阅级别。

  > [!NOTE]
  > 脚本服务将创建一个存储帐户（除非指定现有存储帐户），并在后台创建一个容器实例。  如果订阅尚未注册 Azure 存储帐户（Microsoft 存储）和 Azure 容器实例（ContainerInstance）资源提供程序，则需要一个用户分配的托管标识，其中包含参与者在订阅级别的角色。

  若要创建标识，请参阅[使用 Azure 门户创建用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)，或通过使用[Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)或[Azure PowerShell 使用](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。 部署模板时需要此标识 ID。 标识符的格式为：

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
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell**或**Azure CLI**。 有关受支持的 Azure PowerShell 版本的列表，请参阅[此处](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list);有关受支持的 Azure CLI 版本的列表，请参阅[此处](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)。

    >[!IMPORTANT]
    > 部署脚本使用 Microsoft 容器注册表（MCR）中的可用 CLI 映像。 需要约一个月来验证用于部署脚本的 CLI 映像。 不要使用在30天内发布的 CLI 版本。 若要查找图像的发行日期，请参阅[Azure CLI 发行说明](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)。 如果使用了不支持的版本，错误消息会列出受支持的版本。

    不需要这些版本来部署模板。 但在本地测试部署脚本需要这些版本。 请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 可以使用预配置的 Docker 映像。  请参阅[配置开发环境](#configure-development-environment)。

## <a name="sample-templates"></a>示例模板

下面的 json 是一个示例。  可在[此处](/azure/templates/microsoft.resources/deploymentscripts)找到最新的模板架构。

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
    "arguments": "[concat('-name ', parameters('name'))]",
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
> 示例用于演示目的。  **scriptContent**和**primaryScriptUris**不能共存于模板中。

属性值详细信息：

- **标识**：部署脚本服务使用用户分配的托管标识来执行脚本。 目前仅支持用户分配的托管标识。
- **kind**：指定脚本的类型。 目前，Azure PowerShell 和 Azure CLI 脚本都是支持的。 值为**AzurePowerShell**和**AzureCLI**。
- **forceUpdateTag**：在模板部署之间更改此值会强制重新执行部署脚本。 使用需要设置为参数的 defaultValue 的 newGuid （）或 utcNow （）函数。 若要了解详细信息，请参阅[多次运行脚本](#run-script-more-than-once)。
- **containerSettings**：指定用于自定义 Azure 容器实例的设置。  **containerGroupName**用于指定容器组名称。  如果未指定，则将自动生成组名称。
- **storageAccountSettings**：指定用于使用现有存储帐户的设置。 如果未指定，则会自动创建存储帐户。 请参阅[使用现有的存储帐户](#use-an-existing-storage-account)。
- **azPowerShellVersion**/**azCliVersion**：指定要使用的模块版本。 有关支持的 PowerShell 和 CLI 版本的列表，请参阅[先决条件](#prerequisites)。
- **arguments**：指定参数值。 请以空格分隔这些值。
- **environmentVariables**：指定要传递到脚本的环境变量。 有关详细信息，请参阅[开发部署脚本](#develop-deployment-scripts)。
- **scriptContent**：指定脚本内容。 若要运行外部脚本，请`primaryScriptUri`改用。 有关示例，请参阅[使用内联脚本](#use-inline-scripts)和[使用外部脚本](#use-external-scripts)。
- **primaryScriptUri**：使用受支持的文件扩展名指定可公开访问的主部署脚本 Url。
- **supportingScriptUris**：指定一个可公开访问的 url 数组，以支持在`ScriptContent`或`PrimaryScriptUri`中调用的支持文件。
- **timeout**：指定以[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)指定的最大允许脚本执行时间。 默认值为 **P1D**。
- **cleanupPreference**。 指定在脚本执行处于终端状态时清理部署资源的首选项。 默认设置**始终**为，这意味着，即使在终端状态（成功、失败、已取消）的情况下也删除资源。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。
- **retentionInterval**：指定服务在部署脚本执行达到终端状态后保留部署脚本资源的时间间隔。 此持续时间到期时，将删除部署脚本资源。 持续时间基于[ISO 8601 模式](https://en.wikipedia.org/wiki/ISO_8601)。 默认值为**P1D**，这意味着七天。 当 cleanupPreference 设置为 *OnExpiration* 时将使用此属性。 当前未启用*OnExpiration*属性。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

### <a name="additional-samples"></a>其他示例

- [创建证书并将其分配到密钥保管库](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [创建用户分配的托管标识并将其分配给资源组，并运行部署脚本](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)。

> [!NOTE]
> 建议创建用户分配的标识并提前授予权限。 如果在运行部署脚本的同一模板中创建标识并授予权限，可能会收到与登录和权限相关的错误。 需要一段时间才能使权限生效。

## <a name="use-inline-scripts"></a>使用内联脚本

以下模板有一个用`Microsoft.Resources/deploymentScripts`类型定义的资源。 突出显示的部分是内联脚本。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> 由于内联部署脚本是用双引号括起来的，因此部署脚本内的字符串需要改用单引号括起来。 PowerShell 的转义字符是 **&#92;**。 你还可以考虑使用字符串替换，如前面的 JSON 示例中所示。 请参阅 name 参数的默认值。

脚本采用一个参数，并输出参数值。 **DeploymentScriptOutputs**用于存储输出。  在 "输出" 部分中，"**值**" 行显示了如何访问存储的值。 `Write-Output`用于调试目的。 若要了解如何访问输出文件，请参阅[调试部署脚本](#debug-deployment-scripts)。  有关属性说明，请参阅[示例模板](#sample-templates)。

若要运行该脚本，请选择 "**尝试**" 打开 Cloud Shell，然后将以下代码粘贴到 "Shell" 窗格中。

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

除了内联脚本以外，还可以使用外部脚本文件。 仅支持具有**ps1**文件扩展名的主 PowerShell 脚本。 对于 CLI 脚本，只要脚本是有效的 bash 脚本，主脚本就可以有任何扩展名（或不带扩展名）。 若要使用外部脚本文件， `scriptContent`请`primaryScriptUri`将替换为。 例如：

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

若要查看示例，请[在此处](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)选择。

外部脚本文件必须是可访问的。  若要保护存储在 Azure 存储帐户中的脚本文件，请参阅[部署具有 SAS 令牌的专用 ARM 模板](./secure-template-with-sas-token.md)。

你负责确保部署脚本引用的脚本的完整性，无论是**PrimaryScriptUri**还是**SupportingScriptUris**。  仅引用你信任的脚本。

## <a name="use-supporting-scripts"></a>使用支持脚本

可以将复杂的逻辑分隔到一个或多个支持脚本文件中。 `supportingScriptURI`属性允许根据需要向支持脚本文件提供 uri 的数组：

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

支持脚本文件可以从内联脚本和主脚本文件中进行调用。 支持脚本文件对文件扩展名没有限制。

支持文件将复制到运行时的 azscripts/azscriptinput。 使用相对路径引用内联脚本和主脚本文件中的支持文件。

## <a name="work-with-outputs-from-powershell-script"></a>使用 PowerShell 脚本的输出

以下模板演示了如何在两个 deploymentScripts 资源之间传递值：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

在第一个资源中，可以定义一个名为 **$DeploymentScriptOutputs**的变量，并使用它来存储输出值。 若要访问模板内其他资源的输出值，请使用：

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>使用 CLI 脚本的输出

与 PowerShell 部署脚本不同，CLI/bash 支持不会公开公用变量来存储脚本输出，而是使用一个名为**AZ_SCRIPTS_OUTPUT_PATH**的环境变量来存储脚本输出文件所在的位置。 如果部署脚本是从资源管理器模板运行的，则 Bash shell 会自动设置此环境变量。

部署脚本输出必须保存在 AZ_SCRIPTS_OUTPUT_PATH 位置，并且输出必须为有效的 JSON 字符串对象。 必须将该文件的内容保存为键值对。 例如，字符串数组以 {"MyResult"： ["foo"，"bar"]} 的形式存储。  只存储数组结果（例如 ["foo"，"bar"]）是无效的。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

前面的示例中使用了[jq](https://stedolan.github.io/jq/) 。 它附带了容器映像。 请参阅[配置开发环境](#configure-development-environment)。

## <a name="develop-deployment-scripts"></a>开发部署脚本

### <a name="handle-non-terminating-errors"></a>处理非终止错误

您可以通过使用部署脚本中的[**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
)变量来控制 PowerShell 如何响应非终止错误。 脚本服务未设置/更改值。  尽管你为 $ErrorActionPreference 设置的值，但当脚本遇到错误时，部署脚本会将资源预配状态设置为 "*失败*"。

### <a name="pass-secured-strings-to-deployment-script"></a>将安全字符串传递到部署脚本

通过在容器实例中设置环境变量（Value），可以提供容器运行的应用程序或脚本的动态配置。 部署脚本采用与 Azure 容器实例相同的方式处理非安全和安全的环境变量。 有关详细信息，请参阅[在容器实例中设置环境变量](../../container-instances/container-instances-environment-variables.md#secure-values)。

## <a name="debug-deployment-scripts"></a>调试部署脚本

脚本服务将创建一个[存储帐户](../../storage/common/storage-account-overview.md)（除非指定了现有存储帐户）和一个用于执行脚本的[容器实例](../../container-instances/container-instances-overview.md)。 如果这些资源由脚本服务自动创建，则这两个资源的资源名称中都有**azscripts**后缀。

![资源管理器模板部署脚本资源名称](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

用户脚本、执行结果和 stdout 文件存储在存储帐户的文件共享中。 有一个名为**azscripts**的文件夹。 在该文件夹中，输入和输出文件有两个文件夹： **azscriptinput**和**azscriptoutput**。

输出文件夹包含 **executionresult.json** 和脚本输出文件。 可以在**executionresult**中查看脚本执行错误消息。 仅当成功执行脚本时，才会创建输出文件。 输入文件夹包含一个系统 PowerShell 脚本文件和一些用户部署脚本文件。 您可以使用修订后的用户部署脚本文件替换该文件，然后从 Azure 容器实例中重新运行部署脚本。

你可以使用 REST API 在资源组级别和订阅级别获取部署脚本资源部署信息：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

下面的示例使用[ARMClient](https://github.com/projectkudu/ARMClient)：

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

输出类似于：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

输出显示部署状态和部署脚本资源 Id。

以下 REST API 返回日志：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

它仅在删除部署脚本资源之前工作。

若要在门户中查看 deploymentScripts 资源，请选择 "**显示隐藏的类型**"：

![资源管理器模板部署脚本、显示隐藏的类型、门户](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>使用现有的存储帐户

脚本执行和故障排除需要一个存储帐户和一个容器实例。 你可以选择指定现有的存储帐户，否则脚本服务会自动创建存储帐户和容器实例。 使用现有存储帐户的要求：

- 支持的存储帐户类型为：常规用途 v2、常规用途 v1 和 FileStorage 帐户。 只有 FileStorage 支持高级 SKU。 有关详细信息，请参阅[存储帐户的类型](../../storage/common/storage-account-overview.md)。
- 尚不支持存储帐户防火墙规则。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md)。
- 部署脚本的用户分配的托管标识必须有权管理存储帐户，其中包括读取、创建和删除文件共享。

若要指定现有存储帐户，请将以下 json 添加到的属性元素`Microsoft.Resources/deploymentScripts`中：

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**：指定存储帐户的名称。
- **storageAccountKey "**：指定存储帐户密钥之一。 您可以使用[`listKeys()`](./template-functions-resource.md#listkeys)函数来检索密钥。 例如：

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

有关完整`Microsoft.Resources/deploymentScripts`定义示例，请参阅[示例模板](#sample-templates)。

使用现有存储帐户时，脚本服务将创建一个具有唯一名称的文件共享。 有关脚本服务如何清理文件共享的详细说明，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="clean-up-deployment-script-resources"></a>清理部署脚本资源

脚本执行和故障排除需要一个存储帐户和一个容器实例。 你可以选择指定现有存储帐户，否则脚本服务将自动创建一个存储帐户以及一个容器实例。 当部署脚本在终端状态中执行时，脚本服务将删除两个自动创建的资源。 在这些资源删除之前，这些资源会一直向你收费。 有关价格信息，请参阅[容器实例定价](https://azure.microsoft.com/pricing/details/container-instances/)和[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

这些资源的生命周期由模板中的以下属性控制：

- **cleanupPreference**：当脚本执行处于终端状态时进行清理。 支持的值包括：

  - **始终**：在脚本执行进入终端状态后，删除自动创建的资源。 如果使用现有的存储帐户，则脚本服务会删除存储帐户中创建的文件共享。 由于在清理资源后，deploymentScripts 资源可能仍然存在，因此在删除资源之前，脚本服务将保留脚本执行结果，例如 stdout、输出、返回值等。
  - **OnSuccess**：仅当脚本执行成功时才删除自动创建的资源。 如果使用现有的存储帐户，则脚本服务仅在脚本执行成功时才删除文件共享。 你仍可以访问资源来查找调试信息。
  - **OnExpiration**：仅当**retentionInterval**设置为 "已过期" 时才删除自动资源。 如果使用了现有的存储帐户，则脚本服务将删除文件共享，但保留存储帐户。

- **retentionInterval**：指定将保留脚本资源的时间间隔，超过此时间间隔后，将过期并删除。

> [!NOTE]
> 不建议使用由脚本服务生成的存储帐户和容器实例来实现其他目的。 可能会删除这两个资源，具体取决于脚本生命周期。

## <a name="run-script-more-than-once"></a>多次运行脚本

部署脚本执行是幂等操作。 如果没有更改 deploymentScripts 资源属性（包括内联脚本），则在重新部署该模板时将不会执行该脚本。 部署脚本服务将模板中的资源名称与同一资源组中的现有资源进行比较。 如果要多次执行相同的部署脚本，有两个选项可供选择：

- 更改 deploymentScripts 资源的名称。 例如，使用[utcNow](./template-functions-date.md#utcnow)模板函数作为资源名称，或使用作为资源名称的一部分。 更改资源名称会创建新的 deploymentScripts 资源。 它适用于保留脚本执行的历史记录。

    > [!NOTE]
    > UtcNow 函数只能用于参数的默认值。

- 在`forceUpdateTag`模板属性中指定其他值。  例如，使用 utcNow 作为值。

> [!NOTE]
> 编写为幂等的部署脚本。 这可以确保在意外运行这些脚本时，不会导致系统发生更改。 例如，如果部署脚本用于创建 Azure 资源，请在创建该资源之前验证该资源是否存在，因此，该脚本将成功，或者你不会重新创建资源。

## <a name="configure-development-environment"></a>配置开发环境

你可以使用预配置的 docker 容器映像作为部署脚本开发环境。 以下过程说明如何在 Windows 上配置 docker 映像。 对于 Linux 和 Mac，可以在 Internet 上找到信息。

1. 在开发计算机上安装[Docker Desktop](https://www.docker.com/products/docker-desktop) 。
1. 打开 Docker Desktop。
1. 从 "任务栏" 中选择 "Docker 桌面" 图标，然后选择 "**设置**"。
1. 选择 "**共享驱动器**"，选择要提供给容器的本地驱动器，然后选择 "**应用**"

    ![资源管理器模板部署脚本 docker 驱动器](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. 在提示符下输入 windows 凭据。
1. 打开终端窗口，无论是命令提示符还是 Windows PowerShell （请勿使用 PowerShell ISE）。
1. 将部署脚本容器映像提取到本地计算机：

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    该示例使用版本 PowerShell 2.7.0。

    从 Microsoft 容器注册表（MCR）拉取 CLI 映像：

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    此示例使用版本 CLI 2.0.80。 部署脚本使用[此处](https://hub.docker.com/_/microsoft-azure-cli)的默认 CLI 容器映像。

1. 在本地运行 docker 映像。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    使用共享驱动器上的现有文件夹替换** &lt;主机驱动程序号>** 和** &lt;主机目录名称>** 。  它将文件夹映射到容器中的 **/data sources**文件夹。 例如，若要映射 D:\docker：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-这**意味着使容器映像保持活动状态。

    CLI 示例：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 在出现提示时选择 "**共享**"。
1. 以下屏幕截图显示了如何运行 PowerShell 脚本，假设你在 d:\docker 文件夹中具有 helloworld 文件。

    ![资源管理器模板部署脚本 docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

脚本成功测试后，可以将其用作部署脚本。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用部署脚本。 若要演练部署脚本教程：

> [!div class="nextstepaction"]
> [教程：使用 Azure 资源管理器模板中的部署脚本](./template-tutorial-deployment-script.md)