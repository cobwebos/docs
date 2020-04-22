---
title: 如何创建适用于 Linux 的 Guest Configuration 策略
description: 了解如何创建适用于 Linux 的 Azure Policy Guest Configuration 策略。
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24442a89d55e34f9ce9697c2f6a32cfc740bcd85
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758961"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>如何创建适用于 Linux 的 Guest Configuration 策略

在创建自定义策略之前，请阅读 [Azure Policy Guest Configuration](../concepts/guest-configuration.md) 中的概述信息。
 
若要了解如何创建适用于 Windows 的 Guest Configuration 策略，请参阅[如何创建适用于 Windows 的 Guest Configuration 策略](./guest-configuration-create.md)页

审核 Linux 时，Guest Configuration 将使用 [Chef InSpec](https://www.inspec.io/)。 InSpec 配置文件定义计算机应该所处的状态。 如果配置评估失败，则会触发策略效应 auditIfNotExists，并将计算机视为不合规。  

[Azure Policy Guest Configuration](../concepts/guest-configuration.md) 只可用于审核计算机内部的设置。 目前尚未提供修正计算机内部设置的功能。

使用以下操作创建自己的配置用于验证 Azure 或非 Azure 计算机的状态。

> [!IMPORTANT]
> 使用 Guest Configuration 的自定义策略是一项预览版功能。
>
> 在 Azure 虚拟机中执行审核需要来宾配置扩展。
> 要大规模部署扩展，请分配以下策略定义：
>   - 部署必备组件以在 Windows VM 上启用 Guest Configuration 策略
>   - 部署必备组件以在 Linux VM 上启用 Guest Configuration 策略

## <a name="install-the-powershell-module"></a>安装 Powershell 模块

使用 PowerShell 中的来宾配置模块，创建来宾配置项目、自动测试项目、创建策略定义和发布策略是完全可自动的。 该模块可以安装在运行 Windows、macOS 或 Linux 的计算机上，其中 PowerShell 6.2 或更高版本在本地运行，或者使用[Azure 云外壳](https://shell.azure.com)，或者使用[Azure PowerShell 核心 Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell-core)。

> [!NOTE]
> Linux 上不支持配置编译。

### <a name="base-requirements"></a>基本要求

可安装模块的操作系统：

- Linux
- macOS
- Windows

Guest Configuration 资源模块需要以下软件：

- PowerShell 6.2 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/scripting/install/installing-powershell)。
- Azure PowerShell 1.5.0 或更高版本。 若尚未安装，请遵循[这些说明](/powershell/azure/install-az-ps)。
  - 只需要 AZ 模块"Az.帐户"和"Az.Resources"。

### <a name="install-the-module"></a>安装模块

要在 PowerShell 中安装**来宾配置**模块，请执行以下操作：

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Linux 的来宾配置工件和策略

即使在 Linux 环境中，来宾配置也使用所需的状态配置作为语言抽象。 实现基于本机代码 （C++），因此不需要加载 PowerShell。 但是，它确实需要配置 MOF 描述有关环境的详细信息。 DSC 充当 InSpec 的包装器，以标准化其执行方式、参数的提供方式以及输出如何返回到服务。 使用自定义 InSpec 内容时，不需要对 DSC 了解甚少。

#### <a name="configuration-requirements"></a>配置要求

自定义配置的名称必须在所有位置保持一致。 内容包的 .zip 文件的名称、MOF 文件中的配置名称以及资源管理器模板中的来宾分配名称必须相同。

### <a name="custom-guest-configuration-configuration-on-linux"></a>Linux 上的自定义 Guest Configuration 配置

Linux 上的来宾配置使用`ChefInSpecResource`资源向引擎提供[InSpec 配置文件](https://www.inspec.io/docs/reference/profiles/)的名称。 只有 **Name** 是必需的资源属性。 创建 YaML 文件和 Ruby 脚本文件，详情如下。

首先，创建 InSpec 使用的 YaML 文件。 该文件提供有关环境的基本信息。 下面给出了一个示例：

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

将具有名称`inspec.yml`的文件保存到项目目录中指定的`linux-path`文件夹。

接下来，使用用于审核计算机的 InSpec 语言抽象创建 Ruby 文件。

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

将具有名称`linux-path.rb`的文件保存在`controls``linux-path`目录中命名的新文件夹中。

最后，创建配置、导入**PS希望状态配置**资源模块并编译配置。

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

将此文件与名称`config.ps1`保存在项目文件夹中。 通过在终端中执行`./config.ps1`在 PowerShell 中运行它。 将创建新的 mof 文件。

该`Node AuditFilePathExists`命令在技术上不是必需的，但它生成名为`AuditFilePathExists.mof`而不是默认值的文件。 `localhost.mof` 使 .mof 文件名遵循配置，因此在大规模运行时很容易组织许多文件。



现在，您应该有如下项目结构：

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

支持文件必须打包在一起。 Guest Configuration 使用已完成的包来创建 Azure Policy 定义。

可以使用 `New-GuestConfigurationPackage` cmdlet 创建该包。 创建 Linux`New-GuestConfigurationPackage`内容时的 cmdlet 参数：

- **名称**：来宾配置包名称。
- **配置**：编译的配置文档完整路径。
- **路径**：输出文件夹路径。 此参数是可选的。 如果未指定，将在当前目录中创建包。
- **Chef 配置文件路径**：通往 InSpec 配置文件的完整路径。 仅当创建用于审核 Linux 的内容时才支持此参数。

运行以下命令，使用上一步骤中给出的配置创建包：

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

创建配置包后，但在将其发布到 Azure 之前，可以从工作站或 CI/CD 环境中测试该包。 Guest 配置 cmdlet`Test-GuestConfigurationPackage`在开发环境中包括与 Azure 计算机中使用的代理相同的代理。 使用此解决方案，您可以在本地执行集成测试，然后再发布到计费的云环境。

由于代理实际上是在评估本地环境，因此在大多数情况下，您需要在计划审核的同一操作系统平台上运行 Test-cmdlet。

`Test-GuestConfigurationPackage` cmdlet 的参数：

- **名称**：来宾配置策略名称。
- **参数**：以可哈希格式提供的策略参数。
- **路径**：来宾配置包的完整路径。

运行以下命令以测试上一步骤创建的包：

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

该 cmdlet 还支持来自 PowerShell 管道的输入。 通过管道将 `New-GuestConfigurationPackage` cmdlet 的输出传送到 `Test-GuestConfigurationPackage` cmdlet。

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

下一步是将文件发布到 Blob 存储。 下面的脚本包含可用于自动执行此任务的函数。 `publish`函数中使用的命令需要模块`Az.Storage`。

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
创建并上载来宾配置自定义策略包后，创建来宾配置策略定义。 cmdlet`New-GuestConfigurationPolicy`采用自定义策略包并创建策略定义。

`New-GuestConfigurationPolicy` cmdlet 的参数：

- **内容Uri**：来宾配置内容包的公共 http（s） uri。
- **显示名称**：策略显示名称。
- **说明**：策略说明。
- **参数**：以可哈希格式提供的策略参数。
- **版本**：策略版本。
- **路径**：创建策略定义的目标路径。
- **平台**：用于来宾配置策略和内容包的目标平台（Windows/Linux）。

以下示例从自定义策略包创建指定路径中的策略定义：

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

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

cmdlet 输出中会返回一个对象，其中包含策略文件的计划显示名称和路径。

最后，使用`Publish-GuestConfigurationPolicy`cmdlet 发布策略定义。
cmdlet 仅具有**指向**由 创建的`New-GuestConfigurationPolicy`JSON 文件的位置的路径参数。

要运行"发布"命令，需要访问才能在 Azure 中创建策略。 特定的授权要求记录在 Azure[策略概述](../overview.md)页中。 最好的内置角色是**资源策略参与者**。

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy` cmdlet 接受源自 PowerShell 管道的路径。 此功能意味着，可以在一组管道命令中创建并发布策略文件。

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

在 Azure 中创建策略后，最后一步是分配计划。 请参阅如何使用[门户](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 和 [Azure PowerShell](../assign-policy-powershell.md) 分配计划。

> [!IMPORTANT]
> **始终**必须使用结合了 _AuditIfNotExists_ 和 _DeployIfNotExists_ 策略的计划来分配 Guest Configuration 策略。 如果仅分配 _AuditIfNotExists_ 策略，则不会部署必备组件，并且该策略始终显示“0”个服务器合规。

使用_DeployIfNotExists_效果分配策略定义需要额外的访问级别。 要授予最低权限，您可以创建扩展**资源策略参与者**的自定义角色定义。 下面的示例创建一个名为 **"资源策略参与者 DINE"** 的角色，该角色具有_Microsoft.授权/角色分配/写入_的其他权限。

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>使用自定义 Guest Configuration 策略中的参数

Guest Configuration 支持在运行时重写配置的属性。 此功能意味着，不一定要将包中 MOF 文件中的值视为静态值。 重写值是通过 Azure Policy 提供的，不会影响配置的创作或编译方式。

使用 InSpec 时，参数通常在运行时作为输入处理，或者使用属性作为代码处理。 来宾配置混淆了此过程，以便在分配策略时提供输入。 属性文件在计算机中自动创建。 您无需在项目中创建和添加文件。 向 Linux 审核项目添加参数有两个步骤。

定义 Ruby 文件中的输入，在其中编写要在计算机上审核的内容。 下面给出了一个示例。

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

cmdlet `New-GuestConfigurationPolicy` 和 `Test-GuestConfigurationPolicyPackage` 包含名为 **Parameters** 的参数。 此参数采用哈希表，包括有关每个参数的所有详细信息，并自动创建用于创建每个 Azure 策略定义的文件的所有必需部分。

下面的示例创建一个策略定义来审核文件路径，用户在分配策略时提供路径。

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
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

对于 Linux 策略，请在配置中包括**属性属性属性属性YmlContent，** 并根据需要覆盖值。 来宾配置代理会自动创建 InSpec 用于存储属性的 YAML 文件。 请参阅以下示例。

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

要发布策略定义的更新，需要注意两个字段。

- **版本**：运行`New-GuestConfigurationPolicy`cmdlet 时，必须指定大于当前发布的版本号的版本号。 属性更新来宾配置分配的版本，以便代理识别更新的包。
- **内容哈希**：此属性由`New-GuestConfigurationPolicy`cmdlet 自动更新。 它是 `New-GuestConfigurationPackage` 创建的包的哈希值。 对于发布的 `.zip` 文件，该属性必须正确。 如果仅更新**内容 Uri**属性，则扩展不会接受内容包。

发布已更新的包的最简单方法是重复本文所述的过程，并提供更新的版本号。 该过程可保证正确更新所有属性。

## <a name="optional-signing-guest-configuration-packages"></a>可选：对来宾配置包进行签名

来宾配置自定义策略使用 SHA256 哈希来验证策略包未更改。
客户还可以选择性地使用证书来为该包签名，并强制 Guest Configuration 扩展仅允许已签名的内容。

若要实现此方案，需要完成两个步骤。 运行 cmdlet 以便为内容包签名，并将一个标记追加到要求为代码签名的计算机。

若要使用签名验证功能，请在发布该包之前，运行 `Protect-GuestConfigurationPackage` cmdlet 为其签名。 此 cmdlet 需要“代码签名”证书。

`Protect-GuestConfigurationPackage` cmdlet 的参数：

- **路径**：来宾配置包的完整路径。
- **公共GpgKeyPath：** 公共GPG密钥路径。 仅当为适用于 Linux 的内容签名时才支持此参数。

GitHub 上的[生成新的 GPG 密钥](https://help.github.com/en/articles/generating-a-new-gpg-key)一文中全面介绍了如何创建可在 Linux 计算机上使用的 GPG 密钥。

Guest 配置代理希望证书公钥存在于 Linux 计算机的`/usr/local/share/ca-certificates/extra`路径中。 要使节点能够验证已签名的内容，请在应用自定义策略之前在计算机上安装证书公钥。 可以使用 VM 中的任何技术或使用 Azure Policy 来完成此过程。 [此处提供](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)了一个示例模板。
Key Vault 访问策略必须允许计算资源提供程序在部署期间访问证书。 有关详细步骤，请参阅[在 Azure 资源管理器中为虚拟机设置 Key Vault](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)。

发布内容后，将名为 `GuestConfigPolicyCertificateValidation`、值为 `enabled` 的标记追加到需要代码签名的所有虚拟机。 有关如何使用 Azure 策略大规模交付标记，请参阅[标记示例](../samples/built-in-policies.md#tags)。 追加此标记后，使用 `New-GuestConfigurationPolicy` cmdlet 生成的策略定义可通过 Guest Configuration 扩展来满足要求。

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>排除来宾配置策略分配（预览）

我们已提供一个预览版工具用于帮助排查 Azure Policy Guest Configuration 分配问题。 该工具目前为预览版，已发布到 PowerShell 库，其名称为 [Guest Configuration 故障排除工具](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)。

有关此工具中的 cmdlet 的详细信息，请在 PowerShell 中使用 Get-Help 命令显示内置的指导。 在该工具的频繁更新过程中，此命令是获取最新信息的最佳方式。

## <a name="next-steps"></a>后续步骤

- 了解如何使用 [Guest Configuration](../concepts/guest-configuration.md) 审核 VM。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[获取合规性数据](get-compliance-data.md)。
