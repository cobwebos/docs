<!-- not suitable for Mooncake -->

<properties
	pageTitle="使用 Azure CLI 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager"
	description="本文逐步讲解平台支持的迁移服务功能：使用 Azure CLI 从服务管理迁移到 Azure Resource Manager"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.date="05/04/2016"
	wacn.date=""/>

# 使用 Azure CLI 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager

以下步骤说明如何使用 Azure CLI 命令将 IaaS 资源从经典部署模型迁移到 Resource Manager。本文中的操作需要用到已登录的 [Azure CLI](/documentation/articles/xplat-cli-install) (`azure login -e AzureChinaCloud`)。

## 步骤 1：准备迁移

下面是建议在评估从经典迁移 IaaS 资源到 Resource Manager 的一些最佳实践

- 请阅读[此文](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager)来了解不受支持的配置或功能。如果虚拟机使用不受支持的配置或功能，建议你等到宣布支持该功能/配置再进行迁移。或者，可以删除该功能或移出该配置，以利迁移进行（如果这样做符合要求）。
-	如果是用自动化的脚本部署基础结构和应用程序。请尝试使用这些迁移用脚本创建类似的测试设置。或者，也可以使用 Azure 门户设置示例环境。
- 因为服务目前是公共预览版本，请确保用于迁移的测试环境与生产环境隔离。请勿在测试和生产环境之间混用存储帐户、VNET 或其他资源。

## 步骤 2：设置订阅并注册迁移公共预览版

对于迁移方案，需要针对经典和 Resource Manager 设置环境。[安装 Azure CLI](/documentation/articles/xplat-cli-install) 并[选择订阅](/documentation/articles/xplat-cli-connect)。

使用以下命令选择 Azure 订阅

	azure account set "azure-subscription-name"

使用以下命令注册公共预览版。请注意，在某些情况下，此命令将会超时，但注册会成功。请执行下一步骤来检查注册状态。

	azure provider register Microsoft.ClassicInfrastructureMigrate

等 5 分钟让注册完成。可以使用以下命令来检查审批状态。在继续进一步之前，应确保 RegistrationState 为“Registered”。

	azure provider show Microsoft.ClassicInfrastructureMigrate

现在请将 CLI 切换到 asm 模式

	azure config mode asm

## 步骤 3：用于迁移 IaaS 资源的命令

>[AZURE.NOTE] 请注意，下述所有操作都是幂等的。如果你遇到不支持的功能或配置错误以外的任何情况，建议再试一次准备、中止或提交操作，然后平台将重试操作。

### 迁移云服务中的虚拟机（不在虚拟网络中）

使用以下命令获取云服务列表，然后选择要迁移的云服务。请注意，如果云服务中的 VM 在 VNET 中或者具有 Web/辅助角色，你将收到错误消息。

	azure service list

运行以下命令，从详细输出中获取云服务的部署名称。在大多数情况下，部署名称与云服务名称相同。

	azure service show servicename -vv

准备迁移云服务中的虚拟机。可以选择两个选项。

如果想要将 VM 迁移到创建虚拟网络的平台上，请使用以下命令。

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

如果想要迁移到 Resource Manager 部署模型中的现有虚拟网络，请使用以下命令。

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

准备操作成功后，可以查看详细输出，以获取 VM 的迁移状态，并确保 VM 处于“已准备”状态。

	azure vm show vmName -vv

使用 CLI 或 Azure 门户检查已准备好的资源的配置。如果尚未准备好进行迁移并想要回到旧状态，请使用以下命令。

	azure service deployment abort-migration serviceName deploymentName

如果准备好的配置看起来没问题，可以继续进行，使用以下命令提交资源。

	azure service deployment commit-migration serviceName deploymentName

### 迁移虚拟网络中的虚拟机

选择要迁移的虚拟网络。请注意，虚拟网络是否包含 Web/辅助角色，以及 VM 是否有不支持的配置，若有，你将收到验证错误消息。

使用以下命令获取订阅中的所有虚拟网络。

	azure network vnet list

使用以下命令来准备要迁移的所选虚拟网络。

	azure network vnet prepare-migration virtualnetworkname

使用 CLI 或 Azure 门户检查已准备好的虚拟机的配置。如果尚未准备好进行迁移并想要回到旧状态，请使用以下命令。

	azure network vnet abort-migration virtualnetworkname

如果准备好的配置看起来没问题，可以继续进行，使用以下命令提交资源。

	azure network vnet commit-migration virtualnetworkname

## 参考

- [平台支持从经典部署模型迁移到 Resource Manager 的 IaaS 资源](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager)
- [有关平台支持的从经典部署模型到 Resource Manager 的迁移的技术深入探讨](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager-deep-dive)

<!---HONumber=Mooncake_0620_2016-->