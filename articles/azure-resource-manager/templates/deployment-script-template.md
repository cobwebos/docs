---
title: 在模板中使用部署脚本 |微软文档
description: 在 Azure 资源管理器模板中使用部署脚本。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: f84707adfa406011989c8f9bfdf1e8d9270698a6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984787"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>在模板中使用部署脚本（预览）

了解如何在 Azure 资源模板中使用部署脚本。 使用称为`Microsoft.Resources/deploymentScripts`的新资源类型，用户可以在模板部署中执行部署脚本并查看执行结果。 这些脚本可用于执行自定义步骤，例如：

- 将用户添加到目录
- 创建应用注册
- 执行数据平面操作，例如，复制 blob 或种子数据库
- 查找并验证许可证密钥
- 创建自签名证书
- 在 Azure AD 中创建对象
- 从自定义系统查找 IP 地址块

部署脚本的好处：

- 易于编码、使用和调试。 您可以在喜爱的开发环境中开发部署脚本。 脚本可以嵌入到模板或外部脚本文件中。
- 您可以指定脚本语言和平台。 目前，支持 Linux 环境中的 Azure PowerShell 和 Azure CLI 部署脚本。
- 允许指定用于执行脚本的标识。 目前，仅支持[Azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。
- 允许将命令行参数传递给脚本。
- 可以指定脚本输出并将其传回部署。

部署脚本资源仅在 Azure 容器实例可用的区域可用。  请参阅[Azure 区域中的 Azure 容器实例的资源可用性](../../container-instances/container-instances-region-availability.md)。

> [!IMPORTANT]
> 在同一资源组中会创建两个部署脚本资源（一个存储帐户和一个容器实例），用于执行脚本和进行故障排除。 当部署脚本执行处于终端状态时，脚本服务通常会删除这些资源。 在这些资源删除之前，这些资源会一直向你收费。 要了解更多信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="prerequisites"></a>先决条件

- **具有参与者角色的用户分配的托管标识到目标资源组**。 此标识用来执行部署脚本。 要在资源组之外执行操作，您需要授予其他权限。 例如，如果要创建新的资源组，请将标识分配给订阅级别。

  > [!NOTE]
  > 部署脚本引擎在后台创建存储帐户和容器实例。  如果订阅尚未注册 Azure 存储帐户 （Microsoft.Storage） 和 Azure 容器实例 （Microsoft.ContainerInstance） 资源提供程序，则需要在订阅级别具有参与者角色的用户分配的托管标识。

  要创建标识，请参阅通过使用 Azure 门户 、[或使用 Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)或使用[Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)[创建用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 部署模板时需要此标识 ID。 标识符的格式为：

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  使用以下 CLI 或 PowerShell 脚本通过提供资源组名称和标识名称来获取 ID。

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

- **Azure 电源外壳**或**Azure CLI**。 有关受支持的 Azure PowerShell 版本的列表，请参阅[此处](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)。有关受支持的 Azure CLI 版本的列表，请参阅[此处](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)。

    >[!IMPORTANT]
    > 部署脚本使用来自微软容器注册表 （MCR） 的可用 CLI 映像。 验证部署脚本的 CLI 映像大约需要一个月的时间。 不要使用 30 天内发布的 CLI 版本。 要查找映像的发布日期，请参阅 Azure [CLI 发行说明](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)。 如果使用不支持的版本，错误消息将列出受支持的版本。

    部署模板不需要这些版本。 但是，在本地测试部署脚本需要这些版本。 请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 您可以使用预配置的 Docker 映像。  请参阅[配置开发环境](#configure-development-environment)。

## <a name="sample-templates"></a>示例模板

下面的 json 就是一个例子。  最新的模板架构可以[在这里](/azure/templates/microsoft.resources/deploymentscripts)找到。

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
> 该示例用于演示目的。  **脚本内容和****主脚本 Uri 不能**共存于模板中。

属性值详细信息：

- **标识**：部署脚本服务使用用户分配的托管标识执行脚本。 目前，仅支持用户分配的托管标识。
- **类型**：指定脚本的类型。 目前，Azure PowerShell 和 Azure CLI 脚本是支持的。 这些值是**AzurePowerShell**和**AzureCLI**。
- **forceUpdateTag**： 在模板部署之间更改此值会强制部署脚本重新执行。 使用需要设置为参数的默认值的 newGuid（） 或 utcNow（） 函数。 若要了解详细信息，请参阅[多次运行脚本](#run-script-more-than-once)。
- **azPowerShellVersion**/**azCliVersion：** 指定要使用的模块版本。 有关受支持的 PowerShell 和 CLI 版本的列表，请参阅[先决条件](#prerequisites)。
- **参数**：指定参数值。 请以空格分隔这些值。
- **环境变量**：指定要传递给脚本的环境变量。 有关详细信息，请参阅[开发部署脚本](#develop-deployment-scripts)。
- **脚本内容**：指定脚本内容。 要运行外部脚本，请使用`primaryScriptUri`。 有关示例，请参阅[使用内联脚本](#use-inline-scripts)[和使用外部脚本](#use-external-scripts)。
- **主脚本库**：指定具有受支持文件扩展名的主部署脚本的可公开访问的 Url。
- **支持ScriptUris：** 指定一组可公开访问的 Url 来支持在`ScriptContent`或`PrimaryScriptUri`中调用的文件。
- **超时**：指定[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)中指定的最大允许脚本执行时间。 默认值为 **P1D**。
- **清理首选项**。 指定在脚本执行进入终端状态时清理部署资源的首选项。 默认设置为 **"始终**"，这意味着删除资源，尽管终端状态（成功，失败，已取消）。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。
- **保留间隔**：指定在部署脚本执行达到终端状态后服务保留部署脚本资源的时间间隔。 在此持续时间到期时，将删除部署脚本资源。 持续时间基于[ISO 8601 模式](https://en.wikipedia.org/wiki/ISO_8601)。 默认值为**P1D，** 表示 7 天。 当 cleanupPreference 设置为 *OnExpiration* 时将使用此属性。 当前未启用 *"过期*"属性。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

### <a name="additional-samples"></a>其他示例

- [创建证书并将其分配给密钥保管库](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [创建用户分配的托管标识并将其分配给资源组，并运行部署脚本](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)。

> [!NOTE]
> 建议创建用户分配的标识并提前授予权限。 如果在运行部署脚本的同一模板中创建标识并授予权限，则可能会收到登录和权限相关的错误。 权限生效需要一些时间。

## <a name="use-inline-scripts"></a>使用内联脚本

以下模板具有一个使用`Microsoft.Resources/deploymentScripts`类型定义的资源。 突出显示的部分是内联脚本。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> 由于内联部署脚本是用双引号括起来的，因此部署脚本内的字符串需要改用单引号括起来。 PowerShell 的转义字符是 **&#92;**。 您还可以考虑使用字符串替换，如上一个 JSON 示例中所示。 请参阅名称参数的默认值。

该脚本采用一个参数，并输出参数值。 **部署脚本输出**用于存储输出。  在输出部分中，**值**行演示如何访问存储的值。 `Write-Output`用于调试目的。 要了解如何访问输出文件，请参阅[调试部署脚本](#debug-deployment-scripts)。  有关属性说明，请参阅[示例模板](#sample-templates)。

要运行脚本，请选择 **"尝试它**以打开云外壳"，然后将以下代码粘贴到 shell 窗格中。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

输出如下所示：

![资源管理器模板部署脚本 hello 世界输出](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>使用外部脚本

除了内联脚本之外，您还可以使用外部脚本文件。 仅支持具有**ps1**文件扩展名的主 PowerShell 脚本。 对于 CLI 脚本，主脚本可以具有任何扩展（或没有扩展），只要脚本是有效的 bash 脚本。 要使用外部脚本文件，请`scriptContent`替换为`primaryScriptUri`。 例如：

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

要查看示例，请[在此处](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)选择 。

外部脚本文件必须可访问。  要保护存储在 Azure 存储帐户中的脚本文件，请参阅[教程：Azure 资源管理器模板部署中的安全项目](./template-tutorial-secure-artifacts.md)。

您有责任确保部署脚本引用的脚本（**主要脚本库**或**支持脚本Uri）的完整性**。  仅引用您信任的脚本。

## <a name="use-supporting-scripts"></a>使用支持脚本

您可以将复杂逻辑分离到一个或多个支持脚本文件中。 如果需要`supportingScriptURI`，该属性允许您向支持脚本文件提供一组 URI：

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

可以从内联脚本和主脚本文件调用支持脚本文件。 支持脚本文件对文件扩展名没有限制。

支持文件在运行时复制到脚本/脚本输入。 使用相对路径引用来自内联脚本和主脚本文件中的支持文件。

## <a name="work-with-outputs-from-powershell-script"></a>使用 PowerShell 脚本的输出

以下模板演示如何在两个部署脚本资源之间传递值：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

在第一个资源中，定义一个称为 **$DeploymentScriptOutputs**的变量，并用它来存储输出值。 要从模板中的另一个资源访问输出值，请使用：

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>使用 CLI 脚本的输出

与 PowerShell 部署脚本不同，CLI/bash 支持不会公开用于存储脚本输出的通用变量，而是存在一个称为**AZ_SCRIPTS_OUTPUT_PATH**的环境变量，用于存储脚本输出文件所在的位置。 如果部署脚本是从资源管理器模板运行的，则 Bash shell 会自动为您设置此环境变量。

部署脚本输出必须保存在AZ_SCRIPTS_OUTPUT_PATH位置，并且输出必须是有效的 JSON 字符串对象。 文件的内容必须保存为键值对。 例如，字符串数组存储为 [我的结果]： [ "foo"，"bar"* 。  仅存储数组结果（例如"foo"，"bar"）无效。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/)在上一个示例中使用。 它附带容器图像。 请参阅[配置开发环境](#configure-development-environment)。

## <a name="develop-deployment-scripts"></a>开发部署脚本

### <a name="handle-non-terminating-errors"></a>处理非终止错误

通过使用部署脚本中的[**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
)变量，您可以控制 PowerShell 如何响应非终止错误。 部署脚本引擎不设置/更改值。  尽管为$ErrorActionPreference设置了值，但当脚本遇到错误时，部署脚本会将资源预配状态设置为 *"失败*"。

### <a name="pass-secured-strings-to-deployment-script"></a>将安全字符串传递给部署脚本

在容器实例中设置环境变量（环境变量）允许您提供容器运行的应用程序或脚本的动态配置。 部署脚本处理不安全和安全的环境变量的方式与 Azure 容器实例相同。 有关详细信息，请参阅[容器实例中的"设置环境变量](../../container-instances/container-instances-environment-variables.md#secure-values)"。

## <a name="debug-deployment-scripts"></a>调试部署脚本

脚本服务创建[存储帐户](../../storage/common/storage-account-overview.md)和[容器实例](../../container-instances/container-instances-overview.md)以执行脚本。 这两个资源在资源名称中都有**脚本**后缀。

![资源管理器模板部署脚本资源名称](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

用户脚本、执行结果和斯特写文件存储在存储帐户的文件共享中。 有一个文件夹叫做 **"脚本**"。 在文件夹中，还有两个用于输入和输出文件的文件夹 **：azscriptinput**和**azscript 输出**。

输出文件夹包含 **executionresult.json** 和脚本输出文件。 您可以在**执行结果**中看到脚本执行错误消息。 仅当脚本成功执行时，才会创建输出文件。 输入文件夹包含一个系统 PowerShell 脚本文件和一些用户部署脚本文件。 您可以将用户部署脚本文件替换为修订后的脚本文件，并从 Azure 容器实例重新运行部署脚本。

通过使用 REST API，可以在资源组级别和订阅级别获取部署脚本资源部署信息：

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

输出显示部署状态和部署脚本资源指示。

以下 REST API 返回日志：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

它仅在删除部署脚本资源之前工作。

要查看门户中的部署脚本资源，请选择 **"显示隐藏类型**：

![资源管理器模板部署脚本，显示隐藏类型、门户](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>清理部署脚本资源

部署脚本创建存储帐户和容器实例，用于执行部署脚本和存储调试信息。 这两个资源与预配资源在同一资源组中创建，脚本服务将在脚本过期时删除这些资源。 您可以控制这些资源的生命周期。  在删除它们之前，您都会为这两个资源收费。 有关价格信息，请参阅[容器实例定价](https://azure.microsoft.com/pricing/details/container-instances/)和[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

这些资源的生命周期由模板中的以下属性控制：

- **清理首选项**：当脚本执行处于终端状态时，请清理首选项。  支持的值包括：

  - **始终**：一旦脚本执行进入终端状态，请删除资源。 由于清除资源后部署脚本资源可能仍然存在，因此系统脚本会在删除资源之前将脚本执行结果（例如，停滞、输出、返回值等）复制到数据库。
  - **成功**：仅在脚本执行成功时删除资源。 您仍然可以访问资源以查找调试信息。
  - **过期**时：仅在**保留间隔**设置过期时删除资源。 此属性当前已禁用。

- **保留间隔**：指定将保留脚本资源的时间间隔，之后将过期和删除。

> [!NOTE]
> 不建议将部署脚本资源用于其他目的。

## <a name="run-script-more-than-once"></a>多次运行脚本

部署脚本执行是一种幂等操作。 如果未更改任何部署脚本资源属性（包括内联脚本），则在重新部署模板时不会执行该脚本。 部署脚本服务将模板中的资源名称与同一资源组中的现有资源进行比较。 如果要多次执行同一部署脚本，有两个选项：

- 更改部署脚本资源的名称。 例如，使用[utcNow](./template-functions-date.md#utcnow)模板函数作为资源名称或资源名称的一部分。 更改资源名称将创建新的部署脚本资源。 它有利于保留脚本执行历史记录。

    > [!NOTE]
    > utcNow 函数只能在参数的默认值中使用。

- 在模板属性中`forceUpdateTag`指定不同的值。  例如，使用 utcNow 作为值。

> [!NOTE]
> 编写幂等化的部署脚本。 这可以确保在意外运行这些脚本时，不会导致系统发生更改。 例如，如果部署脚本用于创建 Azure 资源，则在创建该资源之前验证该资源不存在，因此该脚本将成功或您不再创建资源。

## <a name="configure-development-environment"></a>配置开发环境

您可以使用预配置的 Docker 容器映像作为部署脚本开发环境。 以下过程演示如何在 Windows 上配置 Docker 映像。 对于 Linux 和 Mac，您可以在 Internet 上找到信息。

1. 在开发计算机上安装[Docker 桌面](https://www.docker.com/products/docker-desktop)。
1. 打开 Docker 桌面。
1. 从任务栏中选择 Docker 桌面图标，然后选择 **"设置**"。
1. 选择 **"共享驱动器**"，选择要对容器可用的本地驱动器，然后选择 **"应用"**

    ![资源管理器模板部署脚本 Docker 驱动器](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. 在提示符处输入窗口凭据。
1. 打开终端窗口，命令提示符或 Windows PowerShell（请勿使用 PowerShell ISE）。
1. 将部署脚本容器映像拉到本地计算机：

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    该示例使用版本 PowerShell 2.7.0。

    要从 Microsoft 容器注册表 （MCR） 中提取 CLI 映像：

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    此示例使用版本 CLI 2.0.80。 部署脚本使用[此处](https://hub.docker.com/_/microsoft-azure-cli)找到的默认 CLI 容器映像。

1. 在本地运行 Docker 映像。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    将**&lt;主机驱动程序字母>** 和**&lt;主机目录名称>** 与共享驱动器上的现有文件夹替换。  它将文件夹映射到容器中的 **/data**文件夹。 有关示例，要映射 D：\docker：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-这意味着**保持容器映像处于活动状态。

    CLI 示例：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 选择在收到提示时**共享它**。
1. 以下屏幕截图显示了如何运行 PowerShell 脚本，因为 d：\docker 文件夹中有一个 helloworld.ps1 文件。

    ![资源管理器模板部署脚本 docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

测试脚本成功后，您可以将其用作部署脚本。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何使用部署脚本。 要浏览部署脚本教程，请进行以下说明：

> [!div class="nextstepaction"]
> [教程：在 Azure 资源管理器模板中使用部署脚本](./template-tutorial-deployment-script.md)