<properties
	pageTitle="将 Azure CLI 与资源管理器配合使用 | Microsoft Azure"
	description="使用适用于 Mac、Linux 和 Windows 的 Azure CLI 将多个资源部署为资源组。"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.date="10/26/2015"
	wacn.date=""/>

# 将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 资源管理器配合使用

> [AZURE.SELECTOR]
- [Azure CLI](/documentation/articles/xplat-cli-azure-resource-manager)
- [Azure PowerShell](/documentation/articles/powershell-azure-resource-manager)



本文介绍如何在 Azure 资源管理器模式下，使用适用于 Mac、Linux 和 Windows 的 Azure 命令行界面 (CLI) 创建和管理 Azure 资源。

>[AZURE.NOTE]若要使用命令行来创建和管理 Azure 资源，你必须有一个 Azure 帐户（[在此处免费试用](http://azure.microsoft.com/pricing/free-trial/)）。你还需要[安装 Azure CLI](/documentation/articles/xplat-cli-install)，并[登录以使用与你帐户关联的 Azure 资源](/documentation/articles/xplat-cli-connect)。如果已做好了这些准备，你便可以开始了。

## Azure 资源

使用 Azure 资源管理器可将一组_资源_（用户管理的实体，例如虚拟机、数据库服务器、数据库或网站）作为单个逻辑单位或_资源组_来进行创建和管理。

Azure 资源管理器的一个优点是，可以_声明_方式创建 Azure 资源：在 JSON *模板*中描述可部署资源组的结构和关系。模板确定相关参数，这些参数可以在运行命令时以内联方式填充，或存储在单独的 JSON azuredeploy-parameters.json 文件中。这样您只需提供不同参数，即可使用同一模板轻松地创建新资源。例如，创建网站的模板将包含站点名称参数、网站所在区域的参数以及其他常见设置的参数。

使用模板修改或创建组时，将创建_部署_，随后将其应用于该组。有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](/documentation/articles/resource-group-overview)。

创建部署后，你可以在命令行中以命令方式管理各个资源，就像在经典的（服务管理）部署模型中可以做到的一样。例如，使用 Azure 资源管理器 CLI 命令来启动、停止或删除资源（如 [Azure 资源管理器虚拟机](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md)）。

## 身份验证

若要通过 Azure CLI 使用 Azure 资源管理器，你需要使用工作或学校帐户（组织帐户）或 Microsoft 帐户（从 CLI 版本 0.9.10 开始）向 Microsoft Azure 进行身份验证。在此模式下，使用通过 .publishsettings 文件安装的证书进行身份验证不起作用。

有关向 Microsoft Azure 进行身份验证的详细信息，请参阅[从 Azure CLI 连接到 Azure 订阅](/documentation/articles/xplat-cli-connect)。

>[AZURE.NOTE]当你使用由 Azure Active Directory 管理的工作或学校帐户时，你还可以使用 Azure 基于角色的访问控制 (RBAC) 来管理 Azure 资源的访问和使用。有关详细信息，请参阅[管理和审核对资源的访问权限](/documentation/articles/resource-group-rbac)。

## 设置 Azure 资源管理器模式

由于默认情况下未启用 Azure 资源管理器模式，因此需要使用以下命令来启用 Azure CLI 资源管理器命令。

	azure config mode arm

>[AZURE.NOTE]Azure 资源管理器模式与 Azure 服务管理模式互斥。即在一种模式下创建的资源不能从另一种模式进行管理。

## 查找位置

大多数 Azure 资源管理器命令需要有可用于创建或查找资源的有效位置。可以使用以下命令来查找不同 Azure 资源的所有可用位置。

	azure location list

这将列出 Azure 资源以及提供这些资源的 Azure 区域，例如“West US”、“East US”，等等。

## 创建资源组

资源组是网络、存储和其他资源的逻辑分组。Azure 资源管理器模式中的几乎所有命令都需要资源组。例如，你可以使用以下命令创建名为 _testRG_ 的资源组。

	azure group create -n "testRG" -l "West US"

在此之后，你可以开始将资源添加到此组中，然后使用此组来配置资源（如新的虚拟机）。


## 使用资源组模板

### 查找和配置资源组模板

使用模板时，你可以[创建自己的模板](/documentation/articles/resource-group-authoring-templates)，或者使用[模板库](https://azure.microsoft.com/documentation/templates/)中的某个模板，这些模板也在 [GitHub](https://github.com/Azure/azure-quickstart-templates) 中提供。

创建新模板超出本文的范围，因此首先，让我们使用 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm) 中提供的 _101-simple-vm-from-image_ 模板。默认情况下，这将在新的虚拟网络（包含美国西部区域中的单个子网）中创建单个 Ubuntu 4.04.2-LTS 虚拟机。只需指定以下少数参数即可使用此模板：

* 唯一的存储帐户名称
* VM 的管理员用户名
* 密码
* VM 的域名

>[AZURE.TIP]这些步骤只演示了在 Azure CLI 中使用 VM 模板的一种方式。有关其他示例，请参阅[使用 Azure 资源管理器模板和 Azure CLI 部署和管理虚拟机](../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md)。

1. 从 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-linux-vm) 将文件 azuredeploy.json 和 azuredeploy.parameters.json 下载到本地计算机上的工作文件夹。

2. 在文本编辑器中打开 azuredeploy.parameters.json 文件，然后输入适合你环境的参数值（将 **ubuntuOSVersion** 值保持不变）。

		{
	  	"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  	"contentVersion": "1.0.0.0",
	  	"parameters": {
		    "newStorageAccountName": {
		      "value": "MyStorageAccount"
		    },
		    "adminUsername": {
		      "value": "MyUserName"
		    },
		    "adminPassword": {
		      "value": "MyPassword"
		    },
		    "dnsNameForPublicIP": {
		      "value": "MyDomainName"
		    },
		    "ubuntuOSVersion": {
		      "value": "14.04.2-LTS"
		    }
		  }
		}

3. 保存 azuredeploy.parameters.json 文件后，请使用以下命令基于模板创建新的资源组。`-e` 选项指定你在上一步中修改的 azuredeploy.parameters.json 文件。将 *testRG* 替换为你要使用的组名，将 *testDeploy* 替换为所选的部署名称。位置应与你在模板参数文件中指定的位置相同。

		azure group create "testRG" "West US" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json

	在上载部署之后，但在将部署应用于组中的资源之前，此命令将返回 OK。

4. 要检查部署的状态，请使用以下命令。

		azure group deployment show "testRG" "testDeploy"

	**ProvisioningState** 将显示部署的状态。

	如果部署成功，你将看到与以下内容类似的输出。

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-10-26T16:15:29.5562024Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ---------------------
		data:    newStorageAccountName  String        MyStorageAccount
		data:    adminUsername          String        MyUserName
		data:    adminPassword          SecureString  undefined
		data:    dnsNameForPublicIP     String        MyDomainName
		data:    ubuntuOSVersion        String        14.04.2-LTS
		info:    group deployment show command OK

	>[AZURE.NOTE]如果你发现配置不正确，且需要停止长时间运行的部署，请使用以下命令。
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> 如果你未提供部署名称，系统将根据模板文件的名称自动创建一个。该名称将作为 `azure group create` 命令输出的一部分返回。

	现在你可以使用你指定的域名通过 SSH 连接到 VM。连接到 VM 时，需要使用 `<domainName>.<region>.cloudapp.azure.com` 形式的完全限定域名，例如 `MyDomainName.westus.cloudapp.azure.com`。

5. 要查看组，请使用以下命令：

		azure group show "testRG"

	此命令将返回组中资源的相关信息。如果有多个组，可以使用 `azure group list` 命令来检索组名的列表，然后使用 `azure group show` 来查看特定组的详细信息。

你还可以直接使用 [GitHub](https://github.com/Azure/azure-quickstart-templates) 中的模板，而不用将模板下载到计算机。为此，请通过在命令中使用 **--template-url** 选项将模板的 URL 传递给 azuredeploy.json 文件。若要获取该 URL，请在 GitHub 上以 _raw_ 模式打开 azuredeploy.json，并复制在浏览器的地址栏中显示的 URL。然后，可以直接使用此 URL 通过类似于以下内容的命令创建部署。

	azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
系统将提示你输入必需的模板参数。

> [AZURE.NOTE]请务必以 _raw_ 模式打开 JSON 模板。浏览器地址栏中显示的 URL 与常规模式下显示的 URL 不同。在 GitHub 上查看文件时，若要以 _raw_ 模式打开文件，请单击右上角的“原始”。

## 使用资源

尽管模板可用于声明组范围内的配置更改，但有时您只需要使用特定资源。你可以使用 `azure resource` 命令来实现此目的。

> [AZURE.NOTE]使用除 `list` 命令以外的 `azure resource` 命令时，你必须使用 `-o` 参数指定你使用的资源的 API 版本。如果你不确定要使用的 API 版本，请查阅模板文件并查找资源的 **apiVersion** 字段。

1. 要列出组中的所有资源，请使用以下命令。

		azure resource list "testRG"

2. 若要查看组中的单个资源，请使用如下命令。

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

	注意 **Microsoft.Compute/virtualMachines** 参数。其表示您正在请求信息的资源的类型。如果你看一下之前下载的模板文件，你会注意到此相同的值用于定义模板中所述的虚拟机资源的类型。

	此命令将返回与虚拟机相关的信息。

3. 在资源上查看详细信息时，通常使用 `--json` 参数会很有用。这可使输出更具可读性，因为某些值为嵌套的结构或集合。以下示例演示了将 **show** 命令的结果返回为 JSON 文档。

		azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json

	>[AZURE.NOTE]你可以通过使用 &gt; 字符将输出传输到文件，将 JSON 数据保存到文件。例如：
	>
	> `azure resource show "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

4. 若要删除现有资源，请使用如下命令。

		azure resource delete "testRG" "MyUbuntuVM" Microsoft.Compute/virtualMachines -o "2015-06-15"

## 日志记录

要查看对组执行的操作的日志记录信息，请使用 `azure group log show` 命令。默认情况下，此命令将列出对组执行的最后一个操作。要查看所有操作，请使用可选的 `--all` 参数。对于最后一个部署，请使用 `--last-deployment`。对于特定部署，请使用 `--deployment` 并指定部署名称。以下示例将返回对组 *MyGroup* 执行的所有操作的日志。

	azure group log show MyGroup --all

## 后续步骤

* 有关将 Azure 资源管理器与 Azure PowerShell 配合使用的信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](/documentation/articles/powershell-azure-resource-manager)。
* 有关从 Azure 门户使用 Azure 资源管理器的信息，请参阅[使用资源组管理 Azure 资源][psrm]。

[signuporg]: http://www.windowsazure.cn/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=Mooncake_1207_2015-->