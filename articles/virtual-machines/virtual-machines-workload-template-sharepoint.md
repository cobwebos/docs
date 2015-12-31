<properties
	pageTitle="使用 ARM 模板部署 SharePoint 场 | Microsoft Azure"
	description="使用资源管理器模板和 Azure 门户、Azure PowerShell 或 Azure CLI，轻松部署包含 3 个服务器或 9 个服务器的 SharePoint 场。"
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="10/20/2015"
	wacn.date=""/>

# 使用 Azure 资源管理器模板部署 SharePoint 场

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-rm-include.md)]经典部署模型。你无法使用经典部署模型创建此资源。

按照本文中的说明，使用资源管理器模板部署一个新的包含 3 个服务器或 9 个服务器的 SharePoint Server 2013 场。

## 部署包含 3 个服务器的 SharePoint 场

对于基本的 SharePoint Server 2013 场，资源管理器模板将在 3 个不同子网上的新虚拟网络中创建 3 个虚拟机。

![](./media/virtual-machines-workload-template-sharepoint/three-server-sharepoint-farm.png)

可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 运行模板。

> [AZURE.NOTE]你还可以使用 Azure 预览门户的 Azure 应用商店中的 [SharePoint 2013 非 HA 场](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/)项创建此配置。

### Azure 门户

要使用资源管理器模板和 Azure 门户部署此工作负荷，请单击[此处](https://manage.windowsazure.cn#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-three-vm%2Fazuredeploy.json)。

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	单击“参数”。在“参数”窗格上，输入新值、从允许的值中选择，或者接受默认值，然后单击“确定”。
2.	如有需要，可单击“订阅”并选择正确的 Azure 订阅。
3.	单击“资源组”并选择现有的资源组。或者，单击“或新建”为此工作负荷创建新的资源组。
4.	如有需要，可单击“资源组位置”并选择正确的 Azure 位置。
6.	单击“法律条款”以查看使用模板的条款和协议，然后单击“购买”。
7.	单击“创建”。

根据具体模板，Azure 可能需花费一些时间生成工作负荷。完成时，你的现有或新资源组中将出现一个新的包含 3 个服务器的 SharePoint 场。

### Azure PowerShell

> [AZURE.NOTE]本文包含 Azure PowerShell Preview 1.0 的命令。若要在 Azure PowerShell 0.9.8 和之前版本中运行这些命令，请将 **New-AzureRMResourceGroup** 替换为 **New-AzureResourceGroup**，将 **New-AzureRMResourceGroupDeployment** 替换为 **New-AzureResourceGroupDeployment**，并在 **New-AzureResourceGroup** 命令之前添加 **Switch-AzureMode AzureResourceManager** 命令。有关详细信息，请参阅 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)。

在以下命令集中填写 Azure 部署名称、新的资源组名称以及 Azure 数据中心位置。删除引号内的所有内容，包括 < and > 字符。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

下面是一个示例。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

接下来，在 Azure PowerShell 提示符中运行命令块。

当你运行 **New-AzureRMResourceGroupDeployment** 命令时，系统会提示你提供一系列参数的值。指定了所有参数值后，**New-AzureRMResourceGroupDeployment** 将创建和配置虚拟机。

当模板执行完成时，你的新资源组中将出现一个新的包含 3 个服务器的 SharePoint 场。

### Azure CLI

在开始之前，请确保安装了正确版本的 Azure CLI 且已登录，并切换到新的“资源管理器”模式。有关详细信息，请单击[此处](/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli#getting-ready)。

首先，创建新的资源组。使用以下命令并指定组名称，以及要向其中部署的 Azure 数据中心位置。

	azure group create <group name> <location>

下一步，使用以下命令并指定新资源组的名称以及 Azure 部署的名称。

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json <group name> <deployment name>

下面是一个示例。

	azure group create sp3serverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json sp3serverfarm spdevtest

当你运行 **azure group deployment create** 命令时，系统会提示你提供一系列参数的值。指定了所有参数值后，Azure 将创建和配置虚拟机。

现在，你的新资源组中将出现一个新的包含 3 个服务器的 SharePoint 场。

## 部署包含 9 个服务器的 SharePoint 场

对于高可用性的 SharePoint Server 2013 场，资源管理器模板将在 4 个不同子网上的新虚拟网络中创建 9 个虚拟机。

![](./media/virtual-machines-workload-template-sharepoint/nine-server-sharepoint-farm.png)

> [AZURE.NOTE]你还可以使用 Azure 预览门户的 Azure 应用商店中的 [SharePoint 2013 HA 场](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/)项创建此配置。

### Azure 门户

要使用资源管理器模板和 Azure 门户部署此工作负荷，请单击[此处](https://manage.windowsazure.cn#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsharepoint-server-farm-ha%2Fazuredeploy.json)。

![](./media/virtual-machines-workload-template-sharepoint/azure-portal-template.png)

1.	单击“参数”。在“参数”窗格上，输入新值、从允许的值中选择，或者接受默认值，然后单击“确定”。
2.	如有需要，可单击“订阅”并选择正确的 Azure 订阅。
3.	单击“资源组”并选择现有的资源组。或者，单击“或新建”为此工作负荷创建新的资源组。
4.	如有需要，可单击“资源组位置”并选择正确的 Azure 位置。
5.	单击“法律条款”以查看使用模板的条款和协议，然后单击“购买”。
6.	单击“创建”。

根据具体模板，Azure 可能需花费一些时间生成工作负荷。完成时，你的现有或新资源组中将出现一个新的包含 9 个服务器的 SharePoint 场。

### Azure PowerShell

> [AZURE.NOTE]本文包含 Azure PowerShell Preview 1.0 的命令。若要在 Azure PowerShell 0.9.8 和之前版本中运行这些命令，请将 **New-AzureRMResourceGroup** 替换为 **New-AzureResourceGroup**，将 **New-AzureRMResourceGroupDeployment** 替换为 **New-AzureResourceGroupDeployment**，并在 **New-AzureResourceGroup** 命令之前添加 **Switch-AzureMode AzureResourceManager** 命令。有关详细信息，请参阅 [Azure PowerShell 1.0 预览版](https://azure.microsoft.com/blog/azps-1-0-pre/)。

在以下命令集中填写 Azure 部署名称、新的资源组名称以及 Azure 数据中心位置。删除引号内的所有内容，包括 < and > 字符。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

下面是一个示例。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json"
	New-AzureRMResourceGroup -Name $RGName -Location $locName
	New-AzureRMResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

接下来，在 Azure PowerShell 命令提示符下运行你的命令块。

当你运行 **New-AzureRMResourceGroupDeployment** 命令时，系统会提示你提供一系列参数的值。指定了所有参数值后，**New-AzureRMResourceGroupDeployment** 将创建和配置虚拟机。

当模板执行完成时，你的新资源组中将出现一个新的包含 9 个服务器的 SharePoint 场。

### Azure CLI

在开始之前，请确保安装了正确版本的 Azure CLI 且已登录，并切换到新的“资源管理器”模式。有关详细信息，请单击[此处](/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli#getting-ready)。

首先，创建一个新的资源组。使用以下命令并指定组名称，以及要向其中部署的 Azure 数据中心位置。

	azure group create <group name> <location>

下一步，使用以下命令并指定新资源组的名称以及 Azure 部署的名称。

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json <group name> <deployment name>

下面是一个示例。

	azure group create sphaserverfarm eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-server-farm-ha/azuredeploy.json sphaserverfarm spdevtest

当你运行 **azure group deployment create** 命令时，系统会提示你提供一系列参数的值。指定了所有参数值后，Azure 将创建和配置虚拟机。

当模板执行完成时，现在你的新资源组中将出现一个新的包含 9 个服务器的 SharePoint Server 2013 场。


## 其他资源

[Azure 基础结构服务中托管的 SharePoint 场](/documentation/articles/virtual-machines-sharepoint-infrastructure-services)

[使用 Azure 资源管理器模板和 Azure PowerShell 部署和管理虚拟机](/documentation/articles/virtual-machines-deploy-rmtemplates-powershell)

[Azure 资源管理器中的 Azure 计算、网络和存储提供程序](/documentation/articles/virtual-machines-azurerm-versus-azuresm)

[Azure 资源管理器概述](/documentation/articles/resource-group-overview)

[使用 Azure 资源管理器模板和 Azure CLI 部署和管理虚拟机](/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli)

[虚拟机文档](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure)

<!---HONumber=Mooncake_1221_2015-->