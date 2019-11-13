---
title: 如何创建 Guest Configuration 策略
description: 了解如何创建适用于 Windows 或 Linux VM 的 Azure Policy Guest Configuration 策略。
ms.date: 09/20/2019
ms.topic: conceptual
ms.openlocfilehash: 3c7b214a07b89f4b66aa32724259b01129b9b7e9
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959477"
---
# <a name="how-to-create-guest-configuration-policies"></a>如何创建 Guest Configuration 策略

Guest Configuration 使用 [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) 资源模块创建用于审核 Azure 计算机的配置。 DSC 配置定义计算机的应有状态。 如果配置评估失败，则会触发 Policy 效应 **auditIfNotExists**，并将计算机视为**不合规**。

[Azure Policy Guest Configuration](/azure/governance/policy/concepts/guest-configuration) 只可用于审核计算机内部的设置。 目前尚未提供修正计算机内部设置的功能。

使用以下操作创建自己的配置用于验证 Azure 计算机的状态。

> [!IMPORTANT]
> 使用 Guest Configuration 的自定义策略是一项预览版功能。

## <a name="add-the-guestconfiguration-resource-module"></a>添加 GuestConfiguration 资源模块

若要创建 Guest Configuration 策略，必须添加资源模块。 此资源模块可用于本地安装的 PowerShell、 [Azure Cloud Shell](https://shell.azure.com)或[Azure PowerShell Docker 映像](https://hub.docker.com/rsdk-powershell/)。

### <a name="base-requirements"></a>基本要求

Guest Configuration 资源模块需要以下软件：

- PowerShell。 若尚未安装，请遵循[这些说明](/powershell/scripting/install/installing-powershell)。
- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。

### <a name="install-the-module"></a>安装模块

Guest Configuration 使用 **GuestConfiguration** 资源模块创建 DSC 配置并将其发布到 Azure Policy：

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

为 Guest Configuration 创建自定义策略的第一步是创建 DSC 配置。 有关 DSC 的概念和术语概述，请参阅 [PowerShell DSC 概述](/powershell/scripting/dsc/overview/overview)。

如果你的配置只需要安装来宾配置代理的内置资源，则只需编写配置 MOF 文件。 如果需要运行其他脚本，则需要创作自定义资源模块。

### <a name="requirements-for-guest-configuration-custom-resources"></a>来宾配置自定义资源的要求

当来宾配置审核计算机时，它会首先运行 `Test-TargetResource` 以确定其是否处于正确的状态。 函数返回的布尔值确定来宾分配的 Azure 资源管理器状态是否应符合或不符合。 如果为配置中的任何资源 `$false` 了布尔值，则该提供程序将运行 `Get-TargetResource`。 如果布尔值为 `$true` 则 `Get-TargetResource` 不会调用。

函数 `Get-TargetResource` 对来宾配置具有特定的要求，这些要求对于 Windows Desired 状态配置没有必要。

- 返回的哈希表必须包含**一个名为的属性**。
- 原因属性必须是数组。
- 数组中的每一项应为一个具有名为**Code**和**短语**的键的哈希表。

"原因" 属性用于标准化计算机不符合时信息的显示方式。 可以将每个项视为一个 "原因"，因为资源不符合要求。 属性是一个数组，因为资源可能因多种原因而不符合要求。

服务需要属性**代码**和**短语**。 创作自定义资源时，请设置要显示的文本（通常为 stdout），因为该资源不符合**短语**的值。 **代码**具有特定的格式要求，因此报表可以清楚地显示用于执行审核的资源的相关信息。 此解决方案使来宾配置可扩展。 只要可以捕获输出并将其作为**短语**属性的字符串值返回，就可以运行任何命令来审核计算机。

- **代码**（string）：重复的资源的名称，然后是不包含空格作为原因标识符的短名称。 这三个值应以冒号分隔，不含空格。
  - 示例 `registry:registry:keynotpresent`
- **短语**（string）：可读文本，用于说明设置不符合的原因。
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

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自定义 Guest Configuration 配置

Linux 上的 Guest Configuration 的 DSC 配置使用 `ChefInSpecResource` 资源为引擎提供 [Chef InSpec](https://www.chef.io/inspec/) 定义的名称。 只有 **Name** 是必需的资源属性。

以下示例创建名为 **baseline** 的配置，导入 **GuestConfiguration** 资源模块，然后使用 `ChefInSpecResource` 资源将 InSpec 定义的名称设置为 **linux-patch-baseline**：

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

有关详细信息，请参阅[编写、编译和应用配置](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)。

### <a name="custom-guest-configuration-configuration-on-windows"></a>Windows 上的自定义 Guest Configuration 配置

Azure Policy Guest Configuration 的 DSC 配置仅由 Guest Configuration 代理使用，它不会与 Windows PowerShell Desired State Configuration 相冲突。

以下示例创建名为 **AuditBitLocker** 的配置，导入 **GuestConfiguration**  资源模块，然后使用 `Service` 资源来审核正在运行的服务：

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

有关详细信息，请参阅[编写、编译和应用配置](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)。

## <a name="create-guest-configuration-custom-policy-package"></a>创建 Guest Configuration 自定义策略包

编译 MOF 后，必须将支持文件打包在一起。 Guest Configuration 使用已完成的包来创建 Azure Policy 定义。 该包中包括：

- 用作 MOF 的已编译 DSC 配置
- 模块文件夹
  - GuestConfiguration 模块
  - DscNativeResources 模块
  - (Linux) 包含 Chef InSpec 定义和附加内容的文件夹
  - (Windows) 非内置的 DSC 资源模块

可以使用 `New-GuestConfigurationPackage` cmdlet 创建该包。 使用以下格式创建自定义包：

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

`New-GuestConfigurationPackage` cmdlet 的参数：

- **名称**：来宾配置包名称。
- **配置**：编译的 DSC 配置文档完整路径。
- **路径**：输出文件夹路径。 此参数是可选的。 如果未指定，将在当前目录中创建包。
- **ChefProfilePath**： InSpec 配置文件的完整路径。 仅当创建用于审核 Linux 的内容时才支持此参数。

完成的包必须存储在可由托管虚拟机访问的位置。 例如，存储在 GitHub 存储库、Azure 存储库或 Azure 存储中。 如果你不希望公开该包，可以在 URL 中包含 [SAS 令牌](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。 还可以针对专用网络中的计算机实施[服务终结点](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)，不过，这种配置仅适用于访问包，而不适用于与服务之间的通信。

### <a name="working-with-secrets-in-guest-configuration-packages"></a>处理 Guest Configuration 包中的机密

在 Azure Policy Guest Configuration 中，管理运行时使用的机密的最佳方式是将其存储在 Azure Key Vault 中。 此设计将在自定义 DSC 资源中实施。

1. 首先，在 Azure 中创建用户分配的托管标识。

   计算机将使用该标识来访问 Key Vault 中存储的机密。 有关详细步骤，请参阅[使用 Azure PowerShell 创建、列出或删除用户分配的托管标识](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)。

1. 创建 Key Vault 实例。

   有关详细步骤，请参阅[设置和检索机密 - PowerShell](../../../key-vault/quick-create-powershell.md)。
   分配对该实例的权限，使用户分配的标识能够访问 Key Vault 中存储的机密。 有关详细步骤，请参阅[设置和检索机密 - .NET](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault)。

1. 将用户分配的标识分配到计算机。

   有关详细步骤，请参阅[使用 PowerShell 在 Azure VM 上配置 Azure 资源的托管标识](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity)。
   使用 Azure 资源管理器通过 Azure Policy 大规模分配此标识。 有关详细步骤，请参阅[使用模板在 Azure VM 上配置 Azure 资源的托管标识](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm)。

1. 最后，在自定义资源中，使用前面生成的客户端 ID 通过计算机提供的令牌访问 Key Vault。

   可将 Key Vault 实例的 `client_id` 和 URL 作为[属性](/powershell/scripting/dsc/resources/authoringresourcemof#creating-the-mof-schema)传递给资源，这样，就不需要为多个环境更新资源，也不需要因为更改了值而更新资源。

可在自定义资源中使用以下代码示例，以使用用户分配的标识从 Key Vault 检索机密。 从请求返回到 Key Vault 的值为纯文本格式。 最佳做法是将其存储在某个 credential 对象中。

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>测试 Guest Configuration 包

创建配置包之后、将其发布到 Azure 之前，可以从工作站或 CI/CD 环境测试该包的功能。 GuestConfiguration 模块包含一个 `Test-GuestConfigurationPackage` cmdlet，用于将 Azure 计算机中所用的同一个代理载入开发环境。 使用此解决方案可以在发布到计费的测试/QA/生产环境之前，在本地执行集成测试。

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

`Test-GuestConfigurationPackage` cmdlet 的参数：

- **名称**：来宾配置策略名称。
- **参数**：以哈希表格式提供的策略参数。
- **路径**：来宾配置包的完整路径。

该 cmdlet 还支持来自 PowerShell 管道的输入。 通过管道将 `New-GuestConfigurationPackage` cmdlet 的输出传送到 `Test-GuestConfigurationPackage` cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

有关如何使用参数进行测试的详细信息，请参阅下一部分[使用自定义 Guest Configuration 策略中的参数](#using-parameters-in-custom-guest-configuration-policies)。

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>创建 Azure Policy 定义和计划部署文件

创建 Guest Configuration 自定义策略包并将其上传到计算机可访问的位置后，创建 Azure Policy 的 Guest Configuration 策略定义。 `New-GuestConfigurationPolicy` cmdlet 将提取可公开访问的 Guest Configuration 自定义策略包，并创建 **auditIfNotExists** 和 **deployIfNotExists** 策略定义。 另外还会创建包含这两个策略定义的策略计划定义。

以下示例通过适用于 Windows 的 Guest Configuration 自定义策略包在指定的路径中创建策略和计划定义，并提供名称、说明和版本：

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

`New-GuestConfigurationPolicy` cmdlet 的参数：

- **ContentUri**：来宾配置内容包的公共 http （s） uri。
- **DisplayName**：策略显示名称。
- **说明**：策略描述。
- **参数**：以哈希表格式提供的策略参数。
- **版本**：策略版本。
- **路径**：创建策略定义的目标路径。
- **平台**：适用于来宾配置策略和内容包的目标平台（Windows/Linux）。

`New-GuestConfigurationPolicy` 创建以下文件：

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

cmdlet 输出中会返回一个对象，其中包含策略文件的计划显示名称和路径。

若要使用此命令来构建自定义策略项目的基架，可对这些文件进行更改。 例如，修改“If”节以评估是否为计算机提供了特定的标记。 有关创建策略的详细信息，请参阅[以编程方式创建策略](./programmatically-create.md)。

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>使用自定义 Guest Configuration 策略中的参数

Guest Configuration 支持在运行时重写配置的属性。 此功能意味着，不一定要将包中 MOF 文件中的值视为静态值。 重写值是通过 Azure Policy 提供的，不会影响配置的创作或编译方式。

cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含名为 **Parameters** 的参数。 此参数采用哈希表定义（其中包含有关每个参数的所有详细信息），并自动创建全部所需的文件节来创建每个 Azure Policy 定义。

以下示例将创建一个 Azure 策略用于审核某个服务，在分配策略时，用户可从服务列表中进行选择。

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

对于 Linux 策略，请在配置中包含属性**AttributesYmlContent** ，并相应地覆盖值。 Guest Configuration 代理会自动创建供 InSpec 用来存储属性的 YaML 文件。 请参阅以下示例。

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

对于每个附加参数，请将一个哈希表添加到数组。 在策略文件中，将会看到已添加到 Guest Configuration configurationName 中的、用于标识资源类型、名称、属性和值的属性。

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

## <a name="publish-to-azure-policy"></a>发布到 Azure Policy

使用 **GuestConfiguration** 资源模块，只需通过 `Publish-GuestConfigurationPolicy` cmdlet 执行一个步骤，即可在 Azure 中创建策略定义和计划定义。
该 cmdlet 仅包含指向 **所创建的三个 JSON 文件的位置的**Path`New-GuestConfigurationPolicy` 参数。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

`Publish-GuestConfigurationPolicy` cmdlet 接受源自 PowerShell 管道的路径。 此功能意味着，可以在一组管道命令中创建并发布策略文件。

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

在 Azure 中创建策略和计划定义后，最后一步是分配计划。 请参阅如何使用[门户](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 和 [Azure PowerShell](../assign-policy-powershell.md) 分配计划。

> [!IMPORTANT]
> **始终**必须使用结合了 _AuditIfNotExists_ 和 _DeployIfNotExists_ 策略的计划来分配 Guest Configuration 策略。 如果仅分配 _AuditIfNotExists_ 策略，则不会部署必备组件，并且该策略始终显示“0”个服务器合规。

## <a name="policy-lifecycle"></a>策略生命周期

使用自定义内容包发布自定义 Azure 策略后，若要发布新版本，必须更新两个字段。

- **版本**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须指定一个大于当前发布的版本号。 该属性会更新新策略文件中的 Guest Configuration 分配版本，使扩展能够识别到包已更新。
- **contentHash**： `New-GuestConfigurationPolicy` cmdlet 自动更新此属性。 它是 `New-GuestConfigurationPackage` 创建的包的哈希值。 对于发布的 `.zip` 文件，该属性必须正确。 如果仅更新**contentUri**属性（例如，如果用户可以从门户手动更改策略定义），则扩展不会接受内容包。

发布已更新的包的最简单方法是重复本文所述的过程，并提供更新的版本号。 该过程可保证正确更新所有属性。

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>将 Windows 组策略内容转换为 Azure Policy Guest Configuration

审核 Windows 计算机时，Guest Configuration 是 PowerShell Desired State Configuration 语法的实现。 DSC 社区已发布相应的工具用于将导出的组策略模板转换为 DSC 格式。 结合上述 Guest Configuration cmdlet 使用此工具，可以转换 Windows 组策略内容和包，并将其发布以供 Azure Policy 审核。 有关使用该工具的详细信息，请参阅文章[快速入门：将组策略转换为 DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart)。
转换内容后，创建包并将其发布为 Azure 策略的步骤与处理任何 DSC 内容的相应步骤相同。

## <a name="optional-signing-guest-configuration-packages"></a>可选：为来宾配置包签名

默认情况下，来宾配置自定义策略使用 SHA256 哈希验证策略包在被审核的服务器读取时，是否从发布到该策略包的时间未更改。
客户还可以选择性地使用证书来为该包签名，并强制 Guest Configuration 扩展仅允许已签名的内容。

若要实现此方案，需要完成两个步骤。 运行 cmdlet 以便为内容包签名，并将一个标记追加到要求为代码签名的计算机。

若要使用签名验证功能，请在发布该包之前，运行 `Protect-GuestConfigurationPackage` cmdlet 为其签名。 此 cmdlet 需要“代码签名”证书。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` cmdlet 的参数：

- **路径**：来宾配置包的完整路径。
- **证书**：用于对包进行签名的代码签名证书。 仅当为适用于 Windows 的内容签名时才支持此参数。
- **PrivateGpgKeyPath**：私有 GPG 密钥路径。 仅当为适用于 Linux 的内容签名时才支持此参数。
- **PublicGpgKeyPath**：公共 GPG 密钥路径。 仅当为适用于 Linux 的内容签名时才支持此参数。

GuestConfiguration 代理要求证书公钥在 Windows 计算机上的“受信任的根证书颁发机构”和 Linux 计算机上的 `/usr/local/share/ca-certificates/extra` 路径中存在。 要使节点能够验证已签名的内容，请在应用自定义策略之前在计算机上安装证书公钥。 可以使用 VM 中的任何技术或使用 Azure Policy 来完成此过程。 [此处提供](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)了一个示例模板。
Key Vault 访问策略必须允许计算资源提供程序在部署期间访问证书。 有关详细步骤，请参阅[在 Azure 资源管理器中为虚拟机设置 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

下面是从签名证书导出公钥并将其导入计算机的示例。

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

GitHub 上的[生成新的 GPG 密钥](https://help.github.com/en/articles/generating-a-new-gpg-key)一文中全面介绍了如何创建可在 Linux 计算机上使用的 GPG 密钥。

发布内容后，将名为 `GuestConfigPolicyCertificateValidation`、值为 `enabled` 的标记追加到需要代码签名的所有虚拟机。 可以使用 Azure Policy 大规模传送此标记。 请参阅[应用标记及其默认值](../samples/apply-tag-default-value.md)示例。 追加此标记后，使用 `New-GuestConfigurationPolicy` cmdlet 生成的策略定义可通过 Guest Configuration 扩展来满足要求。

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>[预览] 排查 Guest Configuration 策略分配问题

我们已提供一个预览版工具用于帮助排查 Azure Policy Guest Configuration 分配问题。 该工具目前为预览版，已发布到 PowerShell 库，其名称为 [Guest Configuration 故障排除工具](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)。

有关此工具中的 cmdlet 的详细信息，请在 PowerShell 中使用 Get-Help 命令显示内置的指导。 在该工具的频繁更新过程中，此命令是获取最新信息的最佳方式。

## <a name="next-steps"></a>后续步骤

- 了解如何使用 [Guest Configuration](../concepts/guest-configuration.md) 审核 VM。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[获取合规性数据](getting-compliance-data.md)。