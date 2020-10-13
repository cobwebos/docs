---
title: 如何从 Windows 组策略基线创建来宾配置策略定义
description: 了解如何将 Windows Server 2019 安全基线中的组策略转换为策略定义。
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: dce22885981ab01fe37fac8588899d12a5afb87d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893367"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>如何从 Windows 组策略基线创建来宾配置策略定义

创建自定义策略定义之前，最好参阅 [Azure Policy 来宾配置](../concepts/guest-configuration.md)中的概念性概述信息。 若要了解如何创建适用于 Linux 的自定义来宾配置策略定义，请参阅[如何创建适用于 Linux 的来宾配置策略](./guest-configuration-create-linux.md)。 若要了解如何创建适用于 Windows 的自定义来宾配置策略定义，请参阅[如何创建适用于 Windows 的来宾配置策略](./guest-configuration-create.md)。

审核 Windows 时，来宾配置使用 [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) 资源模块创建配置文件。 DSC 配置定义了计算机应处于的条件。 如果配置评估为“不合规”，则会触发策略效果 auditIfNotExists。
[Azure Policy 来宾配置](../concepts/guest-configuration.md)仅审核计算机内部的设置。

> [!IMPORTANT]
> 具有来宾配置的自定义策略定义是一项预览功能。
>
> 必须有来宾配置扩展，才能在 Azure 虚拟机中执行审核。 若要在所有 Windows 计算机上大规模部署此扩展，请分配以下策略定义：
> - [部署必备组件以在 Windows VM 上启用 Guest Configuration 策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

DSC 社区已发布 [BaselineManagement 模块](https://github.com/microsoft/BaselineManagement)，以将导出的组策略模板转换为 DSC 格式。 BaselineManagement 模块与 GuestConfiguration cmdlet 一起为 Windows 组策略内容创建 Azure Policy 来宾配置包。 有关使用 BaselineManagement 模块的详细信息，请参阅文章[快速入门：将组策略转换为 DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart)。

在本指南中，我们将逐步介绍从组策略对象 (GPO) 创建 Azure Policy 来宾配置包的过程。 虽然本演练概述了 Windows Server 2019 安全基线的转换，但该过程也可以应用于其他 GPO。  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>下载 Windows Server 2019 安全基线并安装相关的 PowerShell 模块

在 PowerShell 中安装“DSC”、“GuestConfiguration”、“基线管理”和相关的 Azure 模块  ：

1. 在 PowerShell 提示符下，运行以下命令：

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. 创建目录并从 Windows 安全合规性工具包下载 Windows Server 2019 安全基线。

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. 取消阻止并展开下载的 Server 2019 基线。

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. 使用 MapGuidsToGpoNames.ps1 验证 Server 2019 基线内容。

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>从组策略转换为 Azure Policy 来宾配置

接下来，使用来宾配置和基线管理模块，将下载的 Server 2019 基线转换为来宾配置包。

1. 使用基线管理模块将组策略转换为 Desired State Configuration。

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. 在创建策略内容包之前，重命名、重格式化并运行转换后的脚本。

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. 创建 Azure Policy 来宾配置内容包。

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>创建 Azure Policy 来宾配置

下一步是将文件发布到 Azure Blob 存储。 

1. 下面的脚本包含可用于自动执行此任务的函数。 请注意，`publish` 函数中使用的命令需要 `Az.Storage` 模块。

   ```azurepowershell-interactive
    function Publish-Configuration {
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
   ```

1. 创建参数以定义唯一的资源组、存储帐户和容器。 
   
   ```azurepowershell-interactive
    # Replace the $resourceGroup, $storageAccount, and $storageContainer values below.
    $resourceGroup = 'rfc_customguestconfig'
    $storageAccount = 'guestconfiguration'
    $storageContainer = 'content'
    $path = 'c:\git\policyfiles\Server2019Baseline\Server2019Baseline.zip'
    $blob = 'Server2019Baseline.zip' 
    ```

1. 将 publish 函数与已分配参数一起使用，以将来宾配置包发布到公共 Blob 存储。


   ```azurepowershell-interactive
   $PublishConfigurationSplat = @{
       resourceGroup = $resourceGroup
       storageAccountName = $storageAccount
       storageContainerName = $storageContainer
       filePath = $path
       blobName = $blob
       FullUri = $true
   }
   $uri = Publish-Configuration @PublishConfigurationSplat
    ```
1. 在创建并上传来宾配置自定义策略包后，创建来宾配置策略定义。 使用 `New-GuestConfigurationPolicy` cmdlet 创建来宾配置。

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. 使用 `Publish-GuestConfigurationPolicy` cmdlet 发布策略定义。 cmdlet 只有 Path 参数，此参数指向 `New-GuestConfigurationPolicy` 创建的 JSON 文件的位置。 若要运行 Publish 命令，需要具有在 Azure 中创建策略定义的访问权限。 [Azure Policy 概述](../overview.md#getting-started)页中收录了具体的授权要求。 最合适的内置角色是“资源策略参与者”。

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>分配来宾配置策略定义

在 Azure 中创建策略后，最后一步是分配计划。 请参阅“如何使用[门户](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 和 [Azure PowerShell](../assign-policy-powershell.md) 分配计划”。

> [!IMPORTANT]
> 来宾配置策略定义必须始终使用结合了 AuditIfNotExists 和 DeployIfNotExists 策略的计划进行分配 。 如果只分配了 AuditIfNotExists 策略，则不会部署必备组件，并且策略始终显示“0”个服务器是符合的。

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

## <a name="next-steps"></a>后续步骤

- 了解如何使用[来宾配置](../concepts/guest-configuration.md)审核 VM。
- 了解如何[以编程方式创建策略](./programmatically-create.md)。
- 了解如何[获取符合性数据](./get-compliance-data.md)。
