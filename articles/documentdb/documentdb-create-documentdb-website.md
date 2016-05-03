<properties 
	pageTitle="使用 Azure Resource Manager 模板部署 DocumentDB 和 Azure App Service Web Apps | Microsoft Azure" 
	description="了解如何使用 Azure Resource Manager 模板部署 DocumentDB 帐户、Azure App Service Web Apps 以及示例 Web 应用程序。" 
	services="documentdb, app-service\web" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.date="02/01/2016" 
	wacn.date=""/>

# 使用 Azure Resource Manager 模板部署 DocumentDB 和 Azure App Service Web Apps #

本教程说明如何使用 Azure Resource Manager 模板来部署和集成 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)、Web 应用以及示例 Web 应用程序。

完成本教程后，你将能够回答以下问题：

-	如何使用 Azure Resource Manager 模板来部署和集成 DocumentDB 帐户与 Azure App Service 中的 Web 应用？
-	如何使用 Azure Resource Manager 模板来部署和集成 DocumentDB 帐户、App Service Web Apps 中的 Web 应用以及 Webdeploy 应用程序？

<a id="Prerequisites"></a>
## 先决条件 ##
> [AZURE.TIP] 虽然本教程不会假设先前有使用 Azure Resource Manager 模板、JSON 或 Azure PowerShell 的经验，但是，如果你想修改引用的模板或部署选项，则需要有其中每个领域的知识。

在按照本教程中的说明操作之前，请确保已有下列各项：

- Azure 订阅。Azure 是基于订阅的平台。有关获取订阅的详细信息，请参阅[购买选项](https://azure.microsoft.com/pricing/purchase-options/)、[成员优惠](https://azure.microsoft.com/pricing/member-offers/)或[免费试用](https://azure.microsoft.com/pricing/free-trial/)。
- Azure 存储帐户。有关说明，请参阅[关于 Azure 存储帐户](/documentation/articles/storage-create-storage-account.md)。
- 配备 Azure PowerShell 0.9.8 的工作站。有关说明，请参阅[安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure)。本教程尚未针对 Azure PowerShell 1.0 预览版进行更新。 

##<a id="CreateDB"></a>步骤 1：下载并提取示例文件 ##
让我们从下载示例文件开始，我们将在本教程中使用这些文件。

1. 将[创建 DocumentDB 帐户、Web Apps 和部署演示应用程序示例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)下载到本地文件夹（例如 C:\\DocumentDBTemplates）并提取文件。此示例将部署 DocumentDB 帐户、App Service Web 应用和 Web 应用程序。它还会自动配置 Web 应用程序，以连接到 DocumentDB 帐户。

2. 将[创建 DocumentDB 帐户和 Web Apps 示例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)下载到本地文件夹（例如 C:\\DocumentDBTemplates）并提取文件。此示例将部署 DocumentDB 帐户、App Service Web 应用，并修改 Web 应用的配置以便轻松地呈现 DocumentDB 连接信息，但不包含 Web 应用程序。

> [AZURE.TIP] 请注意，根据你计算机的安全设置，你可能需要取消阻止所提取的文件，方法是单击鼠标右键，并单击“属性”，然后单击“取消阻止”。

![屏幕截图：突出显示“取消阻止”按钮的“属性”窗口](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##步骤 2：部署 DocumentDB 帐户、App Service Web 应用和演示应用程序示例 ##

现在让我们来部署第一个模板。

> [AZURE.TIP] 该模板不会验证下面输入的 Web 应用名称和 DocumentDB 帐户名称是否 a) 有效以及 b) 可用。强烈建议你在运行 PowerShell 部署脚本之前，先确认你打算提供的名称的可用性。

1. 打开 Microsoft Azure PowerShell，然后导航到你在其中下载并提取[创建 DocumentDB 帐户、App Service Web 应用和部署演示应用程序示例的文件夹](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)（例如 C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo）。


2. 我们即将运行 CreateDocDBWebsiteTodo.ps1 PowerShell 脚本。该脚本采用下列必需参数：
	- WebsiteName：指定 App Service Web 应用名称，并用来构造你将用于访问 Web 应用的 URL（例如，如果你指定“mydemodocdbwebsite”，则将用于访问 Web 应用的 URL 会是 mydemodocdbwebsite.azurewebsites.net）。

	- ResourceGroupName：指定要部署的 Azure 资源组的名称。如果指定的资源组不存在，则会创建一个。

	- docDBAccountName：指定要创建的 DocumentDB 帐户的名称。

	- location：指定要在其中创建 DocumentDB 和 Web 应用资源的 Azure 位置。有效值为：东亚、东南亚、美国东部、美国西部、北欧、西欧（请注意，提供的位置值区分大小写）。


3. 以下是运行该脚本的示例命令：

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP] 请注意，在运行该脚本时，系统会提示你输入 Azure 帐户的用户名和密码。完整部署需要 10 到 15 分钟才能完成。

4. 以下是所生成的输出的示例：

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. 在查看我们的示例应用程序之前，让我们先来了解已完成的模板部署：

	- 创建了 App Service Web 应用。

	- 创建了 DocumentDB 帐户。

	- Web 部署包已部署到 App Service Web 应用

	- 修改了 Web 应用配置，因此 DocumentDB 终结点和主要主密钥呈现为应用程序设置。

	- 创建了一系列默认监视规则。

	
6. 若要使用应用程序，只需导航到 Web 应用 URL（上述示例中的 URL 会是 http://mydemodocdbwebapp.azurewebsites.net)）。你会看到下列 Web 应用程序：

	![示例待办事项应用程序](./media/documentdb-create-documentdb-website/image2.png)

7. 请继续进行，创建几个任务，接下来，让我们打开 [Microsoft Azure 门户](https://portal.azure.com)。

8. 选择浏览资源组，并选择我们在部署期间创建的资源组（上述示例中的 myDemoResourceGroup）。

	![屏幕截图：突出显示 myDemoResourceGroup 的 Azure 经典门户](./media/documentdb-create-documentdb-website/image3.png)
9.  注意“摘要”可重用功能区中的资源映射如何显示我们所有的相关资源（DocumentDB 帐户、App Service Web 应用、监视）。

	![屏幕截图：“摘要”可重用功能区](./media/documentdb-create-documentdb-website/image4.png)
10.  单击你的 DocumentDB 帐户，并启动查询资源管理器（靠近“帐户”边栏选项卡的底部）。

	![屏幕截图：突出显示“查询资源管理器”磁贴的“资源组”和“帐户”边栏选项卡](./media/documentdb-create-documentdb-website/image8.png)

11. 运行默认查询“SELECT * FROM c”，并检查结果。请注意，查询已检索你在上面的步骤 7 中创建的待办事项的 JSON 表示形式。任意尝试查询；例如，尝试运行 SELECT * FROM c WHERE c.isComplete = true，以返回所有标记为完成的待办事项。


	![屏幕截图：显示查询结果的“查询资源管理器”和“结果”边栏选项卡](./media/documentdb-create-documentdb-website/image5.png)
12. 任意体验 DocumentDB 门户，或修改示例待办事项应用程序。当你准备好时，让我们来部署另一个模板。
	
<a id="Build"></a> 
## 步骤 3：部署 DocumentDB 帐户和 Web 应用示例 ##

现在让我们来部署第二个模板。

> [AZURE.TIP] 该模板不会验证下面输入的 Web 应用名称和 DocumentDB 帐户名称是否 a) 有效以及 b) 可用。强烈建议你在运行 PowerShell 部署脚本之前，先确认你打算提供的名称的可用性。

1. 打开 Microsoft Azure PowerShell，然后导航到你在其中下载并提取[创建 DocumentDB 帐户和 Web 应用示例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)的文件夹（例如 C:\\DocumentDBTemplates\\CreateDocDBWebsite）。


2. 我们即将运行 CreateDocDBWebsite.ps1 PowerShell 脚本。该脚本采用与我们部署的第一个模板相同的参数，即：
	- WebsiteName：指定 App Service Web 应用名称，并用来构造你将用于访问 Web 应用的 URL（例如，如果你指定“myotherdocumentdbwebapp”，则将用于访问 Web 应用的 URL 会是 myotherdocumentdbwebapp.azurewebsites.net）。

	- ResourceGroupName：指定要部署的 Azure 资源组的名称。如果指定的资源组不存在，则会创建一个。

	- docDBAccountName：指定要创建的 DocumentDB 帐户的名称。

	- 	location：指定要在其中创建 DocumentDB 和 Web 应用资源的 Azure 位置。有效值为：东亚、东南亚、美国东部、美国西部、北欧、西欧（请注意，提供的位置值区分大小写）。

3. 以下是运行该脚本的示例命令：

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP] 请注意，在运行该脚本时，系统会提示你输入 Azure 帐户的用户名和密码。完整部署需要 10 到 15 分钟才能完成。

4. 部署输出将非常类似于第一个模板示例。
5. 在打开 Azure 门户之前，让我们先来了解已完成的模板部署：

	- 创建了 App Service Web 应用。

	- 创建了 DocumentDB 帐户。

	- 	修改了 Web 应用配置，因此Azure DocumentDB 终结点、主要主密钥和辅助主密钥呈现为应用程序设置。

	- 	创建了一系列默认监视规则。

6. 让我们打开 [Azure 门户](https://portal.azure.cn)，选择浏览资源组，并选择我们在部署期间创建的资源组（上述示例中的 myOtherDemoResourceGroup）。
7. 在“摘要”可重用功能区中，单击刚部署的 Web 应用。

	![屏幕截图：突出显示 myotherdocumentdbwebapp web 应用的“摘要”可重用功能区](./media/documentdb-create-documentdb-website/image6.png)
8. 在 Web 应用边栏选项卡中，依次单击“所有设置”、“应用程序设置”，并注意出现的 DocumentDB 终结点和每个 DocumentDB 主密钥的应用程序设置。

	![屏幕截图：Web 应用、“设置”和“应用程序设置”边栏选项卡](./media/documentdb-create-documentdb-website/image7.png)
9. 继续任意浏览 Azure 门户，或遵循其中一个 DocumentDB [示例](http://go.microsoft.com/fwlink/?LinkID=402386)来创建你自己的 DocumentDB 应用程序。

	
	
<a name="NextSteps"></a>
## 后续步骤

祝贺你！ 你已使用 Azure Resource Manager 模板部署了 DocumentDB、App Service Web 应用以及示例 Web 应用程序。

- 若要了解有关 DocumentDB 的详细信息，请单击[此处](http://azure.com/docdb)。
- 若要详细了解 Azure App Service Web Apps，请单击[此处](http://go.microsoft.com/fwlink/?LinkId=325362)。
- 若要详细了解 Azure Resource Manager 模板，请单击[此处](https://msdn.microsoft.com/library/azure/dn790549.aspx)。


## 发生的更改
* 有关从网站更改为 App Service 的指南，请参阅 [Azure App Service 及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)
* 有关从旧门户更改为新门户的指南，请参阅：[有关在 Azure 经典门户中导航的参考](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE] 如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到[试用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。
 

<!---HONumber=Mooncake_0425_2016-->