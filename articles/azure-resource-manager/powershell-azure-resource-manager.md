---
title: "将 Azure PowerShell 与 Resource Manager 配合使用 | Microsoft Docs"
description: "介绍如何使用 Azure PowerShell 将作为资源组的多个资源部署到 Azure。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 83f2818873e2716193004bf1cc2b9da4da360f17


---
# <a name="using-azure-powershell-with-azure-resource-manager"></a>将 Azure PowerShell 与 Azure 资源管理器配合使用
> [!div class="op_single_selector"]
> * [门户](resource-group-portal.md) 
> * [Azure CLI](xplat-cli-azure-resource-manager.md)
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [REST API](resource-manager-rest-api.md)
> 
> 

Azure Resource Manager 采用现代方法来控制 Azure 资源生命周期。 首先应构想整个解决方案，而不是创建和管理博客、照片库、SharePoint 门户或 wiki 等各个资源。 可利用模板（即解决方案的声明性表示形式）定义包含支持解决方案时所需资源的资源组。 然后，可以将该资源组作为逻辑单元进行部署和管理。 

在本教程中，你将了解如何将 Azure PowerShell 与 Azure 资源管理器配合使用。 其中会引导你完成部署解决方案，以及使用该解决方案的过程。 你将使用 Azure PowerShell 和 Resource Manager 模板来部署：

* SQL 服务器 - 用于托管数据库
* SQL 数据库 - 用于存储数据
* 防火墙规则 - 允许 Web 应用连接到数据库
* App Service 计划 - 用于定义 Web 应用的功能和成本
* 网站 - 用于运行 Web 应用
* Web 配置 - 用于存储数据库的连接字符串 
* 警报规则 - 用于监视性能和错误
* App Insights - 用于自动缩放设置

若要获取 Azure PowerShell ，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。

## <a name="get-help-for-cmdlets"></a>获取有关 cmdlet 的帮助
若要获得您在此教程中看到的任何 cmdlet 的详细帮助，请使用 Get-Help cmdlet。 

    Get-Help <cmdlet-name> -Detailed

例如，若要获取有关 Get-AzureRmResource cmdlet 的帮助，请键入：

    Get-Help Get-AzureRmResource -Detailed

若要获取带有帮助摘要的资源模块中的 cmdlet 列表，请键入： 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

输出与以下摘要类似：

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

若要获得 cmdlet 的完整帮助，请使用以下格式键入命令：

    Get-Help <cmdlet-name> -Full

## <a name="login-to-your-azure-account"></a>登录到你的 Azure 帐户
在处理解决方案之前，你必须登录到你的帐户。

请使用 **Add-AzureRmAccount** cmdlet 登录到 Azure 帐户。

    Add-AzureRmAccount

该 cmdlet 将提示您提供您的 Azure 帐户的登录凭据。 登录后它会下载你的帐户设置，以便这些信息可供 Azure PowerShell 使用。 

帐户设置会过期，因此您需要不时刷新它们。 若要刷新帐户设置，请再次运行 **Add-AzureRmAccount**。 

> [!NOTE]
> Resource Manager 模块要求使用 Add-AzureRmAccount。 一个发布设置文件是不够的。     
> 
> 

如果具有多个订阅，请提供要通过 **Set-AzureRmContext** cmdlet 用于部署的订阅 ID。

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>创建资源组
必须先创建将包含资源的资源组，才能向订阅部署任何资源。 

请使用 **New-AzureRmResourceGroup** cmdlet 创建资源组。

该命令使用 **Name** 参数指定资源组的名称，并使用 **Location** 参数指定其位置。 根据上一部分中所述，我们将使用“美国西部”作为位置。

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"

输出结果将会类似于：

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

现已成功创建资源组。

## <a name="deploy-your-solution"></a>部署你的解决方案
本主题不会说明如何创建模板，也不会介绍模板的结构。 相关信息，请参阅[创作 Azure Resource Manager 模板](resource-group-authoring-templates.md) 和 [Resource Manager 模板演示](resource-manager-template-walkthrough.md)。 需在 [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates/)中部署预定义的[预配包含 SQL 数据库的 Web 应用](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)模板。

在创建资源组和模板后，可以将模板中定义的基础结构部署到资源组。 通过 **New-AzureRmResourceGroupDeployment** cmdlet 部署。 该模板指定了我们要使用的许多默认值，因此你不需要提供这些参数的值。 基本语法如下：

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

指定资源组及模板的位置。 如果模板为本地文件，请使用 **-TemplateFile** 参数并指定模板路径。 可将 **-Mode** 参数设置为 **Incremental** 或 **Complete**。 默认情况下，Resource Manager 会在部署期间执行增量更新；因此，执行**增量**更新时无需设置 **-Mode**。 若要了解这些部署模式之间的区别，请参阅[通过 Azure Resource Manager 模板部署应用程序](resource-group-template-deploy.md)。 

### <a name="dynamic-template-parameters"></a>动态模板参数
如果你熟悉 PowerShell 的话，就知道你可以通过键入减号 (-) 并按 TAB 键来切换 cmdlet 的可用参数。 对于模板中定义的参数，同样也可以使用此功能。 只要你键入模板名称，该 cmdlet 就会提取该模板、对其进行分析并将模板参数动态地添加到该命令。 这使指定模板参数值变得非常轻松。

输入命令时，系统会提示缺少必需参数 **administratorLoginPassword**。 并且，当您键入密码时，将遮盖安全字符串值。 此策略可以消除以纯文本格式提供密码的风险。

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

如果模板中某参数的名称与部署模板时所用的命令中的某参数名称匹配（例如，模板中包括名为 **ResourceGroupName** 的参数，这与 [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment) cmdlet 中的 **ResourceGroupName** 参数名称相同），则系统将提示对后缀为 **FromTemplate ** 的参数（如 **ResourceGroupNameFromTemplate**）提供值。 通常，为避免此类混乱，不应按部署操作所用的参数名称命令参数。

创建资源时，该命令将会运行并返回消息。 最终，你将看到部署结果。

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net

    DeploymentDebugLogLevel :

在几个步骤中，我们将创建和部署复杂网站所需的资源。 

### <a name="log-debug-information"></a>日志调试信息
模板运行期间，可在运行 **New-AzureRmResourceGroupDeployment** 时指定 **-DeploymentDebugLogLevel** 参数，记录有关请求和响应的额外信息。 此信息可帮助排查部署错误。 默认值为“无”，即不记录任何请求或响应内容。 可指定记录请求和/或响应中的内容。  若要深入了解如何排查部署问题和记录调试信息，请参阅 [使用 Azure PowerShell 排除资源组部署问题](resource-manager-troubleshoot-deployments-powershell.md)。 以下示例将记录部署的请求内容和响应内容。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [!NOTE]
> 设置 DeploymentDebugLogLevel 参数时，请仔细考虑在部署期间传入的信息类型。 通过记录有关请求或响应的信息，可能会公开通过部署操作检索的机密数据。 
> 
> 

## <a name="get-information-about-your-resource-groups"></a>获取有关资源组的信息
创建资源组之后，可以使用资源管理器模块中的 cmdlet 来管理该资源组。

* 若要获取订阅中的资源组，请使用 **Get-AzureRmResourceGroup** cmdlet：
  
        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
  
    这将返回以下信息：
  
        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
  
        ...
  
    如果未指定资源组名称，该 cmdlet 将返回订阅中的所有资源组。
* 若要获取资源组中的资源，请使用 **Find-AzureRmResource** cmdlet 及其 **ResourceGroupNameContains** 参数。 若不带参数，Find-AzureRmResource 会获取 Azure 订阅中的所有资源。
  
        Find-AzureRmResource -ResourceGroupNameContains TestRG1
  
     将返回采用以下格式的资源列表：
  
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
* 可以使用标记以逻辑方式组织订阅中的资源，并使用 **Find-AzureRmResource** 和 **Find-AzureRmResourceGroup** cmdlet 检索资源。
  
        Find-AzureRmResource -TagName displayName -TagValue Website
  
        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
  
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>添加到资源组
若要将资源添加到资源组，可以使用 **New-AzureRmResource** cmdlet。 但是，以这种方式添加资源可能会在将来造成混淆，因为模板中不存在新资源。 如果重新部署旧模板，则会部署不完整的解决方案。 如果你经常要部署模板，就会发现更方便且更可靠的方法是将新资源添加到模板，然后重新部署模板。

## <a name="move-a-resource"></a>移动资源
可以将现有资源移动到新的资源组。 有关示例，请参阅[将资源移动到新的资源组或订阅中](resource-group-move-resources.md)。

## <a name="export-template"></a>导出模板
对于通过 PowerShell 或门户等其他方法部署的现有资源组，可查看资源组的 Resource Manager 模板。 导出模板有两个好处：

1. 由于所有基础结构都已定义于模板中，因此可以轻松地自动进行解决方案的将来部署。
2. 可以查看代表解决方案的 JavaScript 对象表示法 (JSON)，以此熟悉模板语法。

通过 PowerShell，可以生成代表资源组当前状态的模板，或检索特定部署所用的模板。

已更改资源组，并且需要检索其当前状态的 JSON 表示法时，导出资源组的模板很有用。 但是，生成的模板只包含最少的参数数目，但不包含任何变量。 模板中大部分的值为硬编码。 在部署所生成的模板之前，你可能想要将更多的值转换成参数，以便针对不同的环境自定义部署。

当你需要查看用于部署资源的实际模板时，针对特定部署导出模板很有用。 模板包含针对原始部署定义的所有参数和变量。 但是，如果组织中有人已更改非此模板中定义的资源组，此模板并不代表资源组的当前状态。

> [!NOTE]
> 导出模板功能处于预览状态，并非所有的资源类型目前都支持导出模板。 尝试导出模板时，你可能会看到一个错误，指出未导出某些资源。 如果需要，你可以在下载模板之后，在模板中手动定义这些资源。
> 
> 

### <a name="export-template-from-resource-group"></a>从资源组导出模板
若要查看资源组的模板，请运行 **Export-AzureRmResourceGroup** cmdlet。

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json

### <a name="download-template-from-deployment"></a>从部署下载模板
若要下载用于特定部署的模板，请运行 **Save-AzureRmResourceGroupDeploymentTemplate** cmdlet。

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>删除资源或资源组
* 若要从资源组中删除资源，请使用 **Remove-AzureRmResource** cmdlet。 此 cmdlet 将删除该资源，但不会删除该资源组。
  
    此命令从 TestRG1 资源组中删除 TestSite 网站。
  
        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01
* 若要删除资源组，请使用 **Remove-AzureRmResourceGroup** cmdlet。 此 cmdlet 将删除资源组及其资源。
  
        Remove-AzureRmResourceGroup -Name TestRG1
  
    系统将请求你确认删除。
  
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>部署脚本
本主题中前面的部署示例仅介绍了将资源部署到 Azure 所需的各个 cmdlet。 以下示例显示了可创建资源组和部署资源的部署脚本。

    <#
      .SYNOPSIS
      Deploys a template to Azure

      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>后续步骤
* 若要了解如何创建 Resource Manager 模板，请参阅[创作 Azure Resource Manager 模板](resource-group-authoring-templates.md)。
* 若要了解如何部署模板，请参阅[使用 Azure Resource Manager 模板部署应用程序](resource-group-template-deploy.md)。
* 有关部署项目的详细示例，请参阅[按可预见的方式在 Azure 中部署微服务](../app-service-web/app-service-deploy-complex-application-predictably.md)。
* 若要了解如何排查失败的部署，请参阅 [Azure 中的资源组部署疑难解答](resource-manager-troubleshoot-deployments-powershell.md)。
* 若要通过指南了解企业可如何使用 Resource Manager 有效管理订阅，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。




<!--HONumber=Nov16_HO3-->


