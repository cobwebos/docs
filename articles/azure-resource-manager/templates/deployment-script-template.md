---
title: 在模板中使用部署脚本 |Microsoft Docs
description: 使用 Azure 资源管理器模板中的部署脚本。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jgao
ms.openlocfilehash: 5475f49acfdcd74a792a9a0f2f16cf1ea3272232
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121890"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>在模板中使用部署脚本（预览）

了解如何使用 Azure 资源模板中的部署脚本。 使用名为 `Microsoft.Resources/deploymentScripts`的新资源类型，用户可以在模板部署中执行部署脚本并查看执行结果。 这些脚本可用于执行自定义步骤，如：

- 将用户添加到目录
- 创建应用注册
- 执行数据平面操作，例如，复制 blob 或种子数据库
- 查找并验证许可证密钥
- 创建自签名证书
- 在 Azure AD 中创建对象
- 查找自定义系统中的 IP 地址块

部署脚本的优点：

- 易于编码、使用和调试。 你可以在最喜欢的开发环境中开发部署脚本。 脚本可以嵌入在模板或外部脚本文件中。
- 可以指定脚本语言和平台。 目前仅支持 Linux 环境上的 Azure PowerShell 部署脚本。
- 允许指定用于执行脚本的标识。 目前仅支持[Azure 用户分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
- 允许将命令行参数传递给脚本。
- 可以指定脚本输出，并将其传递回部署。

> [!NOTE]
> 部署脚本目前处于预览阶段。 若要使用它，必须[注册预览](https://aka.ms/armtemplatepreviews)。

> [!IMPORTANT]
> 在相同的资源组中创建两个部署脚本资源（一个存储帐户和一个容器实例），用于执行脚本和进行故障排除。 当部署脚本在终端状态中执行时，这些资源通常由脚本服务删除。 在删除资源之前，会对资源计费。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="prerequisites"></a>必备组件

- **用户分配的托管标识，其中包含参与者在订阅级别的角色**。 此标识用于执行部署脚本。 若要创建一个，请参阅[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)。 部署模板时需要标识 ID。 标识的格式为：

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  使用以下 PowerShell 脚本通过提供资源组名称和标识名称获取 ID。

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

- **Azure PowerShell 版本2.7.0、2.8.0 或 3.0.0**。 不需要这些版本来部署模板。 但在本地测试部署脚本需要这些版本。 请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 可以使用预配置的 Docker 映像。  请参阅[配置开发环境](#configure-development-environment)。

## <a name="resource-schema"></a>资源架构

下面的 json 是一个示例。  可在[此处](/azure/templates/microsoft.resources/deploymentscripts)找到最新的模板架构。

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "myDeploymentScript",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "2.8",
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ",
    "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json",
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
- **kind**：指定脚本的类型。 目前仅支持 Azure PowerShell 脚本。 值为**AzurePowerShell**。
- **forceUpdateTag**：在模板部署之间更改此值会强制重新执行部署脚本。 使用需要设置为参数的 defaultValue 的 newGuid （）或 utcNow （）函数。 若要了解详细信息，请参阅多次[运行脚本](#run-script-more-than-once)。
- **azPowerShellVersion**：指定要使用的 Azure PowerShell 模块版本。 部署脚本当前支持版本2.7.0、2.8.0 和3.0.0。
- **arguments**：指定参数值。 值由空格分隔。
- **scriptContent**：指定脚本内容。 若要运行外部脚本，请改用 `primaryScriptUri`。 有关示例，请参阅[使用内联脚本](#use-inline-scripts)和[使用外部脚本](#use-external-scripts)。
- **primaryScriptUri**：使用受支持的 powershell 文件扩展名为主 powershell 脚本指定可公开访问的 Url。
- **supportingScriptUris**：指定一个可公开访问的 url 数组，以支持将在 `ScriptContent` 或 `PrimaryScriptUri`中调用的 powershell 文件。
- **timeout**：指定以[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)指定的最大允许脚本执行时间。 默认值为**P1D**。
- **cleanupPreference**。 指定在脚本执行处于终端状态时清理部署资源的首选项。 默认设置**始终**为，这意味着，即使在终端状态（成功、失败、已取消）的情况下也删除资源。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。
- **retentionInterval**：指定服务在部署脚本执行达到终端状态后保留部署脚本资源的时间间隔。 此持续时间到期时，将删除部署脚本资源。 持续时间基于[ISO 8601 模式](https://en.wikipedia.org/wiki/ISO_8601)。 默认值为**P1D**，这意味着七天。 当 cleanupPreference 设置为*OnExpiration*时，将使用此属性。 当前未启用*OnExpiration*属性。 若要了解详细信息，请参阅[清理部署脚本资源](#clean-up-deployment-script-resources)。

## <a name="use-inline-scripts"></a>使用内联脚本

以下模板有一个用 `Microsoft.Resources/deploymentScripts` 类型定义的资源。

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json?range=1-54)]

> [!NOTE]
> 由于内联部署脚本用双引号引起来，因此，需要将部署脚本内的字符串用单引号引起来。 PowerShell 的转义符是 **&#92;** 。 你还可以考虑使用字符串替换，如前面的 JSON 示例中所示。 请参阅 name 参数的默认值。

脚本采用一个参数，并输出参数值。 **DeploymentScriptOutputs**用于存储输出。  在 "输出" 部分中，"**值**" 行显示了如何访问存储的值。 `Write-Output` 用于调试目的。 若要了解如何访问输出文件，请参阅[调试部署脚本](#debug-deployment-scripts)。  有关属性说明，请参阅[资源架构](#resource-schema)。

若要运行该脚本，请选择 "**尝试**" 以打开 Cloud shell，然后将以下代码粘贴到 "shell" 窗格中。

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

除了内联脚本以外，还可以使用外部脚本文件。 目前仅支持具有**ps1**文件扩展名的 PowerShell 脚本。 若要使用外部脚本文件，请将 `scriptContent` 替换为 `primaryScriptUri`。 例如：

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

若要查看示例，请[在此处](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)选择。

外部脚本文件必须是可访问的。  若要保护存储在 Azure 存储帐户中的脚本文件，请参阅[教程：在 azure 资源管理器模板部署中保护项目](./template-tutorial-secure-artifacts.md)。

## <a name="use-supporting-scripts"></a>使用支持脚本

可以将复杂的逻辑分隔到一个或多个支持脚本文件中。 `supportingScriptURI` 属性允许你根据需要向支持脚本文件提供 Uri 的数组：

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

支持脚本文件可以从内联脚本和主脚本文件中进行调用。

支持文件将复制到运行时的 azscripts/azscriptinput。 使用相对路径引用内联脚本和主脚本文件中的支持文件。

## <a name="work-with-outputs-from-deployment-scripts"></a>使用部署脚本的输出

以下模板演示了如何在两个 deploymentScripts 资源之间传递值：

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-basic.json?range=1-84)]

在第一个资源中，可以定义一个名为 **$DeploymentScriptOutputs**的变量，并使用它来存储输出值。 若要访问模板内其他资源的输出值，请使用：

```json
reference('<ResourceName>').output.text
```

## <a name="debug-deployment-scripts"></a>调试部署脚本

脚本服务将创建一个[存储帐户](../../storage/common/storage-account-overview.md)和一个用于执行脚本的[容器实例](../../container-instances/container-instances-overview.md)。 这两个资源在资源名称中都有**azscripts**后缀。

![资源管理器模板部署脚本资源名称](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

用户脚本、执行结果和 stdout 文件存储在存储帐户的文件共享中。 有一个名为**azscripts**的文件夹。 在该文件夹中，输入和输出文件有两个文件夹： **azscriptinput**和**azscriptoutput**。

Output 文件夹包含**executionresult**和脚本输出文件。 可以在**executionresult**中查看脚本执行错误消息。 仅当成功执行脚本时，才会创建输出文件。 输入文件夹包含系统 PowerShell 脚本文件和用户部署脚本文件。 您可以使用修订后的用户部署脚本文件替换该文件，然后从 Azure 容器实例中重新运行部署脚本。

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

[!code-json[](~/resourcemanager-templates/deployment-script/deploymentscript-status.json?range=1-48)]

输出显示部署状态和部署脚本资源 Id。

以下 REST API 返回日志：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

它仅在删除部署脚本资源之前工作。

若要在门户中查看 deploymentScripts 资源，请选择 "**显示隐藏的类型**"：

![资源管理器模板部署脚本、显示隐藏的类型、门户](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>清理部署脚本资源

部署脚本将创建一个存储帐户和一个用于执行部署脚本并存储调试信息的容器实例。 这两个资源是在预配资源所在的资源组中创建的，并且在脚本过期时将由脚本服务删除。 您可以控制这些资源的生命周期。  在删除之前，将对这两个资源进行计费。 有关价格信息，请参阅[容器实例定价](https://azure.microsoft.com/pricing/details/container-instances/)和[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。

这些资源的生命周期由模板中的以下属性控制：

- **cleanupPreference**：当脚本执行处于终端状态时进行清理。  支持的值包括：

  - **始终**：在脚本执行进入终端状态后删除资源。 由于在清理资源后，deploymentScripts 资源可能仍然存在，因此在删除资源之前，系统脚本会将脚本执行结果（例如，stdout、输出、返回值等）复制到 DB。
  - **OnSuccess**：仅当脚本执行成功时才删除资源。 你仍可以访问资源来查找调试信息。
  - **OnExpiration**：仅当**retentionInterval**设置为 "已过期" 时才删除资源。 此属性当前被禁用。

- **retentionInterval**：指定将保留脚本资源的时间间隔，超过此时间间隔后，将过期并删除。

> [!NOTE]
> 建议不要将部署脚本资源用于其他目的。

## <a name="run-script-more-than-once"></a>多次运行脚本

部署脚本执行是幂等操作。 如果没有更改 deploymentScripts 资源属性（包括内联脚本），则在重新部署该模板时将不会执行该脚本。 部署脚本服务将模板中的资源名称与同一资源组中的现有资源进行比较。 如果要多次执行相同的部署脚本，有两个选项可供选择：

- 更改 deploymentScripts 资源的名称。 例如，使用[utcNow](./template-functions-string.md#utcnow)模板函数作为资源名称，或使用作为资源名称的一部分。 更改资源名称会创建新的 deploymentScripts 资源。 它适用于保留脚本执行的历史记录。

    > [!NOTE]
    > UtcNow 函数只能用于参数的默认值。

- 在 "`forceUpdateTag` 模板" 属性中指定其他值。  例如，使用 utcNow 作为值。

> [!NOTE]
> 编写为幂等的部署脚本。 这可以确保，如果它们意外再次运行，则不会导致系统更改。 例如，如果部署脚本用于创建 Azure 资源，请在创建该资源之前验证该资源是否存在，因此，该脚本将成功，或者你不会重新创建资源。

## <a name="configure-development-environment"></a>配置开发环境

目前，部署脚本支持 Azure PowerShell 版本2.7.0、2.8.0 和3.0.0。  如果你有 Windows 计算机，则可以安装一个受支持的 Azure PowerShell 版本，并开始开发和测试部署脚本。  如果没有 Windows 计算机，或者没有安装这些 Azure PowerShell 版本中的任何一个，则可以使用预配置的 docker 容器映像。 以下过程说明如何在 Windows 上配置 docker 映像。 对于 Linux 和 Mac，可以在 Internet 上找到信息。

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

    该示例使用版本2.7.0。

1. 在本地运行 docker 映像。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    使用共享驱动器上的现有文件夹替换 **&lt;主机驱动程序号 >** 和 **&lt;主机目录名称 >** 。  它将文件夹映射到容器中的 **/data sources**文件夹。 例如，若要映射 D:\docker：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-这**意味着使容器映像保持活动状态。

1. 在出现提示时选择 "**共享**"。
1. 运行 PowerShell 脚本，如以下屏幕截图所示（假定 d:\docker 文件夹中有 helloworld 文件。）

    ![资源管理器模板部署脚本 docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

成功测试 PowerShell 脚本后，可以将其用作部署脚本。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用部署脚本。 若要演练部署脚本教程：

> [!div class="nextstepaction"]
> [教程：使用 Azure 资源管理器模板中的部署脚本](./template-tutorial-deployment-script.md)