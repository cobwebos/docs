---
title: 将项目存储库添加到自己的实验室中 Azure 开发测试实验室 |Microsoft Docs
description: 了解如何将项目存储库添加到自己的实验室中 Azure 开发测试实验室。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: c391aa157e35bdc389bd30efe48fa380d06c193e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508356"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>将项目存储库添加到自己的实验室中开发测试实验室
开发测试实验室，可指定要在创建 VM 或之后创建 VM 时添加到 VM 的项目。 此项目可能是一种工具或你想要在 VM 上安装的应用程序。 从 GitHub 或 Azure DevOps Git 存储库加载的 JSON 文件中定义的项目。 

[公共项目存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)，维护的开发测试实验室，适用于 Windows 和 Linux 提供许多常用工具。 此存储库的链接自动添加到你的实验室。 可以使用公共项目存储库中不可用的特定工具创建自己的项目存储库。 若要了解如何创建自定义项目，请参阅[创建自定义项目](devtest-lab-artifact-author.md)。

本文介绍如何使用 Azure 门户、 Azure 资源管理模板和 Azure PowerShell 添加自定义项目存储库。 可以自动将项目存储库添加到实验室，通过编写 PowerShell 或 CLI 脚本。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件
要将存储库添加到实验室，必须先从存储库获取某些信息。 以下部分介绍如何获取所需的信息的存储库上托管**GitHub**或**Azure DevOps**。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>获取 GitHub 存储库克隆 URL 和个人访问令牌

1. 转到包含项目定义或资源管理器模板定义的 GitHub 存储库主页。
2. 选择“克隆或下载”  。
3. 要将 URL 复制到剪贴板，请选择“HTTPS 克隆 URL”  按钮。 保存该 URL 以供将来使用。
4. 选择 GitHub 右上角的配置文件图像，然后选择“设置”  。
5. 在中**个人设置**左侧，选择菜单上的**开发人员设置**。
6. 选择**个人访问令牌**在左侧菜单中。
7. 选择“生成新令牌”  。
8. 在“新建个人访问令牌”页上，在“令牌描述”下输入相关描述   。 接受“选择范围”下的默认项，然后选择“生成令牌”   。
9. 保存生成的令牌。 稍后会用到该令牌。
10. 关闭 GitHub。   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>获取 Azure Repos 克隆 URL 和个人访问令牌
1. 转到团队集合主页（例如 https://contoso-web-team.visualstudio.com) ） ，并选择项目。
2. 在项目主页上，选择“代码”  。
3. 若要查看克隆 URL，在项目“代码”  页上，选择“克隆”  。
4. 保存 URL。 稍后会用到该 URL。
5. 若要创建个人访问令牌，在用户帐户下拉列表菜单中选择“我的个人资料”  。
6. 在个人资料信息页中，选择“安全性”  。
7. 在“安全性”  选项卡上，选择“添加”  。
8. 在“创建个人访问令牌”  页中：
   1. 输入令牌的**说明**。
   2. 选择“有效期”列表中的“180 天”   。
   3. 选择“帐户”列表中的“所有可访问的帐户”   。
   4. 选择“所有范围”选项  。
   5. 选择“创建令牌”  。
9. 随后“个人访问令牌”列表中会出现新的令牌  。 选择“复制令牌”  ，并保存令牌值以备后用。
10. 转到连接到存储库部分实验室。

## <a name="use-azure-portal"></a>使用 Azure 门户
本部分提供在项目存储库中将添加到 Azure 门户中的步骤。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，并从服务列表中选择“开发测试实验室”   。
3. 在实验室列表中，选择实验室。 
4. 选择**配置和策略**在左侧菜单中。
5. 选择**存储库**下**外部资源**在左侧菜单中的部分。
6. 选择 **+ 添加**工具栏上。

    ![“添加存储库”按钮](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 上**存储库**页上，指定以下信息：
   1. **名称**。 输入存储库的名称。
   2. **Git 克隆 URL**。 输入之前从 GitHub 或 Azure DevOps Services 复制的 Git HTTPS 克隆 URL。
   3. **分支**。 输入分支以获取定义。
   4. **个人访问令牌**。 输入之前从 GitHub 或 Azure DevOps Services 获取的个人访问令牌。
   5. **文件夹路径**。 输入至少一个与包含项目定义或资源管理器模板定义的克隆 URL 有关的文件夹路径。 指定子目录时，请确保在文件夹路径中包含正斜杠。

        ![“存储库”区域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. 选择“保存”。 

## <a name="use-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
Azure 资源管理 （Azure 资源管理器） 模板是描述您想要创建的 Azure 中的资源的 JSON 文件。 有关这些模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。

本部分提供使用 Azure 资源管理器模板将项目存储库添加到实验室的步骤。  如果它尚不存在，则该模板创建实验室。 

### <a name="template"></a>模板
在本文中所使用的示例模板通过参数收集以下信息。 大多数参数具有智能默认值，但有几个必须指定的值。 必须指定实验名称、 项目存储库，URI 和存储库的安全令牌。 

- 实验室名称。
- 在项目存储库，开发测试实验室用户界面 (UI) 中的显示名称。 默认值是： `Team Repository`。
- 向存储库的 URI (示例：`https://github.com/<myteam>/<nameofrepo>.git`或`"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。
- 包含项目的存储库中的分支。 默认值是： `master`。
- 包含项目的文件夹的名称。 默认值是： `/Artifacts`。
- 存储库的类型。 允许的值为`VsoGit`或`GitHub`。
- 存储库的访问令牌。 

    ```json
    {
    
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>部署模板
有几种方法将模板部署到 Azure 并让资源创建，如果不存在，或更新，如果不存在。 有关详细信息，请参阅以下文章：

- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)
- [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [使用 Resource Manager 模板和 Azure 门户部署资源](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](../azure-resource-manager/resource-group-template-deploy-rest.md)

让我们继续并了解如何部署在 PowerShell 中的模板。 用于部署模板的 Cmdlet 是特定于上下文，因此使用当前租户和当前订阅。 使用[集 AzContext](/powershell/module/az.accounts/set-azcontext)然后再部署该模板后，如果需要若要更改上下文。

首先，创建资源组使用[新建 AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)。 如果你想要已使用的资源组存在，则跳过此步骤。

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

接下来，创建到资源组使用的部署[新建 AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。 此 cmdlet 适用于 Azure 资源更改。 多个资源部署可以对任何给定的资源组。 如果要多次部署到同一资源组，请确保每个部署的名称是唯一的。

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

新建 AzResourceGroupDeployment 成功运行后，命令输出 （应成功） 的预配状态和任何输出模板等重要信息。
 
## <a name="use-azure-powershell"></a>使用 Azure PowerShell 
本部分提供了可用于将项目存储库添加到实验室的示例 PowerShell 脚本。 如果没有 Azure PowerShell，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0)有关详细的说明进行安装。

### <a name="full-script"></a>完整脚本
下面是完整的脚本，包括一些详细消息和注释：

**新 DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab. 

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>

 
[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',
    
    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,
    
    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"
 
Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>运行 PowerShell 脚本
下面的示例演示了如何运行脚本： 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>parameters
在本文中的示例 PowerShell 脚本采用以下参数：

| 参数 | 描述 | 
| --------- | ----------- | 
| LabName | 在实验室的名称。 |
| ArtifactRepositoryName | 新的项目存储库的名称。 如果未指定，该脚本将创建存储库的随机名称。 |
| ArtifactRepositoryDisplayName | 在项目存储库的显示名称。 这是在 Azure 门户中显示的名称 (https://portal.azure.com) 查看某个实验室的所有项目存储库时。 |
| RepositoryUri | 向存储库的 Uri。 示例：`https://github.com/<myteam>/<nameofrepo>.git`或`"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。| 
| RepositoryBranch | 文件可以找到哪些项目中的分支。 默认值为 master。 | 
| FolderPath | 可以在其下找到项目的文件夹。 默认为 / 项目的 |
| PersonalAccessToken | 用于访问 GitHub 或 VSOGit 存储库的安全令牌。 请参阅先决条件部分的说明来获取个人访问令牌 |
| SourceType | 项目是否 VSOGit 或 GitHub 存储库。 |

存储库本身需要内部名称标识，它将不同，在 Azure 门户中看到的显示名称。 您看不到使用 Azure 门户的内部名称，但使用 Azure REST Api 或 Azure PowerShell 时，会看到它。 此脚本提供一个名称，如果未指定脚本的用户。

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>在脚本中使用的 PowerShell 命令

| PowerShell 命令 | 说明 |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 使用此命令以获取有关实验室的详细信息，如其位置。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 没有用于添加项目存储库特定命令。 泛型[新建 AzResource](/powershell/module/az.resources/new-azresource) cmdlet 完成这项任务。 此 cmdlet 需要**ResourceId**或**ResourceName**并**ResourceType**对知道要创建的资源类型。 此示例脚本使用的资源名称和资源类型对。 <br/><br/>请注意，在同一位置中并与实验室在同一资源组下创建项目存储库源。|

该脚本将新的资源添加到当前订阅。 使用[Get AzContext](/powershell/module/az.accounts/get-azcontext)若要查看此信息。 使用[集 AzContext](/powershell/module/az.accounts/set-azcontext)若要设置的当前租户和订阅。

发现的资源名称和资源类型信息的最佳方式是使用[测试驱动器 Azure REST Api](https://azure.github.io/projects/apis/)网站。 请查看[开发测试实验室-2016年-05-15](https://aka.ms/dtlrestapis)提供程序，若要查看可用的 REST Api 的开发测试实验室提供程序。 脚本用户以下资源 id。 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
资源类型为 providers 在 URI 中，大括号中列出的项除外后列出的所有内容。 资源名称是在大括号中看到的所有内容。 如果多个项应为资源名称，以斜杠分隔每一项为我们完成。 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>后续步骤
- [在 Azure 开发测试实验室中指定为实验室强制性项目](devtest-lab-mandatory-artifacts.md)
- [创建开发测试实验室虚拟机的自定义项目](devtest-lab-artifact-author.md)
- [诊断在实验室中的项目失败](devtest-lab-troubleshoot-artifact-failure.md)

