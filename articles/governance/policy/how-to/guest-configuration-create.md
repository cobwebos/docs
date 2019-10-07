---
title: 如何创建来宾配置策略
description: 了解如何创建适用于 Windows 或 Linux Vm 的 Azure 策略来宾配置策略。
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/20/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: fcb65e75de730178901742dc36c72776e39b044b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977974"
---
# <a name="how-to-create-guest-configuration-policies"></a>如何创建来宾配置策略

来宾配置使用[Desired State configuration](/powershell/dsc) （DSC）资源模块来创建用于审核 Azure 计算机的配置。 DSC 配置定义计算机应处于的状态。 如果评估配置失败，则会触发策略效果**auditIfNotExists** ，并将计算机视为**不符合**。

[Azure 策略来宾配置](/azure/governance/policy/concepts/guest-configuration)只能用于审核计算机内部的设置。 计算机内的设置的修正功能尚不可用。

使用以下操作创建自己的配置，以验证 Azure 计算机的状态。

> [!IMPORTANT]
> 使用来宾配置的自定义策略是一项预览功能。

## <a name="add-the-guestconfiguration-resource-module"></a>添加 GuestConfiguration 资源模块

若要创建来宾配置策略，必须添加资源模块。 此资源模块可用于本地安装的 PowerShell、 [Azure Cloud Shell](https://shell.azure.com)或[Azure PowerShell Docker 映像](https://hub.docker.com/rsdk-powershell/)。

### <a name="base-requirements"></a>基本要求

来宾配置资源模块需要以下软件：

- PowerShell. 若尚未安装，请遵循[这些说明](/powershell/scripting/install/installing-powershell)。
- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。

### <a name="install-the-module"></a>安装模块

来宾配置使用**GuestConfiguration**资源模块来创建 DSC 配置并将其发布到 Azure 策略：

1. 在 PowerShell 提示符下，运行以下命令：

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 验证是否已导入该模块：

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>创建自定义来宾配置配置和资源

为来宾配置创建自定义策略的第一步是创建 DSC 配置。 有关 DSC 概念和术语的概述，请参阅[POWERSHELL DSC 概述](/powershell/dsc/overview/overview)。

如果你的配置只需要安装来宾配置代理的内置资源，则只需编写配置 MOF 文件。 如果需要运行其他脚本，则需要创作自定义资源模块。

### <a name="requirements-for-guest-configuration-custom-resources"></a>来宾配置自定义资源的要求

当来宾配置审核计算机时，它会首先运行 `Test-TargetResource` 来确定其是否处于正确的状态。 函数返回的布尔值确定来宾分配的 Azure 资源管理器状态是否应符合或不符合。 如果配置中的任何资源的布尔值都为 `$false`，则提供程序将运行 `Get-TargetResource`。 如果布尔值为 `$true`，则不会调用 `Get-TargetResource`。

函数 `Get-TargetResource` 对来宾配置有特殊的要求，这些要求对于 Windows Desired 状态配置没有必要。

- 返回的哈希表必须包含**一个名为的属性**。
- 原因属性必须是数组。
- 数组中的每一项应为一个具有名为**Code**和**短语**的键的哈希表。

"原因" 属性用于标准化计算机不符合时信息的显示方式。 可以将每个项视为一个 "原因"，因为资源不符合要求。 属性是一个数组，因为资源可能因多种原因而不符合要求。

服务需要属性**代码**和**短语**。 创作自定义资源时，请设置要显示的文本（通常为 stdout），因为该资源不符合**短语**的值。 **代码**具有特定的格式要求，因此报表可以清楚地显示用于执行审核的资源的相关信息。 此解决方案使来宾配置可扩展。 只要可以捕获输出并将其作为**短语**属性的字符串值返回，就可以运行任何命令来审核计算机。

- **代码**（字符串）：此资源的名称，重复，然后不包含空格作为原因标识符的短名称。 这三个值应以冒号分隔，不含空格。
  - 示例 `registry:registry:keynotpresent`
- **短语**（字符串）：可读文本，用于说明设置不符合的原因。
  - 示例 `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

#### <a name="scaffolding-a-guest-configuration-project"></a>将来宾配置项目基架

对于想要加速入门和处理示例代码的开发人员，名为 "**来宾配置项目**" 的社区项目作为[Plaster](https://github.com/powershell/plaster) PowerShell 模块的模板存在。 此工具可用于基架项目（包括工作配置和示例资源）和一组用于验证项目的[Pester](https://github.com/pester/pester)测试。 该模板还包括用于自动构建和验证来宾配置包的 Visual Studio Code 的任务流。 有关详细信息，请参阅 GitHub 项目[来宾配置项目](https://github.com/microsoft/guestconfigurationproject)。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自定义来宾配置配置

适用于 Linux 上的来宾配置的 DSC 配置使用 @no__t 资源为引擎提供[Chef InSpec](https://www.chef.io/inspec/)定义的名称。 **Name**为唯一必需的资源属性。

以下示例创建名为 "**基线**" 的配置，导入**GuestConfiguration**资源模块，并使用 `ChefInSpecResource` 资源将 InSpec 定义的名称设置为 " **linux-修补程序-基线**"：

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

有关详细信息，请参阅[编写、编译和应用配置](/powershell/dsc/configurations/write-compile-apply-configuration)。

### <a name="custom-guest-configuration-configuration-on-windows"></a>Windows 上的自定义来宾配置配置

仅来宾配置代理使用 Azure 策略来宾配置的 DSC 配置，而不会与 Windows PowerShell Desired State Configuration 冲突。

以下示例创建名为**AuditBitLocker**的配置，导入**GuestConfiguration**资源模块，并使用 `Service` 资源来审核正在运行的服务：

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

有关详细信息，请参阅[编写、编译和应用配置](/powershell/dsc/configurations/write-compile-apply-configuration)。

## <a name="create-guest-configuration-custom-policy-package"></a>创建来宾配置自定义策略包

MOF 编译完成后，必须将支持文件打包在一起。 来宾配置使用已完成的包来创建 Azure 策略定义。 包包括：

- 作为 MOF 的已编译 DSC 配置
- 模块文件夹
  - GuestConfiguration 模块
  - DscNativeResources 模块
  - Linux具有 Chef InSpec 定义和其他内容的文件夹
  - Windows未内置的 DSC 资源模块

@No__t cmdlet 将创建包。 以下格式用于创建自定义包：

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

@No__t cmdlet 的参数：

- **名称**：来宾配置包名称。
- **配置**：已编译的 DSC 配置文档完整路径。
- **路径**：输出文件夹路径。 此参数是可选的。 如果未指定，则将在当前目录中创建包。
- **ChefProfilePath**：InSpec 配置文件的完整路径。 仅在将内容创建到审核 Linux 时支持此参数。

已完成的包必须存储在被管理的虚拟机可访问的位置。 示例包括 GitHub 存储库、Azure 存储库或 Azure 存储。 如果你不想使包成为公共包，可以在 URL 中包含[SAS 令牌](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 你还可以实现专用网络中的计算机的[服务终结点](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)，尽管此配置仅适用于访问包，而不会与服务通信。

### <a name="working-with-secrets-in-guest-configuration-packages"></a>使用来宾配置包中的机密

在 Azure 策略来宾配置中，管理运行时使用的机密的最佳方式是将它们存储在 Azure Key Vault 中。 此设计在自定义 DSC 资源内实现。

1. 首先，在 Azure 中创建用户分配的托管标识。

   计算机使用该标识来访问存储在 Key Vault 中的机密。 有关详细步骤，请参阅[使用 Azure PowerShell 创建、列出或删除用户分配的托管标识](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。

1. 创建 Key Vault 实例。

   有关详细步骤，请参阅[设置和检索机密-PowerShell](../../../key-vault/quick-create-powershell.md)。
   向实例分配权限，以向用户分配的标识授予对存储在 Key Vault 中的机密的访问权限。 有关详细步骤，请参阅[设置和检索机密-.net](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault)。

1. 将用户分配的标识分配到计算机。

   有关详细步骤，请参阅[使用 PowerShell 在 AZURE VM 上配置 azure 资源的托管标识](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity)。
   大规模，使用 Azure 资源管理器通过 Azure 策略分配此标识。 有关详细步骤，请参阅[使用模板在 AZURE VM 上配置 azure 资源的托管标识](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm)。

1. 最后，在自定义资源中使用上面生成的客户端 ID 通过计算机上提供的令牌访问 Key Vault。

   可以将 `client_id` 和 Key Vault 实例的 url 作为[属性](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema)传递到资源，以便无需为多个环境更新资源，或者需要更改这些值。

下面的代码示例可在自定义资源中使用，以使用用户分配的标识从 Key Vault 检索机密。 从请求返回到 Key Vault 的值为纯文本。 最佳做法是将其存储在 credential 对象中。

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>测试来宾配置包

创建配置包后，但在将其发布到 Azure 之前，可以从工作站或 CI/CD 环境测试包的功能。 GuestConfiguration 模块包括一个 cmdlet `Test-GuestConfigurationPackage`，它在开发环境中加载与在 Azure 计算机中使用的相同的代理。 使用此解决方案，你可以在发布到计费的测试/QA/生产环境之前，在本地执行集成测试。

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

@No__t cmdlet 的参数：

- **名称**：来宾配置策略名称。
- **参数**：以哈希表格式提供的策略参数。
- **路径**：来宾配置包的完整路径。

Cmdlet 还支持来自 PowerShell 管道的输入。 通过管道将 `New-GuestConfigurationPackage` cmdlet 的输出传递给 @no__t cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

有关如何使用参数测试的详细信息，请参阅下面的 "[使用自定义来宾配置策略中的参数](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies)" 一节。

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>创建 Azure 策略定义和计划部署文件

创建来宾配置自定义策略包并将其上传到计算机可访问的位置后，请创建 Azure 策略的来宾配置策略定义。 @No__t cmdlet 采用可公开访问的来宾配置自定义策略包，并创建**auditIfNotExists**和**deployIfNotExists**策略定义。 同时还会创建包括两个策略定义的策略计划定义。

以下示例在来自 Windows 的来宾配置自定义策略包的指定路径中创建策略和计划定义，并提供名称、说明和版本：

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

@No__t cmdlet 的参数：

- **ContentUri**：来宾配置内容包的公共 http （s） uri。
- **DisplayName**：策略显示名称。
- **说明**：策略描述。
- **参数**：以哈希表格式提供的策略参数。
- **版本**：策略版本。
- **路径**：创建策略定义的目标路径。
- **平台**：适用于来宾配置策略和内容包的目标平台（Windows/Linux）。

@No__t 创建以下文件：

- **auditIfNotExists**
- **deployIfNotExists**
- **倡议**

Cmdlet 输出返回一个对象，该对象包含策略文件的计划显示名称和路径。

如果要使用此命令基架自定义策略项目，可以对这些文件进行更改。 例如，将修改 "If" 节，以评估计算机是否存在特定标记。 有关创建策略的详细信息，请参阅[以编程方式创建策略](./programmatically-create.md)。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>使用自定义来宾配置策略中的参数

来宾配置支持在运行时重写配置的属性。 此功能意味着包中 MOF 文件中的值不必被视为静态的。 覆盖值是通过 Azure 策略提供的，并且不会影响创作或编译配置的方式。

Cmdlet `New-GuestConfigurationPolicy`，`Test-GuestConfigurationPolicyPackage` 包含一个名为**Parameters**的参数。 此参数采用哈希表定义，其中包括有关每个参数的所有详细信息，并自动创建用于创建每个 Azure 策略定义的文件的所有必需部分。

下面的示例将创建一个 Azure 策略用于审核服务，其中用户在策略分配时从服务列表中选择。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

对于 Linux 策略，请在配置中包含属性**AttributesYmlContent** ，并相应地覆盖值。 来宾配置代理会自动创建 InSpec 用于存储属性的 YaML 文件。 请参阅以下示例。

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

对于每个附加参数，将哈希表添加到该数组。 在策略文件中，你将看到添加到来宾配置 configurationName 的属性，这些属性标识资源类型、名称、属性和值。

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>发布到 Azure 策略

**GuestConfiguration**资源模块提供一种在 Azure 中创建策略定义和计划定义的方法，其中一个步骤通过 @no__t cmdlet 进行。
该 cmdlet 只有**Path**参数，该参数指向 `New-GuestConfigurationPolicy` 创建的三个 JSON 文件的位置。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

@No__t cmdlet 接受 PowerShell 管道中的路径。 此功能意味着您可以创建策略文件，并在一组管道命令中发布这些文件。

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

由于在 Azure 中创建了策略和计划定义，最后一步是分配计划。 请参阅如何在[门户](../assign-policy-portal.md)中分配计划、 [Azure CLI](../assign-policy-azurecli.md)和[Azure PowerShell](../assign-policy-powershell.md)。

> [!IMPORTANT]
> 必须**始终**使用结合了_AuditIfNotExists_和_DeployIfNotExists_策略的计划来分配来宾配置策略。 如果只分配了_AuditIfNotExists_策略，则不会部署系统必备组件，并且该策略始终显示 "0" 个服务器是相容的。

## <a name="policy-lifecycle"></a>策略生命周期

使用自定义内容包发布自定义 Azure 策略后，如果想要发布新版本，必须更新两个字段。

- **版本**：运行 @no__t cmdlet 时，必须指定一个大于当前发布的版本号。 属性更新新策略文件中的来宾配置分配的版本，以便扩展识别包已更新。
- **contentHash**：此属性由 @no__t cmdlet 自动更新。 它是 `New-GuestConfigurationPackage` 创建的包的哈希值。 对于您发布的 @no__t 0 文件，属性必须是正确的。 如果仅更新**contentUri**属性（例如，如果用户可以从门户手动更改策略定义），则扩展不会接受内容包。

发布更新包的最简单方法是重复本文中所述的过程，并提供更新的版本号。 该进程保证所有属性都已正确更新。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>将 Windows 组策略内容转换为 Azure 策略来宾配置

审核 Windows 计算机时，来宾配置是 PowerShell Desired 状态配置语法的实现。 DSC 社区已发布工具，用于将导出的组策略模板转换为 DSC 格式。 通过将此工具与上述来宾配置 cmdlet 结合使用，你可以转换 Windows 组策略内容并打包/发布它以供 Azure 策略审核。 有关使用该工具的详细信息，请参阅文章 [Quickstart：将组策略转换为 DSC @ no__t。
内容转换完成后，创建包并将其发布为 Azure 策略的步骤与任何 DSC 内容相同。

## <a name="optional-signing-guest-configuration-packages"></a>可选：为来宾配置包签名

默认情况下，来宾配置自定义策略使用 SHA256 哈希验证策略包在被审核的服务器读取时，是否从发布到该策略包的时间未更改。
客户还可以使用证书对包进行签名，并强制来宾配置扩展仅允许已签名的内容。

若要启用此方案，需要完成两个步骤。 运行 cmdlet 以对内容包进行签名，并将标记追加到需要对代码进行签名的计算机。

若要使用签名验证功能，请在发布包之前运行 @no__t cmdlet 对包进行签名。 此 cmdlet 需要 "代码签名" 证书。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

@No__t cmdlet 的参数：

- **路径**：来宾配置包的完整路径。
- **证书**：用于对包进行签名的代码签名证书。 仅当为 Windows 签名内容时才支持此参数。
- **PrivateGpgKeyPath**：私有 GPG 密钥路径。 仅在为 Linux 签名内容时才支持此参数。
- **PublicGpgKeyPath**：公共 GPG 密钥路径。 仅在为 Linux 签名内容时才支持此参数。

GuestConfiguration 代理要求在 Windows 计算机上的 "受信任的根证书颁发机构" 和 Linux 计算机上的路径 `/usr/local/share/ca-certificates/extra` 中提供证书公钥。 要使节点验证签名内容，请在应用自定义策略之前在计算机上安装证书公钥。 可以使用 VM 内的任何方法或使用 Azure 策略来完成此过程。 [此处提供](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)了一个示例模板。
Key Vault 访问策略必须允许计算资源提供程序在部署过程中访问证书。 有关详细步骤，请参阅[在 Azure 资源管理器中为虚拟机设置 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

下面是从签名证书导出公钥以导入到计算机的示例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

GitHub 上的一篇文章提供了一个用于创建用于 Linux 计算机的 GPG 密钥的好参考，其中[生成了新的 GPG 密钥](https://help.github.com/en/articles/generating-a-new-gpg-key)。

发布内容后，将名为 `GuestConfigPolicyCertificateValidation` 的标记和值 `enabled` 附加到需要进行代码签名的所有虚拟机。 可以使用 Azure 策略大规模传递此标记。 请参阅[应用标记及其默认值](../samples/apply-tag-default-value.md)示例。 完成此标记后，使用 @no__t cmdlet 生成的策略定义将通过来宾配置扩展启用要求。

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>效果来宾配置策略分配疑难解答

预览中提供了一个工具，用于帮助排查 Azure 策略来宾配置分配问题。 该工具处于预览阶段，已作为模块名称 "[来宾配置疑难解答](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)" 发布到 PowerShell 库。

有关此工具中的 cmdlet 的详细信息，请使用 PowerShell 中的 Get-help 命令显示内置指南。 当工具获取频繁更新时，这是获取最新信息的最佳方式。

## <a name="next-steps"></a>后续步骤

- 了解如何审核具有[来宾配置](../concepts/guest-configuration.md)的 vm。
- 了解如何以[编程方式创建策略](programmatically-create.md)。
- 了解如何[获取相容性数据](getting-compliance-data.md)。