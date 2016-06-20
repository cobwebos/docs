<!-- not suitable for Mooncake -->

<properties
	pageTitle="Migrate IaaS resources from Classic to Azure Resource Manager using Azure PowerShell（使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager）"
	description="本文逐步讲解平台支持的迁移服务功能：使用 PowerShell 脚本从服务管理迁移到 Azure Resource Manager"
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

# Migrate IaaS resources from Classic to Azure Resource Manager using Azure PowerShell（使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager）

以下步骤说明如何使用 Azure PowerShell 命令将 IaaS 资源从经典部署模型迁移到 Resource Manager。这些步骤遵循填空方法来迁移自定义环境。你只需使用命令并将变量（以“$”为开头的行）替换为自己的值。

## 步骤 1：准备迁移

下面是建议在评估从经典迁移 IaaS 资源到 Resource Manager 的一些最佳实践

- 请阅读[此文](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager)来了解不受支持的配置或功能。如果虚拟机使用不受支持的配置或功能，建议你等到宣布支持该功能/配置再进行迁移。或者，可以删除该功能或移出该配置，以利迁移进行（如果这样做符合要求）。
-	如果是用自动化的脚本部署基础结构和应用程序。请尝试使用这些迁移用脚本创建类似的测试设置。或者，也可以使用 Azure 门户设置示例环境。
- 因为服务目前是公共预览版本，请确保用于迁移的测试环境与生产环境隔离。请勿在测试和生产环境之间混用存储帐户、VNET 或其他资源。

## 步骤 2：安装最新的 Azure PowerShell

有两个主要的安装选项：[PowerShell 库](https://www.powershellgallery.com/profiles/azure-sdk/)和 [WebPI](http://aka.ms/webpi-azps)。WebPI 将每月接收更新。PowerShell 库将持续接收更新。

有关详细信息，请参阅 [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/)。

## 步骤 3：设置订阅并注册迁移公共预览版

首先，请启动 PowerShell 提示符。对于迁移方案，需要针对经典和 Resource Manager 设置环境。

登录到 Resource Manager 模型的帐户。

	Login-AzureRmAccount -EnvironmentName AzureChinaCloud

使用以下命令获取可用订阅。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

设置当前会话的 Azure 订阅。将引号内的所有内容（包括 < and > 字符）替换为相应的名称。

	$subscr="<subscription name>"
	Get-AzureRmSubscription -SubscriptionName $subscr | Select-AzureRmSubscription

使用以下命令注册公共预览版。

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

等 5 分钟让注册完成。可以使用以下命令来检查审批状态。在继续进一步之前，应确保 RegistrationState 为“Registered”。

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

现在，请登录到经典模型的帐户。

	Add-AzureAccount -Environment AzureChinaCloud

使用以下命令获取可用订阅。

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

设置当前会话的 Azure 订阅。将引号内的所有内容（包括 < and > 字符）替换为相应的名称。

	$subscr="<subscription name>"
	Get-AzureSubscription -SubscriptionName $subscr | Select-AzureSubscription

## 步骤 4：用于迁移 IaaS 资源的命令

>[AZURE.NOTE] 请注意，下述所有操作都是幂等的。如果你遇到不支持的功能或配置错误以外的任何情况，建议再试一次准备、中止或提交操作，然后平台将重试操作。

### 迁移云服务中的虚拟机（不在虚拟网络中）

使用以下命令获取云服务列表，然后选择要迁移的云服务。请注意，如果云服务中的 VM 在 VNET 中或者具有 Web/辅助角色，你将收到错误消息。

	Get-AzureService | ft Servicename

使用以下命令获取云服务的部署名称

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

准备迁移云服务中的虚拟机。可以选择两个选项。

如果想要将 VM 迁移到创建虚拟网络的平台上，请使用以下命令。

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

如果想要迁移到 Resource Manager 部署模型中的现有虚拟网络，请使用以下命令。

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

准备操作成功后，可以查询 VM 的迁移状态，并确保 VM 处于“已准备”状态。

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

使用 PowerShell 或 Azure 门户检查已准备好的资源的配置。如果尚未准备好进行迁移并想要回到旧状态，请使用以下命令。

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

如果准备好的配置看起来没问题，可以继续进行，使用以下命令提交资源。

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### 迁移虚拟网络中的虚拟机

选择要迁移的虚拟网络。请注意，虚拟网络是否包含 Web/辅助角色，以及 VM 是否有不支持的配置，若有，你将收到验证错误消息。

使用以下命令来准备要迁移的虚拟网络。

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

使用 PowerShell 或 Azure 门户检查已准备好的虚拟机的配置。如果尚未准备好进行迁移并想要回到旧状态，请使用以下命令。

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

如果准备好的配置看起来没问题，可以继续进行，使用以下命令提交资源。

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## 参考

- [平台支持从经典部署模型迁移到 Resource Manager 的 IaaS 资源](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager)
- [有关平台支持的从经典部署模型到 Resource Manager 的迁移的技术深入探讨](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager-deep-dive)
- [使用 Community PowerShell 脚本将经典虚拟机克隆到 Azure Resource Manager](/documentation/articles/virtual-machines-windows-migration-scripts)

<!---HONumber=Mooncake_0613_2016-->