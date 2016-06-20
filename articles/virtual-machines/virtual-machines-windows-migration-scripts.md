<!-- not suitable for Mooncake -->

<properties
	pageTitle="使用 PowerShell 脚本将经典虚拟机克隆到 Azure Resource Manager"
	description="本文说明如何使用 PowerShell 脚本将单个经典虚拟机克隆到 Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.date="05/03/2016"
	wacn.date=""/>

# 使用 PowerShell 脚本将经典虚拟机克隆到 Azure Resource Manager

本文说明如何使用 [Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration) 中的脚本将**单个**经典虚拟机克隆到 Azure Resource Manager 部署模型。

>[AZURE.IMPORTANT]使用这些脚本克隆可能造成经典虚拟机停机。如果你想要了解平台支持的迁移，请访问
- [Platform supported migration of IaaS resources from Classic to Azure Resource Manager stack（平台支持从经典部署模型迁移到 Azure Resource Manager 堆栈的 IaaS 资源）](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager)
- [有关平台支持的从经典部署模型到 Azure Resource Manager 的迁移的技术深入探讨](/documentation/articles/virtual-machines-windows-migration-classic-resource-manager-deep-dive)
- [Migrate IaaS resources from Classic to Azure Resource Manager using Azure PowerShell（使用 Azure PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager）](/documentation/articles/virtual-machines-windows-ps-migration-classic-resource-manager)

## 获取脚本

>[AZURE.NOTE]Microsoft 支持服务并不正式支持 PowerShell 脚本。因此 Github 上提供了其开放源代码，我们很乐意接受有关修复或其他方案的 PR。

若要获取脚本，可以从上述存储库下载 zip 文件，或使用以下**任一**命令来克隆存储库

	git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git

**或者**

	git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git

## 导入迁移模块

下一步是将模块导入 PowerShell 会话。这可以使用以下命令来实现

	Import-Module .\asm2arm.psd1

## 使用经典部署模型和 Azure Resource Manager 订阅进行身份验证

在克隆经典虚拟机之前，需要向经典部署模型和 Azure Resource Manager 堆栈验证 PowerShell 会话。可以使用以下 cmdlet 实现此目的

	Add-AzureAccount -Environment AzureChinaCloud
	Login-AzureRmAccount -EnvironmentName AzureChinaCloud


>[AZURE.IMPORTANT]确保使用 `Select-AzureSubscription`（对于经典部署模型）和 AzureRmContext（对于 Azure Resource Manager）选择默认订阅

## 使用脚本

### cmdlet

导入模块会将下面两个 cmdlet 添加到你的会话。

	Add-AzureSMVmToRM
	New-AzureSmToRMDeployment

这些 cmdlet 可实现下面指定的功能。

#### Add-AzureSMVmToRM
- 在给定虚拟机的情况下，生成一组 Azure Resource Manager 模板和命令性 PowerShell 脚本（用于复制磁盘 Blob，且如果 VM 有 VM 代理扩展）

>[AZURE.IMPORTANT]如果指定了 `-Deploy` 开关，此 cmdlet 将调用 `New-AzureSmToRMDeployment` cmdlet 来部署上面生成的 Azure Resource Manager 模板和命令性 PowerShell 脚本。

#### New-AzureSmToRMDeployment
- 部署 `Add-AzureSMVmToRM` cmdlet 生成的模板和命令性 PowerShell 脚本以开始迁移。

### 识别要克隆的经典虚拟机

为此，可将经典虚拟机状态存储在某个变量中并将该变量传递给 `Add-AzureSMVmToRM` cmdlet，或直接使用 `ServiceName` 和 VM `Name` 参数。识别经典虚拟机后，可以使用 `Add-AzureSMVmToRM` cmdlet 来克隆它。

	$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
	Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy

**或者**

	Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy

>[AZURE.IMPORTANT]在上述示例中，由于我们使用了 `-Deploy` 参数，因此不需要使用 `New-AzureSmToRMDeployment` cmdlet。

## 如何使用这些脚本进行迁移？

cmdlet 将执行克隆经典 VM 的每个步骤，并生成资源作为存储、网络和计算资源提供程序的自定义 PowerShell 哈希表。
这些代表资源的哈希表将附加到数组，然后再通过序列化为 JSON 变成模板并写入文件。

该模板根据经典虚拟机代理扩展的存在状态和 DiskAction 选项值创建文件。这些设置全都保存在 OutputFileFolder 参数指定的目录中。文件如下：

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json`：此文件代表克隆经典虚拟机之前需要准备的资源，可能与任何后续 VM 所需的资源相同（我们不会维护后续运行的状态，但由于执行 Blob 复制操作之前必须以命令方式预配存储帐户，因此将类似的资源合并到一个组中比较符合逻辑）

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json`：包含 Resource Manager 虚拟机的模板
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json`：包含传递给模板的实际参数
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json`：要运行的一组 PowerShell cmdlet，用于设置虚拟机代理扩展
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json`：要运行的一组 PowerShell cmdlet，用于复制磁盘 Blob（如果指定了 CopyDisks 选项）

如果设置了 -Deploy 标志，则生成文件后，cmdlet 将部署 <ServiceName>-<VMName>-setup.json 模板，复制源虚拟机磁盘 Blob（如果 DiskAction 参数设置为 CopyDisks），然后使用 <ServiceName>-<VMName>-parameters.json 文件作为参数部署 <ServiceName>-<VMName>-deploy.json 模板。虚拟机部署完成之后，如果存在命令性脚本（用于虚拟机代理扩展）或用于复制磁盘的脚本，则执行这些脚本。

### 网络详细信息
cmdlet 的作用不是将经典网络设置克隆到 Resource Manager。它以最有利于克隆虚拟机本身的方式来利用网络设施。下面是在不同情况下执行的操作：

1.  目标资源组上没有虚拟网络
    - 源虚拟机不在子网上：创建使用 10.0.0.0/16 的默认虚拟网络，以及地址空间为 10.0.0.0/22 的子网。
    - 源虚拟机位于子网上：发现虚拟机所在的虚拟网络，并复制虚拟网络规范以及子网
2.  目标资源组包含名为 `<VM virtual network>arm` 的虚拟网络（追加“arm”字符串）
    - 如果虚拟网络包含具有相同名称和地址空间的子网，则使用该子网
    - 如果找不到合适的子网，则在现有子网以外查找具有 22 位掩码的地址块并使用该地址块。

## 克隆与平台迁移

当前的克隆方式与平台支持的迁移之间存在一些差异。

### 克隆


| 优点 | 缺点 |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| 克隆云服务中的任何虚拟机 | 由于需要关闭 VM，因此 VM 会出现停机 |
| 克隆 VNET 内部或外部的任何虚拟机 | 在需要复制磁盘 Blob 的方案中会出现较长的延迟 |
| | VM 网络配置更改 - 内部 IP、VIP。 |


### 平台迁移


| 优点 | 缺点 |
|----------------------------------|:-------------------------------------------:|
| 大多数虚拟网络内的 VM 配置不需要停机。 | 云服务中的所有 VM 或 VNET 中部署的所有资源必须一同迁移。 |
 
 
## 不支持的方案

**以下方案不在克隆脚本支持的范围内**

 1. 停止正在运行的虚拟机 
 2. 更改数据/磁盘
 3. 克隆正在运行的 VM
 4. 在复杂方案中自动克隆多个 VM
 5. 克隆整个 ASM 网络配置
 6. 创建负载平衡的 VM。我们认为这是 Azure 专家需要显式处理的配置
 
 
## 经过测试的配置

已使用以下测试案例验证了 _Add-AzureSMVmToRM_ cmdlet：

| # | 说明 |
|:---|:---|
| 1 | 包含现有 OS 磁盘的 Windows 虚拟机 |
| 2 | 包含现有 OS 磁盘的 Linux 虚拟机 |
| 3 | 包含现有 OS 和数据磁盘的 Windows 虚拟机 |
| 4 | 包含现有 OS 和数据磁盘的 Linux 虚拟机 |
| 5 | 包含与映像库匹配的新 OS 磁盘的 Windows 虚拟机 |
| 6 | 包含与映像库匹配的新 OS 磁盘的 Linux 虚拟机 |
| 7 | 包含新 OS 磁盘和空数据磁盘的 Windows 虚拟机 |
| 8 | 包含新 OS 磁盘和空数据磁盘的 Linux 虚拟机 |
| 9 | 包含公共终结点的 Windows 虚拟机 |
| 10 | 包含公共终结点的 Linux 虚拟机 |
| 11 | 具有 WinRM 证书的 Windows 虚拟机 |
| 12 | VNet 和子网中的 Windows 虚拟机 |
| 13 | VNet 和子网中的 Linux 虚拟机 |
| 14 | 具有自定义扩展的 Windows 虚拟机 |
| 15 | 可用性集中的 Windows 虚拟机 |
| 16 | 在可用性集中或 VNet 和子网中、包含多个数据磁盘、公共终结点和自定义扩展的 Windows 虚拟机 |
| 17 | 在可用性集中或 VNet 和子网中、包含多个数据磁盘、公共终结点和自定义扩展的 Linux 虚拟机 |

## 说明
1. 如果逐个克隆多个 VM 且每两次克隆的间隔时间较短，则可能会由于 DNS 缓存刷新时间的缘故，而导致公共 IP 地址发生 DNS 名称冲突。

<!---HONumber=Mooncake_0613_2016-->