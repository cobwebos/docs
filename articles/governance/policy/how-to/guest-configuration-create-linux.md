---
title: 如何创建适用于 Linux 的来宾配置策略
description: 了解如何创建适用于 Linux 的 Azure Policy 来宾配置策略。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: fef5bdea1b7f98e19f9f8ee8bc9bce8553107fda
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236584"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>如何创建适用于 Linux 的来宾配置策略

创建自定义策略前，请先阅读 [Azure Policy 来宾配置](../concepts/guest-configuration.md)中的概述信息。
 
若要了解如何创建适用于 Windows 的来宾配置策略，请参阅[如何创建适用于 Windows 的来宾配置策略](./guest-configuration-create.md)页

当审核 Linux 时，来宾配置使用 [Chef InSpec](https://www.inspec.io/)。 InSpec 配置文件定义了计算机应处于的条件。 如果配置评估失败，则会触发策略效果 auditIfNotExists，并将计算机视为不符合。

[Azure Policy 来宾配置](../concepts/guest-configuration.md)只能用于审核计算机内部的设置。 还不能修正计算机内部的设置。

请执行以下操作来创建你自己的配置，用于验证 Azure 或非 Azure 计算机的状态。

> [!IMPORTANT]
> 包含来宾配置的自定义策略是一项预览功能。
>
> 必须有来宾配置扩展，才能在 Azure 虚拟机中执行审核。
> 若要在所有 Linux 计算机上大规模部署此扩展，请分配以下策略定义：
>   - [部署必备组件以在 Linux VM 上启用 Guest Configuration 策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

来宾配置模块自动执行创建自定义内容的过程，包括：

- 创建来宾配置内容项目 (.zip)
- 自动测试项目
- 创建策略定义
- 发布策略

此模块可以安装在以下计算机上：运行 Windows、macOS 或 Linux，包含在本地运行的 PowerShell 6.2 或更高版本，或包含 [Azure Cloud Shell](https://shell.azure.com) 或 [Azure PowerShell Core Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 上不支持编译配置。

### <a name="base-requirements"></a>基本要求

可以安装模块的操作系统：

- Linux
- macOS
- Windows

> [!NOTE]
> Cmdlet "GuestConfigurationPackage" 需要 OpenSSL 版本1.0，因为对 OMI 有依赖关系。
> 这会导致 OpenSSL 1.1 或更高版本的任何环境出现错误。

来宾配置资源模块需要以下软件：

- PowerShell 6.2 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/scripting/install/installing-powershell)。
- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。
  - 只需要 AZ 模块“Az.Accounts”和“Az.Resources”。

### <a name="install-the-module"></a>安装模块

若要在 PowerShell 中安装 GuestConfiguration 模块，请执行以下操作：

1. 在 PowerShell 提示符下，运行以下命令：

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. 验证模块是否已导入：

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>适用于 Linux 的来宾配置项目和策略

即使在 Linux 环境中，来宾配置也使用 Desired State Configuration 作为语言抽象。 实现基于本机代码 (C++)，因此不需要加载 PowerShell。 不过，它需要描述环境详细信息的配置 MOF。 DSC 充当 InSpec 的包装器，用于标准化它的执行方式、参数提供方式，以及如何将输出返回到服务。 处理自定义 InSpec 内容时，需要掌握的 DSC 知识很少。

#### <a name="configuration-requirements"></a>配置要求

自定义配置的名称必须在所有位置都保持一致。 内容包的 .zip 文件名称、MOF 文件中的配置名称，以及 Azure 资源管理器模板（ARM 模板）中的来宾分配名称必须相同。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自定义来宾配置

Linux 上的来宾配置使用 `ChefInSpecResource` 资源为引擎提供 [InSpec 配置文件](https://www.inspec.io/docs/reference/profiles/)的名称。 “名称”是唯一必需的资源属性。 创建 YaML 文件和 Ruby 脚本文件，如下所详述。

首先，创建 InSpec 使用的 YaML 文件。 此文件提供了环境的基本信息。 下面给出了一个示例：

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

将这个名为 `inspec.yml` 的文件保存到项目目录中名为 `linux-path` 的文件夹。

接下来，使用用于审核计算机的 InSpec 语言抽象来创建 Ruby 文件。

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

将这个名为 `linux-path.rb` 的文件保存到 `linux-path` 目录内名为 `controls` 的新文件夹。

最后，创建一个配置，导入 PSDesiredStateConfiguration 资源模块，然后编译配置。

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

将这个名为 `config.ps1` 的文件保存到项目文件夹中。 通过在终端中执行 `./config.ps1`，在 PowerShell 中运行它。 将创建一个新的 mof 文件。

从技术上讲，`Node AuditFilePathExists` 命令不是必需的，但它会生成一个名为 `AuditFilePathExists.mof`（而不是默认的 `localhost.mof`）的文件。 让 .mof 文件名遵循配置，可以在大规模操作时轻松地组织许多文件。



你现在应该有如下所示的项目结构：

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

支持文件必须打包在一起。 来宾配置使用已完成的包来创建 Azure Policy 定义。

`New-GuestConfigurationPackage` cmdlet 创建包。 创建 Linux 内容时 `New-GuestConfigurationPackage` cmdlet 的参数：

- **Name**：来宾配置包名称。
- **配置**：已编译的配置文档完整路径。
- **路径**：输出文件夹路径。 此参数是可选的。 如果未指定，则在当前目录中创建包。
- ChefProfilePath：InSpec 配置文件的完整路径。 仅当创建内容来审核 Linux 时，才支持此参数。

运行下面的命令，以使用上一步中给出的配置来创建包：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

创建配置包后，但在将它发布到 Azure 之前，可以在工作站或 CI/CD 环境中测试包。 GuestConfiguration cmdlet `Test-GuestConfigurationPackage` 在开发环境中包含与 Azure 计算机内使用的相同的代理。 使用此解决方案，可以在发布到计费的云环境之前，在本地执行集成测试。

由于代理实际上是在评估本地环境，因此在大多数情况下，你需要在计划审核的同一 OS 平台上运行 Test- cmdlet。

`Test-GuestConfigurationPackage` cmdlet 的参数：

- **Name**：来宾配置策略名称。
- Parameter：以哈希表格式提供的策略参数。
- **路径**：来宾配置包的完整路径。

运行下面的命令，以测试由上一步创建的包：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

此 cmdlet 还支持来自 PowerShell 管道的输入。 将 `New-GuestConfigurationPackage` cmdlet 的输出通过管道传输到 `Test-GuestConfigurationPackage` cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

下一步是将文件发布到 Blob 存储。 下面的脚本包含可用于自动执行此任务的函数。 `publish` 函数中使用的命令需要 `Az.Storage` 模块。

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
```
在创建并上传来宾配置自定义策略包后，创建来宾配置策略定义。 `New-GuestConfigurationPolicy` cmdlet 需要使用自定义策略包，并创建策略定义。

`New-GuestConfigurationPolicy` cmdlet 的参数：

- ContentUri：来宾配置内容包的公共 http(s) URI。
- DisplayName：策略显示名称。
- **说明**：策略说明。
- Parameter：以哈希表格式提供的策略参数。
- **版本**：策略版本。
- **路径**：在其中创建策略定义的目标路径。
- Platform：来宾配置策略和内容包的目标平台 (Windows/Linux)。
- Tag 向策略定义添加一个或多个标记筛选器
- Category 在策略定义中设置类别元数据字段

下面的示例在自定义策略包的指定路径中创建策略定义：

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

`New-GuestConfigurationPolicy` 创建以下文件：

- auditIfNotExists.json
- deployIfNotExists.json
- Initiative.json

cmdlet 输出中会返回一个对象，其中包含策略文件的计划显示名称和路径。

最后，使用 `Publish-GuestConfigurationPolicy` cmdlet 发布策略定义。
cmdlet 只有 Path 参数，此参数指向 `New-GuestConfigurationPolicy` 创建的 JSON 文件的位置。

必须有权在 Azure 中创建策略，才能运行发布命令。 [Azure Policy 概述](../overview.md)页中收录了具体的授权要求。 最合适的内置角色是“资源策略参与者”。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy` cmdlet 接受来自 PowerShell 管道的路径。 此功能意味着可以创建策略文件，并在一组管道命令中发布它们。

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

在 Azure 中创建策略后，最后一步是分配计划。 请参阅“如何使用[门户](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 和 [Azure PowerShell](../assign-policy-powershell.md) 分配计划”。

> [!IMPORTANT]
> 必须始终使用组合 AuditIfNotExists 和 DeployIfNotExists 策略的计划来分配来宾配置策略。 如果只分配了 AuditIfNotExists 策略，则不会部署必备组件，并且策略始终显示“0”个服务器是符合的。

分配具有 DeployIfNotExists 效果的策略定义需要额外级别的访问权限。 若要授予最小特权，可以创建扩展“资源策略参与者”的自定义角色定义。 下面的示例创建具有额外权限 Microsoft.Authorization/roleAssignments/write 的“资源策略参与者 DINE”角色。

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>使用自定义来宾配置策略中的参数

来宾配置支持在运行时替代配置属性。 此功能意味着包中 MOF 文件内的值不必被认为是静态的。 替代值是通过 Azure Policy 提供的，并不影响配置的创作或编译方式。

借助 InSpec，参数通常在运行时作为输入处理，或使用特性作为代码处理。 来宾配置令此过程变得混淆，因此可以在分配策略时提供输入。 特性文件在计算机中自动创建。 不需要在项目中创建和添加文件。 向 Linux 审核项目添加参数需要执行两个步骤。

在编写要在计算机上审核什么的脚本的 Ruby 文件中定义输入。 下面给出了一个示例。

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含名为“Parameter”的参数。 此参数需要使用包含每个参数的所有详细信息的哈希表，并自动创建用于创建每个 Azure Policy 定义的文件的所有必需部分。

下面的示例创建策略定义来审核文件路径，其中用户在策略分配时提供路径。

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

对于 Linux 策略，请在配置中添加属性 AttributesYmlContent，并根据需要覆盖这些值。 来宾配置代理自动创建 InSpec 用于存储特性的 YAML 文件。 请参阅以下示例。

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>策略生命周期

若要发布策略定义的更新，需要注意以下两个字段。

- **版本**：运行 `New-GuestConfigurationPolicy` cmdlet 时，必须指定高于当前发布版本的版本号。 此属性更新来宾配置分配版本，这样代理就能识别更新后的包。
- contentHash：此属性由 `New-GuestConfigurationPolicy` cmdlet 自动更新。 它是 `New-GuestConfigurationPackage` 创建的包的哈希值。 对于你发布的 `.zip` 文件，此属性必须是正确的。 如果只更新了 contentUri 属性，扩展就不会接受内容包。

发布更新后的包的最简单方法是，重复本文中描述的过程，并提供更新后的版本号。 此过程保证所有属性都已正确更新。


### <a name="filtering-guest-configuration-policies-using-tags"></a>使用标记筛选来宾配置策略

来宾配置模块中由 cmdlet 创建的策略可以视需要选择包括标记筛选器。 `New-GuestConfigurationPolicy` 的 -Tag 参数支持包含各个标记条目的哈希表数组。 标记会被添加到策略定义的 `If` 部分，并且不能通过策略分配进行修改。

下面给出了筛选标记的策略定义的示例代码片段。

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>可选：对来宾配置包进行签名

来宾配置自定义策略使用 SHA256 哈希来验证策略包是否没有更改。
客户还可以选择使用证书对包进行签名，并强制来宾配置扩展只允许已签名的内容。

若要启用此方案，需要完成两个步骤。 运行 cmdlet 对内容包进行签名，并将标记追加到应需要对代码进行签名的计算机。

若要使用签名验证功能，请运行 `Protect-GuestConfigurationPackage` cmdlet，以在发布前对包进行签名。 此 cmdlet 需要“代码签名”证书。

`Protect-GuestConfigurationPackage` cmdlet 的参数：

- **路径**：来宾配置包的完整路径。
- PublicGpgKeyPath：公共 GPG 密钥路径。 只有在对 Linux 内容进行签名时，才支持此参数。

GitHub 上的文章[生成新 GPG 密钥](https://help.github.com/en/articles/generating-a-new-gpg-key)为创建用于 Linux 计算机的 GPG 密钥提供了很好的参考。

GuestConfiguration 代理需要在 Linux 计算机上的路径 `/usr/local/share/ca-certificates/extra` 中显示证书公钥。 为了让节点能够验证已签名的内容，请先在计算机上安装证书公钥，再应用自定义策略。 可以使用 VM 内的任何技术或使用 Azure Policy 来完成此过程。 [此处提供了](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)一个示例模板。
Key Vault 访问策略必须允许计算资源提供程序在部署过程中访问证书。 有关详细步骤，请参阅[在 Azure 资源管理器中为虚拟机设置 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

在内容发布后，将名为 `GuestConfigPolicyCertificateValidation` 且值为 `enabled` 的标记追加到所有应需要进行代码签名的虚拟机。 请参阅[标记示例](../samples/built-in-policies.md#tags)，了解如何使用 Azure Policy 大规模传递标记。 在此标记就位后，使用 `New-GuestConfigurationPolicy` cmdlet 生成的策略定义通过来宾配置扩展启用要求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>来宾配置策略分配故障排除（预览）

有一项工具处于预览状态，有助于对 Azure Policy 来宾配置分配进行故障排除。 此工具处于预览状态，已作为模块名称[来宾配置故障排除程序](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)发布到 PowerShell 库中。

若要详细了解此工具中的 cmdlet，请使用 PowerShell 中的 Get-Help 命令来显示内置的指导。 因为此工具经常更新，所以这是获取最新信息的最佳方式。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[来宾配置](../concepts/guest-configuration.md)审核 VM。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[获取符合性数据](get-compliance-data.md)。
