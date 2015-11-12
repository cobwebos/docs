<properties
   pageTitle="开发和测试环境 | Microsoft Azure"
   description="了解如何使用 Azure 资源管理器模板快速一致性地创建和删除开发与测试环境。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.date="09/17/2015"
   wacn.date=""/>

# Microsoft Azure 中的开发和测试环境

自定义应用程序在部署到生产环境之前，通常会先部署到多个开发和测试环境。在本地创建环境时，需要为每个应用程序的每个环境采购或分配计算资源。环境通常包含多个物理或虚拟机，这些虚拟机采用手动部署的特定配置或包含复杂的自动化脚本。部署通常需要数小时，并且会在环境中导致不一致的配置。

## 方案 ##

当你在 Microsoft Azure 中预配开发和测试环境时，只需支付资源的使用费。本文说明如何使用 Azure 资源管理器模板和参数文件按如下所示快速一致地创建、维护和删除开发及测试环境。

![方案](./media/solution-dev-test-environments-preview-portal/scenario.png)

上面显示了三个开发和测试环境。每个环境包含一个 Web 应用程序和 SQL 数据库。每个环境中的应用程序和数据库名称都不相同。本文说明如何使用模板在各环境中部署相同的资源，并使用唯一的参数文件在各环境中指定资源的不同配置。

如果你不熟悉 Azure 资源管理器的概念，建议在阅读本文之前先阅读 [Azure 资源管理器概述](/documentation/articles/resource-group-overview)一文。

你也可以先完成本文中所列的步骤而不阅读任何参考文章，以快速体验 Azure 资源管理器模板的使用。在执行这些步骤时，你肯定会遇到许多问题。一旦完成这些步骤，你就可以通过阅读参考文章并进一步执行这些步骤来获取大部分问题的解答。

## 规划 Azure 资源的使用
在拥有应用程序高级设计经验后，你可以定义：

- 应用程序所要包含的 Azure 资源。你可以构建应用程序并将它部署为包含 Azure SQL 数据库的 Azure Web 应用。可以在虚拟机中使用 PHP 和 MySQL 或 IIS 和 SQL Server 或其他组件构建应用程序。[Azure App Service、云服务与虚拟机的比较](app-service-web/choose-web-site-cloud-service-vm.md)一文可帮助你确定想要用于应用程序的 Azure 资源。
- 应用程序应符合哪些服务级别要求，例如可用性、安全性和缩放性。

## 使用 Azure 资源管理器模板
Azure 资源管理器模板定义应用程序使用的所有 Azure 资源。你可以直接在 Azure 预览门户中部署多个现有的模板，或者使用应用程序代码在源代码管理系统中下载、修改和保存这些模板。现有的模板可能包含你想要与应用程序配合使用的资源。你可以在 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/) GitHub 存储库中找到可用的模板列表。

在列表中，你将看到 [201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) 文件夹。由于许多自定义应用程序包含 Web 应用程序和 SQL 数据库，此模板可作为本文其余部分的示例，帮助你了解如何使用模板。本文未提供有关创建和配置此模板的完整内容，但是，如果你打算使用它在组织中创建实际环境，请阅读[预配包含 SQL 数据库的 Web 应用](app-service-web/app-service-web-arm-with-sql-database-provision.md)一文以全面了解该模板。

> [AZURE.NOTE]你可以通过单击[预配包含 SQL 数据库的 Web 应用](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)一文中的“部署到 Azure”按钮直接将模板部署到 Azure。你可能发现这对了解模板很有帮助，但这样做并不能使用应用程序代码编辑、保存模板与参数值及控制其版本。本文中的步骤说明如何使用应用程序代码保存模板和参数值及控制其版本。

  **步骤 1：**查看 201-web-app-sql-database 文件夹中 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) 文件的内容。这是 Azure 资源管理器模板文件。在视图模式中，单击“原始”按钮。[](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json)使用鼠标选择此文件的整个内容，然后将它以“TestApp1-Template.json”文件名保存到计算机。

在该模板文件中，你会看到“resources”部分，其中定义了此模板创建的 Azure 资源。在其他资源类型中，此模板将创建 [Azure Web 应用](app-service-web/app-service-web-overview.md)和 [Azure SQL 数据库](sql-database/sql-database-technical-overview.md)资源。

你还会看到“parameters”部分，其中定义了可用来设置配置资源的参数。在模板中指定的一些参数带有“defaultValue”属性，而其他一些参数则没有该属性。使用模板部署 Azure 资源时，必须向所有没有 defaultValue 属性的参数提供值。如果未向带有 defaultValue 属性的参数提供值，则会使用模板中为 defaultValue 参数指定的值。

模板定义所创建的 Azure 资源，以及可用来配置每个资源的参数。通常你需要在每个开发和测试环境中创建相同的资源。你可以阅读[设计 Azure 资源管理器模板的最佳实践](/documentation/articles/best-practices-resource-manager-design-templates)一文，以进一步了解模板及如何设计自己的模板。

## 创建参数文件

你可能希望在每个环境中创建相同的 Azure 资源，但也可能希望在每个环境中设置不同的资源。这就是参数文件的作用。

  **步骤 2：**查看 201-web-app-sql-database 文件夹中 [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) 文件的内容。这是在步骤 1 中保存的模板文件的参数文件。在视图模式中，单击“原始”按钮。[](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json)使用鼠标选择此文件的整个内容，然后使用以下名称将它保存在计算机上的三个不同文件中：

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **步骤 3：**使用任何文本或 JSON 编辑器，编辑在步骤 2 中创建的开发和测试环境参数文件，并将文件中的现有值替换为以下值。

  --TestApp1-Parameters-Development.json--

| 参数 | 说明 |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | 美国中部 |
| **serverName** | testapp1dev |
| **serverLocation** | 美国中部 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| 参数 | 说明 |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | 美国中部 |
| **serverName** | testapp1test |
| **serverLocation** | 美国中部 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

在步骤 6 中，这些参数文件将在开发和测试环境中用于创建 Azure Web 应用和 Azure SQL 数据库资源的唯一配置。

 **步骤 4：**编辑在步骤 2 中创建的 TestApp1-Parameters-Pre-Production.json 参数文件。将文件的整个内容替换为以下内容：

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

在上述预生产参数文件中，**sku** 和 **requestedServiceObjectiveName** 参数为 *added*，而它们并未添加到开发和测试参数文件。这是因为模板中为这些参数指定了默认值，并且默认值也用于开发和测试环境中，但在预生产环境中用于这些参数的是非默认值。

在预生产环境中将非默认值用于这些参数的原因是要测试生产环境可能偏好的这些参数的值。这些参数全都与应用程序使用的 Azure [Web 应用托管计划](http://azure.microsoft.com/pricing/details/app-service/)、**sku** 和 [Azure SQL 数据库](http://azure.microsoft.com/pricing/details/sql-database/)或 **requestedServiceObjectiveName** 相关。不同的 sku 和服务目标名称有不同的成本和功能，并支持不同的服务级别度量量。

下表列出了模板中指定参数的默认值，以及在预生产参数文件中替代默认值的值。

| 参数 | 默认值 | 参数文件值 |
|---|---|---|
| **sku** | 免费 | 标准 |
| **requestedServiceObjectiveName** | S0 | S1 |

## 创建环境
所有 Azure 资源必须在 [Azure 资源组](azure-portal/resource-group-portal.md)中创建。资源组可让你将 Azure 资源分组，以便可以统一管理这些资源。[权限](azure-portal/resource-group-rbac.md)可以分配给资源组，使组织中的特定人员可以创建、修改、删除或查看这些组及其包含的资源。可以在 [Azure 预览门户](https://portal.azure.com)中查看资源组中资源的警报和计费信息。资源组在 Azure [位置](http://azure.microsoft.com/regions/)中创建。在本文中，所有资源都在美国中部位置创建。开始创建实际环境时，请选择最符合要求的位置。

  **步骤 5：**使用下列任一方法创建开发和测试环境的资源组。这两种方法会达到完全相同的效果。

  **方法 1：**Azure 命令行界面 (CLI)

  确保 Windows、OS X 或 Linux 计算机上[已安装](/documentation/articles/xplat-cli-install) CLI，并且你的组织 ID [已连接](/documentation/articles/xplat-cli-connect)到 Azure 订阅。在 CLI 命令行中键入以下命令以创建开发环境的资源组。

	azure group create "TestApp1-Development" "Central US"

  如果命令成功，将返回以下信息：

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  若要创建测试环境的资源组，请键入以下命令：

	azure group create "TestApp1-Test" "Central US"

  若要创建预生产环境的资源组，请键入以下命令：

	azure group create "TestApp1-Pre-Production" "Central US"

  **方法 2：**PowerShell

  确保已按[如何安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure) 一文中的详述，在 Windows 计算机上安装了 PowerShell 并已连接到你的订阅。在 PowerShell 命令提示符下，键入以下命令以创建开发环境的资源组。

	New-AzureResourceGroup -Name TestApp1-Development -Location "Central US"

  如果命令成功，将返回以下信息：

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  若要创建测试环境的资源组，请键入以下命令：

	New-AzureResourceGroup -Name TestApp1-Test -Location "Central US"

  若要创建预生产环境的资源组，请键入以下命令：

	New-AzureResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **步骤 6：**使用应用程序的模板文件，将 Azure 资源部署到每个环境的资源组，并使用下列任一方法将其部署到每个环境的参数文件。这两种方法会达到完全相同的效果。

  **方法 1：**Azure 命令行界面 (CLI)

  在 CLI 命令行中键入以下命令（将 [path] 替换为前面步骤中的文件保存路径），将资源部署到为开发环境创建的资源组。

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  如果命令成功，将返回以下信息：

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  如果命令未成功，请解决任何错误消息并重试。常见的问题是使用了未遵循 Azure 资源命名约束的参数值。其他故障排除提示可在 [Azure 中的资源组部署故障排除](virtual-machines/resource-group-deploy-debug.md)一文中找到。

  在 CLI 命令行中键入以下命令（将 [path] 替换为前面步骤中的文件保存路径），将资源部署到为测试环境创建的资源组。

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  在 CLI 命令行中键入以下命令（将 [path] 替换为前面步骤中的文件保存路径），将资源部署到为预生产环境创建的资源组。

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **方法 2：**PowerShell

  在 PowerShell 命令提示符下键入以下命令（将 [path] 替换为前面步骤中的文件保存路径），将资源部署到为开发环境创建的资源组。

	New-AzureResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  如果命令成功，将返回以下信息：

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  如果命令未成功，请解决任何错误消息并重试。常见的问题是使用了未遵循 Azure 资源命名约束的参数值。其他故障排除提示可在 [Azure 中的资源组部署故障排除](virtual-machines/resource-group-deploy-debug.md)一文中找到。

  在 PowerShell 命令提示符下键入以下命令（将 [path] 替换为前面步骤中的文件保存路径），将资源部署到为测试环境创建的资源组。

	New-AzureResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  在 PowerShell 命令提示符下键入以下命令（将 [path] 替换为前面步骤中的文件保存路径），将资源部署到为预生产环境创建的资源组。

	New-AzureResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

在源代码管理系统中可以使用应用程序代码控制模板和参数文件的版本和进行维护。还可以将上述命令保存到脚本文件，以及将它们与代码一起保存。

## 维护环境
在整个开发过程中，不同环境中的 Azure 资源配置可能会出现有意或无意的不一致性更改。这可能会在应用程序开发周期中造成不必要的故障排除和问题解决。

  **步骤 7：**更改环境。打开 [Azure 预览门户](https://portal.azure.com)并使用完成上述步骤时所用的同一帐户登录。如下图所示，单击“浏览全部”->“资源组”（可能需要在“浏览”边栏选项卡中向下滚动才能看到资源组）。你将看到使用先前步骤中的某种方法创建的所有三个资源组。单击 TestApp1-Development 资源组，如下所示。

  ![门户](./media/solution-dev-test-environments-preview-portal/portal1.png)

  单击 TestApp1-Development 资源组之后，你将看到一个边栏选项卡，其中列出了模板在上一步骤完成的资源组部署中所创建的资源。通过单击“TestApp1Dev”->“删除”来删除 TestApp1Dev Web 应用资源，如下所示。

  ![门户](./media/solution-dev-test-environments-preview-portal/portal2.png)

  当门户提示是否确定要删除该资源时，请单击“是”。资源组的内容现在与其应有内容不同。你可以从多个资源组删除多个资源来进一步试验，甚至可以更改某些资源的配置设置。

> [AZURE.NOTE]如果不使用 Azure 预览门户从资源组删除资源，你可以使用 PowerShell [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) 命令或者从 CLI 使用“azure resource delete”命令来完成相同的任务。

  **步骤 8：**使用在步骤 6 中使用的相同命令，将环境重新部署到资源组，但这次请将“Deployment1”替换为“Deployment2”。 如下图中的“摘要”部分所示，你将看到来自模板的所有资源再次出现在 TestApp1-Development 资源组中。使用 Azure 资源管理器模板部署环境的优点之一是可以随时轻松地将环境重新部署回到已知状态。

  ![门户](./media/solution-dev-test-environments-preview-portal/portal3.png)

  如果你单击图中“上次部署”下面的文本，将会看到边栏选项卡显示资源组的部署历史记录。由于你已将名称“Deployment1”用于第一个部署，并已将“Deployment2”用于第二个部署，因此会有两个条目。单击某个部署会显示一个边栏选项卡，其中显示了每个部署的结果。

## 删除环境
使用完某个环境后，你可以将它删除，以免支付不再使用的 Azure 资源的使用费。删除环境比创建环境还要容易。在前面的步骤中，我们为每个环境各自创建了 Azure 资源组。当你删除资源组时，会一同删除它所包含的资源。这意味着，以下任何一种方法都会删除环境（资源组）以及前面在其中部署的所有 Azure 资源。

  **步骤 9：**使用下列任一方法删除环境。这两种方法会达到完全相同的效果。

  **方法 1：Azure CLI**

  在 CLI 提示符下键入以下命令：

	azure group delete "TestApp1-Development"

  如果你在提示下输入“y”，命令将返回以下内容：

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  在 CLI 提示符下键入以下命令，以删除剩余的环境：

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **方法 2：**PowerShell

  在 PowerShell 提示符下键入以下命令：

	Remove-AzureResourceGroup -Name TestApp1-Development

  如果你在提示下输入“y”，命令将返回以下内容：

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  在 PowerShell 提示符下键入以下命令，以删除剩余的环境：

	Remove-AzureResourceGroup -Name TestApp1-Test
	Remove-AzureResourceGroup -Name TestApp1-Pre-Production

不管使用哪种方法，一旦命令完成执行，资源组及其包含的所有资源都将不再存在，但你不再需要支付资源的使用费。

若要在应用程序开发过程中最大程度地减少 Azure 资源的使用费，你可以使用 [Azure 自动化](automation/automation-intro.md)来计划作业，以便：

- 在每天结束时停止虚拟机并在每天开始时重新启动它们。
- 在每天结束时删除整个环境并在每天开始时重新创建它们。
 
现在，你已了解如何轻松创建、维护和删除开发与测试环境，接下来可以通过进一步执行上述步骤并阅读本文中包含的参考内容，来深入了解刚刚学习的知识。

## 后续步骤

- 通过将 Microsoft Azure AD 组或用户分配到能够对 Azure 资源执行部分操作的特定角色，向每个环境中的不同资源[委派系统管理控制](/documentation/articles/role-based-access-control-configure)。
- 向每个环境的资源组和/或单个资源[分配标记](/documentation/articles/resource-group-using-tags)。你可以将“Environment”标记添加到资源组，并将其值设置为与环境名称相对应。当您需要组织资源以进行计费或管理时，标记会特别有用。
- 在 [Azure 预览门户](https://portal.azure.com)中监视资源组中资源的警报和计费。

## 其他资源

- 使用安装的 Azure SDK 2.6 [在 Visual Studio 中创建和部署 Azure 资源管理器模板](http://msdn.microsoft.com/zh-cn/library/azure/Dn872471.aspx)。
- 使用 [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs)、[Visual Studio Code](http://www.visualstudio.com/products/code-vs) 或 [Web Matrix](http://www.microsoft.com/web/webmatrix/) 创建应用程序。
- 在创建的环境中[部署 Web 应用](app-service-web/web-sites-deploy.md)。
- 使用 [Visual Studio Release Management](http://msdn.microsoft.com/Library/vs/alm/Release/overview) 创建受管的连续部署管道，以快速轻松地定期发布产品。
- 请求 [Azure 开发/测试实验室](http://azure.microsoft.com/campaigns/devtest-lab/)的预览邀请。它可让你使用模板管理开发和测试实验室环境，并配置组织内的使用配额和策略。

<!---HONumber=79-->