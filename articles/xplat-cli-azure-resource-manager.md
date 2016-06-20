
<properties
	pageTitle="将 Azure CLI 与 Resource Manager 配合使用 | Azure"
	description="使用 Azure 命令行接口 (CLI) 将多个资源部署为资源组"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.date="04/20/2016"
	wacn.date=""/>

# 将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理器配合使用

> [AZURE.SELECTOR]
- [Azure CLI](/documentation/articles/xplat-cli-azure-resource-manager)
- [Azure PowerShell](/documentation/articles/powershell-azure-resource-manager)



本文介绍在 Azure Resource Manager 模式下使用 Azure 命令行接口 (Azure CLI) 创建和管理 Azure 资源的常见方式。

>[AZURE.NOTE] 若要使用命令行来创建和管理 Azure 资源，你需要一个 Azure 订阅（[在此处创建 Azure 帐户](http://azure.microsoft.com/pricing/free-trial/)）。你还需要[安装 Azure CLI](/documentation/articles/xplat-cli-install)，并[登录以使用与你帐户关联的 Azure 资源](/documentation/articles/xplat-cli-connect)。如果已做好了这些准备，你便可以开始了。

## Azure 资源

使用 Azure 资源管理器可将一组资源（用户管理的实体，例如虚拟机、数据库服务器、数据库或网站）作为单个逻辑单位或资源组来进行创建和管理。

Azure Resource Manager 的其中一个优点是，可采用声明性方式创建 Azure 资源：在 JSON 模板中描述一组可部署资源的结构和关系。模板确定相关参数，这些参数可以在运行命令时以内联方式填充，或存储在单独的 JavaScript 对象表示法 (JSON) 参数文件中。这样您只需提供不同参数，即可使用同一模板轻松地创建新资源。例如，创建网站的模板将包含站点名称参数、网站所在区域的参数以及其他常见设置的参数。

使用模板修改或创建组时，将创建部署，随后将其应用于该组。有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。

创建部署后，可以在命令行中以命令方式管理各个资源，就像在经典部署模型中一样。例如，在 Resource Manager 模式下使用 CLI 命令来启动、停止或删除资源（如 [Azure Resource Manager 虚拟机](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md)）。

## 身份验证

若要通过 Azure CLI 使用 Azure Resource Manager，目前你需要使用 `azure login` 命令，然后指定由 Azure Active Directory 管理的帐户 — 工作或学校帐户（组织帐户）或 Microsoft 帐户，向 Microsoft Azure 进行身份验证。在此模式下，使用通过 .publishsettings 文件安装的证书进行身份验证不起作用。

有关向 Microsoft Azure 进行身份验证的详细信息，请参阅[从 Azure CLI 连接到 Azure 订阅](xplat-cli-connect.md)。

>[AZURE.NOTE] 当你使用由 Azure Active Directory 管理的帐户时，还可以使用 Azure 基于角色的访问控制 (RBAC) 来管理 Azure 资源的访问和使用。有关详细信息，请参阅 [Azure 基于角色的访问控制](./active-directory/role-based-access-control-configure.md)。

## 设置 Resource Manager 模式

由于 CLI 在默认情况下不处于 Resource Manager 模式，因此需要使用以下命令来启用 Azure CLI Resource Manager 命令。

	azure config mode arm

## 查找位置

大多数 Azure 资源管理器命令需要有可用于创建或查找资源的有效位置。可以使用以下命令来查找不同 Azure 资源的所有可用位置。

	azure location list

这将列出可用的 Azure 区域，例如“美国西部”、“美国东部”，等等。有关可用的资源提供程序以及提供这些程序的位置的详细信息，请使用 `azure provider list` 命令，后跟 `azure provider show` 命令。例如，以下命令会列出 Azure 容器服务的位置：

    azure provider show Microsoft.ContainerService 

## 创建资源组

资源组是网络、存储和计算资源等资源的逻辑分组。Resource Manager 模式下的几乎所有命令都需要资源组。例如，你可以使用以下命令在美国西部区域创建名为 testRG 的资源组。

	azure group create -n "testRG" -l "West US"

你将在稍后使用模板启动 Ubuntu VM 时部署到此 testRG 资源组。创建资源组后，可以添加资源，如虚拟机、虚拟网络或存储。


## 使用资源组模板

### 查找和配置资源组模板

使用模板时，可以[创建自己的模板](resource-group-authoring-templates.md)，也可以使用社区提供的[快速启动模板](https://azure.microsoft.com/documentation/templates/)之一，这些模板也在 [GitHub](https://github.com/Azure/azure-quickstart-templates) 中提供。

创建新模板不在本文讨论的范围之内，因此我们可以从使用[快速启动模板](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux/)中提供的 101-simple-vm-from-image 模板开始。默认情况下，这将在新的虚拟网络（包含单个子网）中创建单个 Ubuntu 14.04.2-LTS 虚拟机。只需指定一个资源组和以下几个参数，即可使用此模板：

* VM 的管理员用户名为 `adminUsername`
* 密码为 `adminPassword`
* VM 的域名为 `dnsLabelPrefix`

>[AZURE.TIP] 这些步骤只演示了在 Azure CLI 中使用 VM 模板的一种方式。有关其他示例，请参阅[使用 Azure 资源管理器模板和 Azure CLI 部署和管理虚拟机](./virtual-machines/virtual-machines-linux-cli-deploy-templates.md)。

1. 点击“了解有关 GitHub 的详细信息”链接以从 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) 将 azuredeploy.json 和 azuredeploy.parameters.json 文件下载到本地计算上的工作文件夹。（请务必选择 GitHub 中每个文件的_原始_格式。）

2. 在文本编辑器中打开 azuredeploy.parameters.json 文件，然后输入适合你环境的参数值（将 **ubuntuOSVersion** 值保持不变）。

	```
			{
			  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
			  "contentVersion": "1.0.0.0",
			  "parameters": {
			    "adminUsername": {
			      "value": "azureUser"
			    },
			    "adminPassword": {
			      "value": "GEN-PASSWORD"
			    },
			    "dnsLabelPrefix": {
			      "value": "GEN-UNIQUE"
			    },
			    "ubuntuOSVersion": {
			      "value": "14.04.2-LTS"
			    }
			  }
			}

	```
3.  修改完部署参数后，将 Ubuntu VM 部署到之前创建的 testRG 资源组中。为该部署选择一个名称（在此示例中为 testRGDeploy），然后使用以下命令来启动它。

	```
	azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json testRG testRGDeploy
	```

	`-e` 选项指定你在上一步中修改的 azuredeploy.parameters.json 文件。`-f` 选项指定 azuredeploy.json 模板文件。

	在上载部署之后，但在将部署应用于组中的资源之前，此命令将返回 OK。

4. 要检查部署的状态，请使用以下命令。

	```
	azure group deployment show testRG testRGDeploy
	```

	**ProvisioningState** 将显示部署的状态。

	如果部署成功，你将看到与以下内容类似的输出。

		azure-cli@0.8.0:/# azure group deployment show testRG testRGDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsLabelPrefix    String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE] 如果你发现配置不正确，且需要停止长时间运行的部署，请使用以下命令。
	>
	> `azure group deployment stop testRG testRGDeploy`
	>
	> 如果你未提供部署名称，系统将根据模板文件的名称自动创建一个。该名称将作为 `azure group create` 命令输出的一部分返回。

	现在你可以使用你指定的域名通过 SSH 连接到 VM。连接到 VM 时，需要使用 `<domainName>.<region>.cloudapp.azure.com` 形式的完全限定域名，例如 `MyDomainName.westus.cloudapp.azure.com`。

5. 要查看组，请使用以下命令：

		azure group show testRG

	此命令将返回组中资源的相关信息。如果有多个组，可以使用 `azure group list` 命令来检索组名的列表，然后使用 `azure group show` 来查看特定组的详细信息。

你还可以直接使用 [GitHub](https://github.com/Azure/azure-quickstart-templates) 中的模板，而不用将模板下载到计算机。为此，请通过在命令中使用 **--template-url** 选项将模板的 URL 传递给 azuredeploy.json 文件。若要获取该 URL，请在 GitHub 上以 raw 模式打开 azuredeploy.json，并复制在浏览器的地址栏中显示的 URL。然后，可以直接使用此 URL 通过类似于以下内容的命令创建部署。

	azure group deployment create testRG testRGDeploy --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-linux/azuredeploy.json
系统将提示你输入必需的模板参数。

> [AZURE.NOTE] 请务必以 raw 模式打开 JSON 模板。浏览器地址栏中显示的 URL 与常规模式下显示的 URL 不同。在 GitHub 上查看文件时，若要以 raw 模式打开文件，请单击右上角的“原始”。

## 使用资源

尽管模板可用于声明组范围内的配置更改，但有时您只需要使用特定资源。你可以使用 `azure resource` 命令来实现此目的。

> [AZURE.NOTE] 使用除 `list` 命令以外的 `azure resource` 命令时，你必须使用 `-o` 参数指定你使用的资源的 API 版本。如果你不确定要使用的 API 版本，请查阅模板文件并查找资源的 **apiVersion** 字段。

1. 要列出组中的所有资源，请使用以下命令。

		azure resource list testRG

2. 若要查看组中的单个资源，例如名为 MyUbuntuVM 的 VM，请使用如下命令。

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

	注意 **Microsoft.Compute/virtualMachines** 参数。其表示您正在请求信息的资源的类型。如果你看一下之前下载的模板文件，你会注意到此相同的值用于定义模板中所述的虚拟机资源的类型。

	此命令将返回与虚拟机相关的信息。

3. 在资源上查看详细信息时，通常使用 `--json` 参数会很有用。这可使输出更具可读性，因为某些值为嵌套的结构或集合。以下示例演示了将 **show** 命令的结果返回为 JSON 文档。

		azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE] 你可以将 JSON 数据保存到文件，方法是使用 &gt; 字符将输出定向到文件。例如：
	>
	> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. 若要删除现有资源，请使用如下命令。

		azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

## 查看组日志

要查看对组执行的操作的日志记录信息，请使用 `azure group log show` 命令。默认情况下，此命令将列出对组执行的最后一个操作。要查看所有操作，请使用可选的 `--all` 参数。对于最后一个部署，请使用 `--last-deployment`。对于特定部署，请使用 `--deployment` 并指定部署名称。以下示例将返回对组 testRG 执行的所有操作的日志。

	azure group log show testRG --all
    
## 将资源组导出为模板

对于现有资源组，你可以查看资源组的 Resource Manager 模板。导出模板有两个好处：

1. 由于所有基础结构都已定义于模板中，因此可以轻松地自动进行解决方案的将来部署。

2. 可以查看代表解决方案的 JSON，以此熟悉模板语法。

使用 Azure CLI，可以导出表示资源组当前状态的模板，或下载特定部署所用的模板。

* **导出资源组的模板** — 已更改资源组并且需要检索其当前状态的 JSON 表示法时，此操作很有用。但是，生成的模板只包含最少的参数数目，但不包含任何变量。模板中大部分的值为硬编码。在部署所生成的模板之前，你可能想要将更多的值转换成参数，以便针对不同的环境自定义部署。

    若要将资源组模板导出到本地目录中，请运行 `azure group export` 命令，如以下示例所示。（替换为适用于你操作系统环境的本地目录。）

        azure group export testRG ~/azure/templates/

* **针对特定部署导出模板** — 当你需要查看用于部署资源的实际模板时，此操作很有用。模板包含针对原始部署定义的所有参数和变量。但是，如果组织中有人已更改非此模板中定义的资源组，此模板并不代表资源组的当前状态。

    若要将用于特定部署的模板下载到本地目录，请运行 `azure group deployment template download` 命令。

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] 模板导出功能处于预览状态，并非所有的资源类型目前都支持导出模板。尝试导出模板时，你可能会看到一个错误，指出未导出某些资源。如果需要，你可以在下载模板之后，在模板中手动定义这些资源。

## 后续步骤

* 有关将 Azure 资源管理器与 Azure PowerShell 配合使用的信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](powershell-azure-resource-manager.md)。
* 有关从 Azure 门户使用 Azure Resource Manager 的信息，请参阅[使用 Azure 门户部署和管理 Azure 资源](./azure-portal/resource-group-portal.md)。



<!---HONumber=Mooncake_0613_2016-->