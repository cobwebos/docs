<!-- not suitable for Mooncake -->

<properties
	pageTitle="使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager | Azure"
	description="本文将逐步讲解如何使用 PowerShell 脚本对资源进行平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.date="05/04/2016"
	wacn.date=""/>

# 使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager

以下步骤演示了如何使用 Azure PowerShell 命令将基础结构即服务 (IaaS) 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。这些步骤遵循填空方法来迁移自定义环境。你只需使用命令并将变量（以“$”为开头的行）替换为自己的值。

## 步骤 1：准备迁移

下面是建议你在将 IaaS 资源从经典部署模型迁移到 Resource Manager 部署模型时遵循的一些最佳实践：

- 请参阅[不受支持的配置或功能的列表](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager/)。如果虚拟机使用不受支持的配置或功能，建议你等到我们宣布支持该配置/功能时再进行迁移。或者，可以删除该功能或移出该配置，以利迁移进行（如果这样做符合要求）。
-	如果你通过自动化脚本来部署目前的基础结构和应用程序，则可尝试使用这些脚本进行迁移，以便创建类似的测试性设置。也可以使用 Azure 门户设置示例环境。

## 步骤2：安装最新版本的 Azure PowerShell

有两个主要的安装选项：[PowerShell 库](https://www.powershellgallery.com/profiles/azure-sdk/)和 [Web 平台安装程序 (WebPI)](http://aka.ms/webpi-azps)。WebPI 将每月接收更新。PowerShell 库将持续接收更新。

有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure/)。

## 步骤 3：设置订阅并针对迁移进行注册

首先，请启动 PowerShell 提示符。对于迁移方案，需要针对经典部署模型和 Resource Manager 部署模型设置环境。

登录到 Resource Manager 模型的帐户。

	Login-AzureRmAccount

使用以下命令获取可用订阅。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

设置当前会话的 Azure 订阅。将引号内的所有内容（包括 < and > 字符）替换为相应的名称。

	$subscr="<subscription name>"
	Get-AzureRmSubscription -SubscriptionName $subscr | Select-AzureRmSubscription

>[AZURE.NOTE] 注册是一次性步骤，但必须在尝试迁移之前完成。如果不注册，你会看到以下错误消息

>	BadRequest : Subscription is not registered for migration. 

使用以下命令向迁移资源提供程序注册。
	
	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

请等五分钟让注册完成。可以使用以下命令来检查审批状态。请确保在继续操作之前，RegistrationState 为 `Registered`。

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

现在，请登录到经典模型的帐户。

	Add-AzureAccount

使用以下命令获取可用订阅。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

设置当前会话的 Azure 订阅。将引号内的所有内容（包括 < and > 字符）替换为相应的名称。

	$subscr="<subscription name>"
	Get-AzureSubscription -SubscriptionName $subscr | Select-AzureSubscription

## 步骤 4：运行迁移 IaaS 资源的命令

>[AZURE.NOTE] 此处描述的所有操作都是幂等的。如果你遇到功能不受支持或配置错误以外的问题，建议你重试准备、中止或提交操作。然后平台将重试操作。

### 迁移云服务中的虚拟机（不在虚拟网络中）

使用以下命令获取云服务列表，然后选取要迁移的云服务。请注意，如果云服务中的 VM 在虚拟网络中或者具有 Web/辅助角色，你将收到错误消息。

	Get-AzureService | ft Servicename

使用以下命令获取云服务的部署名称。

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

准备迁移云服务中的虚拟机。可以从两个选项中进行选择。

1. 如果想要将 VM 迁移到平台所创建的虚拟网络上

	第一步是使用以下命令来验证你是否可以迁移云服务：

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
		$validate.ValidationMessages

	上述命令会显示任何阻碍迁移的警告和错误。如果验证成功，你就可以继续执行下面的“准备”步骤。

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

2. 如果想要迁移到 Resource Manager 部署模型中的现有虚拟网络

		$existingVnetRGName = "<Existing VNET's Resource Group Name>"
		$vnetName = "<Virtual Network Name>"
		$subnetName = "<Subnet name>"

	第一步是使用以下命令来验证你是否可以迁移云服务：

		$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
		$validate.ValidationMessages

	上述命令会显示任何阻碍迁移的警告和错误。如果验证成功，你就可以继续执行下面的“准备”步骤。

		Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName

准备操作成功后，可以查询 VM 的迁移状态，并确保其处于 `Prepared` 状态。

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

使用 PowerShell 或 Azure 门户查看准备就绪的资源的配置。如果你尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令。

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源。

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### 迁移虚拟网络中的虚拟机

选取要迁移的虚拟网络。

	$vnetName = "VNET-Name"

>[AZURE.NOTE] 如果虚拟网络包含的 Web/辅助角色或 VM 的配置不受支持，你将收到验证错误消息。

第一步是使用以下命令来验证你是否可以迁移虚拟网络：

	Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName

上述命令会显示任何阻碍迁移的警告和错误。如果验证成功，你就可以继续执行下面的“准备”步骤。
	
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

使用 PowerShell 或 Azure 门户查看准备就绪的虚拟机的配置。如果你尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令。

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源。

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

### 迁移存储帐户

完成虚拟机迁移之后，建议你迁移存储帐户。

使用以下命令来准备要迁移的存储帐户

	$storageAccountName = "storagename"
	Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName

使用 PowerShell 或 Azure 门户查看准备就绪的存储帐户的配置。如果你尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令。

	Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源。

	Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName

## 后续步骤

- [平台支持的从经典部署模型到 Resource Manager 部署模型的 IaaS 资源迁移](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager/)
- [技术探讨：平台支持的从经典部署模型到 Resource Manager 部署模型的迁移](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager-deep-dive/)
- [使用社区 PowerShell 脚本将经典虚拟机克隆到 Azure Resource Manager](/documentation/articles/virtual-machines-windows-migration-scripts/)

<!---HONumber=Mooncake_0808_2016-->