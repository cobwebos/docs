---
title: 在 Azure 开发测试实验室中将项目存储库添加到实验室 |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中将项目存储库添加到实验室。
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
ms.openlocfilehash: 28ab6ca9b87bb00cbb7b5e329b7ff08972ba370a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979132"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>在开发测试实验室中将项目存储库添加到实验室
开发测试实验室允许指定在创建 VM 时或创建 VM 之后要添加到 VM 的项目。 此项目可能是要在 VM 上安装的工具或应用程序。 项目在从 GitHub 或 Azure DevOps Git 存储库加载的 JSON 文件中定义。

开发测试 Labs 维护的[公共项目存储库](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)提供了许多适用于 Windows 和 Linux 的常用工具。 此存储库的链接会自动添加到你的实验室。 你可以创建自己的项目存储库，其中包含公共项目存储库中未提供的特定工具。 若要了解如何创建自定义项目，请参阅[创建自定义项目](devtest-lab-artifact-author.md)。

本文提供了有关如何使用 Azure 门户、Azure 资源管理模板和 Azure PowerShell 添加自定义项目存储库的信息。 可以通过编写 PowerShell 或 CLI 脚本自动将项目存储库添加到实验室。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件
要将存储库添加到实验室，必须先从存储库获取某些信息。 以下部分介绍如何获取在**GitHub**或**Azure DevOps**上托管的存储库所需的信息。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>获取 GitHub 存储库克隆 URL 和个人访问令牌

1. 转到包含项目定义或资源管理器模板定义的 GitHub 存储库主页。
2. 选择“克隆或下载”。
3. 要将 URL 复制到剪贴板，请选择“HTTPS 克隆 URL”按钮。 保存该 URL 以供将来使用。
4. 选择 GitHub 右上角的配置文件图像，然后选择“设置”。
5. 在左侧的 "**个人设置**" 菜单中，选择 "**开发人员设置**"。
6. 在左侧菜单中选择 "**个人访问令牌**"。
7. 选择“生成新令牌”。
8. 在“新建个人访问令牌”页上，在“令牌描述”下输入相关描述。 接受“选择范围”下的默认项，然后选择“生成令牌”。
9. 保存生成的令牌。 稍后会用到该令牌。
10. 关闭 GitHub。   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>获取 Azure Repos 克隆 URL 和个人访问令牌
1. 转到团队集合主页（例如 https://contoso-web-team.visualstudio.com) ） ，并选择项目。
2. 在项目主页上，选择“代码”。
3. 若要查看克隆 URL，在项目“代码”页上，选择“克隆”。
4. 保存 URL。 稍后会用到该 URL。
5. 若要创建个人访问令牌，在用户帐户下拉列表菜单中选择“我的个人资料”。
6. 在个人资料信息页中，选择“安全性”。
7. 在“安全性”选项卡上，选择“添加”。
8. 在“创建个人访问令牌”页中：
   1. 输入令牌的**说明**。
   2. 选择“有效期”列表中的“180 天”。
   3. 选择“帐户”列表中的“所有可访问的帐户”。
   4. 选择“所有范围”选项。
   5. 选择“创建令牌”。
9. 随后“个人访问令牌”列表中会出现新的令牌。 选择“复制令牌”，并保存令牌值以备后用。
10. 继续转到将实验室连接到存储库部分。

## <a name="use-azure-portal"></a>使用 Azure 门户
本部分提供将项目存储库添加到 Azure 门户中的实验室的步骤。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，并从服务列表中选择“开发测试实验室”。
3. 在实验室列表中，选择实验室。
4. 在左侧菜单中选择 "**配置和策略**"。
5. 在左侧菜单中选择 "**外部资源**" 下的 "**存储库**"。
6. 在工具栏上选择 " **+ 添加**"。

    ![“添加存储库”按钮](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. 在 "**存储库**" 页上，指定下列信息：
   1. **名称**。 输入存储库的名称。
   2. **Git 克隆 URL**。 输入之前从 GitHub 或 Azure DevOps Services 复制的 Git HTTPS 克隆 URL。
   3. **分支**。 输入分支以获取定义。
   4. **个人访问令牌**。 输入之前从 GitHub 或 Azure DevOps Services 获取的个人访问令牌。
   5. **文件夹路径**。 输入至少一个与包含项目定义或资源管理器模板定义的克隆 URL 有关的文件夹路径。 指定子目录时，请确保在文件夹路径中包含正斜杠。

        ![“存储库”区域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. 选择“保存”。

## <a name="use-azure-resource-manager-template"></a>使用 Azure 资源管理器模板
Azure 资源管理（Azure 资源管理器）模板是描述 Azure 中要创建的资源的 JSON 文件。 有关这些模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/templates/template-syntax.md)。

本部分提供使用 Azure 资源管理器模板将项目存储库添加到实验室的步骤。  如果该模板尚不存在，则创建该模板。

### <a name="template"></a>模板
本文中使用的示例模板通过参数收集以下信息。 大多数参数都具有智能默认设置，但必须指定一些值。 必须为项目存储库指定实验室名称和 URI，并为存储库指定安全令牌。

- 实验室名称。
- 开发测试实验室用户界面（UI）中的项目存储库的显示名称。 默认值为： `Team Repository`。
- 存储库的 URI （例如： `https://github.com/<myteam>/<nameofrepo>.git` 或 `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。
- 包含项目的存储库中的分支。 默认值为： `master`。
- 包含项目的文件夹的名称。 默认值为： `/Artifacts`。
- 存储库的类型。 允许的值为 `VsoGit` 或 `GitHub`。
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
有几种方法可以将模板部署到 Azure，并创建资源（如果不存在）或更新（如果存在）。 有关详细信息，请参阅以下文章：

- [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)
- [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)
- [使用 Resource Manager 模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)
- [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)

接下来，了解如何在 PowerShell 中部署模板。 用于部署模板的 cmdlet 是特定于上下文的，因此使用当前租户和当前订阅。 如果需要，可以在部署模板之前使用[AzContext](/powershell/module/az.accounts/set-azcontext)来更改上下文。

首先，使用[AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)创建资源组。 如果要使用的资源组已存在，请跳过此步骤。

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

接下来，使用[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)创建资源组部署。 此 cmdlet 将资源更改应用到 Azure。 可以对任何给定的资源组进行多个资源部署。 如果要在同一资源组中部署多次，请确保每个部署的名称都是唯一的。

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

AzResourceGroupDeployment 成功运行后，该命令将输出预配状态（应为 "已成功"）和模板的任何输出等重要信息。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本部分提供了一个示例 PowerShell 脚本，可用于将项目存储库添加到实验室。 如果没有 Azure PowerShell，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) ，以了解有关安装的详细说明。

### <a name="full-script"></a>完整脚本
下面是完整的脚本，包括一些详细的消息和注释：

**New-DevTestLabArtifactRepository**：

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
下面的示例演示如何运行脚本：

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>参数
本文中的示例 PowerShell 脚本采用以下参数：

| 参数 | Description |
| --------- | ----------- |
| LabName | 实验室的名称。 |
| ArtifactRepositoryName | 新项目存储库的名称。 如果未指定存储库，该脚本将为其创建一个随机名称。 |
| ArtifactRepositoryDisplayName | 项目存储库的显示名称。 这是在查看实验室的所有项目存储库时，Azure 门户中显示的名称（ https://portal.azure.com) 。 |
| RepositoryUri | 存储库的 Uri。 示例： `https://github.com/<myteam>/<nameofrepo>.git` 或 `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。|
| RepositoryBranch | 可在其中找到项目文件的分支。 默认值为 "master"。 |
| FolderPath | 可在其下找到项目的文件夹。 默认为 "/Artifacts" |
| PersonalAccessToken | 用于访问 GitHub 或 VSOGit 存储库的安全令牌。 有关获取个人访问令牌的说明，请参阅先决条件部分。 |
| SourceType | 项目是否为 VSOGit 或 GitHub 存储库。 |

存储库本身需要标识的内部名称，这不同于在 Azure 门户中显示的显示名称。 使用 Azure 门户看不到内部名称，但在使用 Azure REST Api 或 Azure PowerShell 时，会看到该名称。 如果脚本的用户未指定名称，该脚本将提供一个名称。

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>脚本中使用的 PowerShell 命令

| PowerShell 命令 | 说明 |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 此命令用于获取有关实验室的详细信息，例如其位置。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 没有用于添加项目存储库的特定命令。 一般的[AzResource](/powershell/module/az.resources/new-azresource) cmdlet 执行该作业。 此 cmdlet 需要**ResourceId**或资源类型和**ResourceType** **对，以**了解要创建的资源类型。 此示例脚本使用资源名称和资源类型对。 <br/><br/>请注意，要在与实验室相同的资源组下创建项目存储库源。|

此脚本将新资源添加到当前订阅。 若要查看此信息，请使用[AzContext](/powershell/module/az.accounts/get-azcontext) 。 使用[AzContext](/powershell/module/az.accounts/set-azcontext)设置当前租户和订阅。

了解资源名称和资源类型信息的最佳方式是使用[AZURE REST api](https://azure.github.io/projects/apis/)网站。 请查看[开发测试实验室– 2016-05-15](https://aka.ms/dtlrestapis)提供商，查看适用于开发测试实验室提供商的可用 REST api。 脚本用户以下资源 ID。

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

资源类型是在 URI 中的 "提供程序" 之后列出的所有内容（在大括号中列出的项除外）。 资源名称是在大括号中显示的所有内容。 如果资源名称需要多个项，请将每个项用斜杠隔开，如我们所做的那样。

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>后续步骤
- [在 Azure 开发测试实验室中指定实验室的强制项目](devtest-lab-mandatory-artifacts.md)
- [为开发测试实验室虚拟机创建自定义项目](devtest-lab-artifact-author.md)
- [诊断实验室中的项目失败](devtest-lab-troubleshoot-artifact-failure.md)
