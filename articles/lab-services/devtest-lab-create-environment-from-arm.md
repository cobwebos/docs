---
title: 使用模板创建多 VM 环境和 PaaS 资源
description: 了解如何在 Azure 开发测试实验室中通过 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169637"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源

Azure 开发人员测试实验室环境允许用户在实验室范围内以一致的方式轻松部署复杂的基础结构。 可以使用[Azure 资源管理器模板](../azure-resource-manager/templates/template-syntax.md)使用 DevTest 实验室中的资源集创建环境。 这些环境可以包含资源管理器模板可以创建的任何 Azure 资源。

通过使用[Azure 门户](https://portal.azure.com)，一[次可以轻松地将一个虚拟机 （VM） 添加到](devtest-lab-add-vm.md)实验室。 但是，多层 Web 应用或 SharePoint 服务器场等方案需要一种机制，在单个步骤中创建多个 VM。 通过使用 Azure 资源管理器模板，可以定义 Azure 解决方案的基础结构和配置，并反复部署处于一致状态的多个 VM。

Azure 资源管理器模板还提供以下优点：

- Azure 资源管理器模板直接从 GitHub 或 Azure 存储库源代码管理存储库加载。
- 用户可以通过从 Azure 门户中选取已配置的 Azure 资源管理器模板来创建环境，就像使用其他类型的 VM[基](devtest-lab-comparing-vm-base-image-types.md)一样。
- 可以从 Azure 资源管理器模板预配 Azure PaaS 资源以及环境中的 IaaS VM。
- 除了其他类型的基创建的各个 VM 之外，您还可以跟踪实验室中环境的成本。 PaaS 资源将创建并显示在成本跟踪中。 不过，VM 自动关闭不适用于 PaaS 资源。

要了解有关使用资源管理器模板在单个操作中部署、更新或删除许多实验室资源的好处的详细信息，请参阅[使用资源管理器模板的好处](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)。

> [!NOTE]
> 使用资源管理器模板作为创建实验室 VM 的基础时，创建多个 VM 或单个 VM 之间存在一些差异。 有关详细信息，请参阅[使用虚拟机的 Azure 资源管理器模板](devtest-lab-use-resource-manager-template.md)。
>

## <a name="use-devtest-labs-public-environments"></a>使用开发人员测试实验室公共环境
Azure 开发人员测试实验室具有[Azure 资源管理器模板的公共存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments)，可用于创建环境，而无需自己连接到外部 GitHub 源。 此公共存储库类似于 Azure 门户中为所创建的每个实验室提供的项目的公共存储库。 通过环境存储库，您可以快速使用具有很少输入参数的预创作环境模板。 这些模板为实验室内的 PaaS 资源提供了平稳的入门体验。

在公共存储库中，DevTest Labs 团队和其他团队创建并共享了常用模板，如 Azure Web 应用、服务结构群集和开发 SharePoint 服务器场环境。 您可以直接使用这些模板，也可以自定义这些模板以满足您的需要。 有关详细信息，请参阅[在开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)。 创建自己的模板后，可以将其存储在此存储库中，以便与他人共享它们，或设置自己的 Git 存储库。

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>创建您自己的模板存储库

作为基础架构即代码和配置即代码的最佳做法之一，您应该在源代码管理中管理环境模板。 Azure 开发人员测试实验室遵循此做法，直接从 GitHub 或 Azure 存储库存储库加载所有 Azure 资源管理器模板。 因此，您可以在整个发布周期（从测试环境到生产环境）使用资源管理器模板。

在存储库中组织 Azure 资源管理器模板需要遵循以下几种规则：

- 您必须命名主模板文件*azuredeploy.json*。

- 如果要使用参数文件中定义的参数值，参数文件必须命名为*azuredeploy.parameter.json*。

  可以使用参数 `_artifactsLocation` 和 `_artifactsLocationSasToken` 来构造 parametersLink URI 值，以允许开发测试实验室自动管理嵌套模板。 有关详细信息，请参阅[部署嵌套 Azure 资源管理器模板以测试环境](deploy-nested-template-environments.md)。

- 可以定义元数据以在名为*metadata.json*的文件中指定模板显示名称和说明，如下所示：

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![关键的 Azure 资源管理器模板文件](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>将模板存储库添加到实验室

创建和配置存储库后，可以使用 Azure 门户将其添加到实验室：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 选择“所有服务”****，并从列表中选择“开发测试实验室”****。
1. 从实验室列表中，选择所需的实验室。
1. 在实验室的 **"概述"** 窗格中，选择 **"配置"和"策略**"。

   ![配置和策略](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. 从 **"配置和策略**设置"列表中，选择**存储库**。 **公共项目存储库**存储库将自动生成所有实验室，并连接到[DevTest Labs 公共 GitHub 存储库](https://github.com/Azure/azure-devtestlab)。

1. 要添加 Azure 资源管理器模板存储库，请选择"**添加**"。

   ![公共存储库](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. 在 **"存储库"** 窗格中，输入以下信息：

   - **名称**：输入要在实验室中使用的存储库名称。
   - **Git 克隆 URL**： 从 GitHub 或 Azure 存储库输入 Git HTTPS 克隆 URL。
   - **分支**（可选）：输入分支名称以访问 Azure 资源管理器模板定义。
   - **个人访问令牌**：输入用于安全访问存储库的个人访问令牌。
     - 要从 Azure 存储库获取令牌，请在配置文件下选择 **"用户设置** > **安全** > **个人访问令牌**"。
     - 要从 GitHub 获取令牌，请在配置文件下选择 **"** > **设置开发人员设置** > **个人访问令牌**"。
   - **文件夹路径**：输入与 Git 克隆 URI 相关的文件夹路径，用于项目定义或 Azure 资源管理器模板定义。

1. 选择“保存”。****

   ![添加新存储库](./media/devtest-lab-create-environment-from-arm/repo-values.png)

将 Azure 资源管理器模板添加到实验室后，实验室用户可以使用该模板创建环境。

## <a name="configure-access-rights-for-lab-users"></a>为实验室用户配置访问权限

默认情况下，实验室用户具有**Reader**角色，因此无法更改环境资源组中的资源。 例如，他们无法停止或启动其资源。

要向实验室用户**提供参与者**角色，以便他们可以编辑其环境中的资源，请按照以下步骤操作：

1. 在[Azure 门户](https://portal.azure.com)中，在实验室的 **"概述"** 窗格中，选择 **"配置"和"策略**"，然后选择 **"实验室设置**"。

1. 在 **"实验室设置"** 窗格中，选择 **"参与者**"，然后选择 **"保存"** 以向实验室用户授予写入权限。

   ![可配置的实验室用户访问权限](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

下一节将介绍从 Azure 资源管理器模板创建环境。

## <a name="create-environments-from-templates-in-the-azure-portal"></a>从 Azure 门户中的模板创建环境

将 Azure 资源管理器模板添加到实验室后，实验室用户可以按照以下步骤在 Azure 门户中创建环境：

1. 登录到 Azure[门户](https://portal.azure.com)。

1. 选择“所有服务”****，并从列表中选择“开发测试实验室”****。

1. 从实验室列表中，选择所需的实验室。

1. 在实验室的页面上，选择 **"添加**"。

1. **"选择基本**窗格"显示可以使用的基本映像，首先列出 Azure 资源管理器模板。 选择所需的 Azure 资源管理器模板。

   ![选择一个库](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. 在 **"添加**"窗格中，输入要向环境用户显示**的环境名称**值。

   Azure 资源管理器模板定义输入字段的其余部分。 如果模板*azuredeploy.parameter.json*文件定义默认值，则输入字段将显示这些值。

   对于类型*安全字符串*的参数，可以使用 Azure 密钥保管库中的秘密。 要了解如何在密钥保管库中存储机密并在创建实验室资源时使用它们，请参阅在[Azure 密钥保管库中存储机密](devtest-lab-store-secrets-in-key-vault.md)。  

   ![添加窗格](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 即使模板指定了以下参数值，输入字段中也不会显示以下参数值。 相反，窗体显示空白输入字段，其中实验室用户必须在创建环境时输入值。
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. 选择“添加”创建环境。****

   环境立即开始预配，状态将显示在 **"我的虚拟机"** 列表中。 该实验室会自动创建一个新资源组，以预配 Azure 资源管理器模板中定义的所有资源。

1. 创建环境后，选择 **"我的虚拟机**"列表中的环境以打开资源组窗格并浏览环境预配的所有资源。

   ![环境资源](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   您还可以扩展环境，以便只查看环境预配的 VM 列表。

   ![“我的虚拟机”列表](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 选择要查看可用操作的任何环境，例如应用工件、附加数据磁盘、更改自动关闭时间等。

   ![环境操作](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>使用 PowerShell 自动创建环境

使用 Azure 门户将单个环境添加到实验室是可行的，但当开发或测试方案必须创建多个环境时，自动部署是一种更好的体验。

在继续操作之前，请确保具有定义要创建的资源的 Azure 资源管理器模板。 [在 Git 存储库中添加和配置模板](#configure-your-own-template-repositories)，并将[存储库添加到实验室](#add-template-repositories-to-the-lab)。

以下示例脚本在实验室中创建环境。 注释可帮助您更好地了解脚本。

1. 将以下示例 PowerShell 脚本作为*deployenv.ps1*保存到硬盘。

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. 使用订阅 Id、LabName、资源组名称、存储库名称、模板名称（Git 存储库中的文件夹）和环境名称的特定值运行脚本，如下所示。

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

您还可以使用 Azure CLI 使用资源管理器模板部署资源。 有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)。

> [!NOTE]
> 只有具有实验室所有者权限的用户才能使用 Azure PowerShell 根据 Resource Manager 模板创建 VM。 如果要使用资源管理器模板自动创建 VM，并且只有用户权限，则可以使用 CLI 命令[az 实验室 vm 创建](/cli/azure/lab/vm#az-lab-vm-create)。

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>开发人员测试实验室中的资源管理器模板限制

在开发人员测试实验室中使用资源管理器模板时，请考虑这些限制：

- 资源管理器模板不能引用大多数现有资源。 他们只能创建新资源。 如果您有在引用现有资源的 DevTest Labs 之外使用的资源管理器模板，则不能在 DevTest 实验室中使用它们。 唯一的例外是可以引用现有的虚拟网络。

- 不能从从资源管理器模板创建的实验室 VM 创建公式或自定义映像。

- 部署资源管理器模板时，不会评估大多数策略。

  例如，您可能具有一个实验室策略，用户可以只创建五个 VM。 但是，用户可以部署创建数十个 VM 的资源管理器模板。 未评估的策略包括：

  - 每个用户的 VM 数

  - 每个实验室用户的高级 VM 数

  - 每个实验室用户的高级磁盘数

## <a name="next-steps"></a>后续步骤
- 创建 VM 后，可以通过在 VM 的管理窗格中选择 **"连接**"连接到 VM。
- 在实验室的“我的虚拟机”列表中选择环境，查看和管理该环境中的资源****。
- 从[Azure 快速入门模板库浏览 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates)。
