<properties
	pageTitle="配合使用 Azure PowerShell 和 Azure Resource Manager 通过 Azure 来保护服务器 | Azure"
	description="在 Azure 中通过使用 PowerShell 和 Azure Resource Manager 的 Azure Site Recovery 自动实现对服务器的保护。"
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.date="03/16/2016"
	ms.author="bsiva"/>

# 使用 PowerShell 和 Azure Resource Manager 在本地 Hyper-V 虚拟机与 Azure 之间复制。

> [AZURE.SELECTOR]
- [Azure 经典门户](/documentation/articles/site-recovery-hyper-v-site-to-azure)
- [PowerShell - 资源管理器](/documentation/articles/site-recovery-deploy-with-powershell-resource-manager)



## 概述

Azure Site Recovery 可在许多部署方案中安排虚拟机的复制、故障转移和恢复，为业务连续性和灾难恢复 (BCDR) 策略发挥作用。有关部署方案的完整列表，请参阅 [Azure Site Recovery 概述](/documentation/articles/site-recovery-overview)。

Azure PowerShell 是一个模块，提供用于通过 Windows PowerShell 管理 Azure 的 cmdlet。它可与两种类型的模块配合工作：Azure 配置文件模块或 Azure 资源管理器 (ARM) 模块。

Azure Site Recovery(ASR) PowerShell cmdlet 在 Azure PowerShell for ARM 中提供，适用于保护和恢复你在 Azure 中的服务器。

本文通过示例介绍如何使用 Windows PowerShell® 及 ARM 来部署 Azure Site Recovery，以便配置和安排对 Azure 的服务器保护。本文所用示例演示了如何通过联合使用Azure PowerShell 和 ARM 对 Hyper-V 主机上的虚拟机进行保护，以及如何将这些虚拟机故障转移和恢复到 Azure。

> [AZURE.NOTE] Azure Site Recovery PowerShell cmdlet 目前允许你配置 VMM 站点到 VMM 站点方案、VMM 站点到 Azure 方案以及 Hyper-V 站点到 Azure 方案。很快会增加对其他 ASR 方案的支持。

你无需是一名 PowerShell 专家就可以使用本文章，但是本文假定你理解诸如模块、cmdlet 和会话等基本概念。有关 Windows PowerShell 的详细信息，请参阅 [Windows PowerShell 入门](http://technet.microsoft.com/zh-cn/library/hh857337.aspx)。
- 阅读有关[将 Azure PowerShell 与 Azure Resource Manager 配合使用](/documentation/articles/powershell-azure-resource-manager)的更多信息。


## 主要功能

- 通过 Azure 保护你的服务器，以及将其故障转移和恢复到 Azure IaaS (ARM) 或 Azure IaaS（经典）
- 参与云解决方案提供商 (CSP) 计划的 Microsoft 合作伙伴可以根据客户的 CSP 订阅（租户订阅）情况对客户服务器的保护措施进行配置和管理

## 开始之前

确保已满足以下先决条件：

- 需要一个 [Microsoft Azure](https://azure.cn/) 帐户。你可以从[免费试用版](/pricing/1rmb-trial/)开始。此外，你可以阅读 [Azure Site Recovery Manager 定价](/home/features/site-recovery/#price)。
- 你将需要 Azure PowerShell 1.0。有关此版本及其安装方法的信息，请参阅 [Azure PowerShell 1.0](/documentation/articles/powershell-install-configure)。
- 你将需要安装 [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) 和 [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) 模块。你可以从 [PowerShell 库](https://www.powershellgallery.com/)获取这些模块的最新版本

本文将举例说明如何使用 Azure Powershell 和 ARM 来配置和管理对服务器的保护。本文中使用的示例说明了如何通过 Azure 来保护在 Hyper-V 主机上运行的虚拟机，并随后说明了特定于本示例的先决条件。如需不同 ASR 方案的各种详细要求，请参阅与该方案相关的文档。

- 你将需要一台运行 Windows Server 2012 R2 的 Hyper-V 主机，其中包含一个或多个虚拟机。
- Hyper-V 服务器应直接或通过代理连接到 Internet。
- 要保护的虚拟机应符合[虚拟机先决条件](/documentation/articles/site-recovery-best-practices#virtual-machines)。
	

## 步骤 1：登录到你的 Azure 帐户


1. 打开 PowerShell 控制台，然后运行以下命令以登录到 Azure 帐户。该 cmdlet 会打开一个网页，提示你输入帐户凭据。

    	Login-AzureRmAccount -EnvironmentName AzureChinaCloud

	此外，你也可以通过 `-Credential` 参数将帐户凭据作为参数包括到 `Login-AzureRmAccount` cmdlet 中。
	
	如果你是代表租户的 CSP 合作伙伴，则需使用 tenantID 或租户主域名将客户指定为一名租户

		Login-AzureRmAccount -EnvironmentName AzureChinaCloud -Tenant "fabrikam.com"

2. 一个帐户可以有多个订阅，因此你需要将你要使用的订阅与帐户关联在一起。

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  使用以下命令验证你的订阅是否已注册，因此可以将 Azure 提供程序用于恢复服务和 Site Recovery：-

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	如果在上述两个命令的输出中已将 RegistrationState 设置为 Registered，则可继续执行步骤 2。否则，你将需要注册订阅中缺失的提供程序。


	若要注册 Site Recovery 的 Azure 提供程序，请执行以下操作：

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
	
	同样，如果你是第一次使用订阅中的恢复服务 cmdlet，则需注册针对恢复服务的 Azure 提供程序。在执行该操作之前，你需要先执行以下命令，以便在订阅中启用对恢复服务提供程序的访问权限：

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP] 成功完成以上命令以后，可能需要长达一小时的时间才能启用对订阅中恢复服务提供程序的访问权限。尝试使用 `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices` 命令注册订阅中的恢复服务提供程序可能会中途失败。如果发生这种情况，请等待一个小时，然后重试。

	启用对订阅中恢复服务提供程序的访问权限以后，即可执行以下命令，以便注册订阅中的提供程序

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	使用 `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` 和 `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery` 命令验证提供程序是否已成功注册

	

## 步骤 2：设置恢复服务保管库

1. 创建一个可在其中创建该保管库的 ARM 资源组，或者使用现有资源组。你可以使用以下命令创建新的 ARM 资源组：

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	其中，$ResourceGroupName 变量包含需要创建的 ARM 资源组的名称，$Geo 变量包含可在其中创建资源组的 Azure 区域（例如：巴西南部）

	你可以使用 `Get-AzureRmResourceGroup` cmdlet 获取订阅中 ARM 资源组的列表。

2. 创建如下所示的新的 Azure 恢复服务保管库：-

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResouceGroupName <string> -Location <string>

	你可以使用 `Get-AzureRmRecoveryServicesVault` cmdlet 检索现有保管库的列表。

> [AZURE.NOTE] 如果你希望在通过经典门户或 Azure 服务管理 PowerShell 模块创建的 ASR 保管库上执行操作，则可使用 `Get-AzureRmSiteRecoveryVault` cmdlet 检索此类保管库的列表。建议所有新操作都创建新的恢复服务保管库。你此前创建的 Site Recovery 保管库将继续受到支持，但不会有最新功能。

## 步骤 3：生成保管库注册密钥

1. 为你的保管库生成和下载保管库注册密钥。

    	Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -Path $path
2. 导入已下载的保管库设置文件，以设置保管库上下文
 
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $path 

## 步骤 4：创建 Hyper-V 站点，然后为该站点生成新的保管库注册密钥。

1. 创建新的 Hyper-V 站点，如下所示：
		
		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	此 cmdlet 会启动一个创建该站点所需的 ASR 作业，然后返回一个 ASR 作业对象。等待作业完成，然后验证作业已成功完成。

	你可以检索 ASR 作业对象，以便使用 Get-AzureRmSiteRecoveryJob cmdlet 查看当前的作业状态 
2. 生成和下载站点的注册密钥：-

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
	
	将已下载的密钥复制到 Hyper-V 主机。你将需要通过该密钥将 Hyper-V 主机注册到站点

	
## 步骤 5：在 Hyper-V 主机上安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理

1. 从[此处](https://aka.ms/downloaddra)下载最新版本的提供程序的安装程序
2. 在 Hyper-V 主机上运行安装程序，在安装结束时继续执行“注册”步骤
3. 在系统提示时提供下载的站点注册密钥，然后完成注册过程，将 Hyper-V 主机注册到站点
4. 使用以下命令验证 Hyper-V 主机是否已注册到站点：

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname 

## 步骤 6：创建复制策略，然后将其与保护容器相关联

1. 创建复制策略：-

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id 

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	检查返回的作业，确保复制策略创建成功。

	>[AZURE.IMPORTANT] 指定的存储帐户应与恢复服务保管库处于同一 Azure 区域，并且应已启用异地复制。
	>
	> - 如果指定的恢复存储帐户的类型为 Azure 存储空间（经典），则在对受保护计算机进行故障转移时，会将该计算机恢复为 Azure IaaS（经典）
	> - 如果指定的恢复存储帐户的类型为 Azure 存储空间 (ARM)，则在对受保护计算机进行故障转移时，会将该计算机恢复为 Azure IaaS (ARM)
	
 
2. 获取对应于该站点的保护容器：-
		
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	开始将保护容器与复制策略相关联：

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	等待关联作业完成，确保它已成功完成

##步骤 7：为虚拟机启用保护

1. 获取与你要保护的 VM 相对应的保护实体：-
		
		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM 
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. 开始保护虚拟机：
		
		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT] 指定的存储帐户应与恢复服务保管库处于同一 Azure 区域，并且应已启用异地复制。
	>
	> - 如果指定的恢复存储帐户的类型为 Azure 存储空间（经典），则在对受保护计算机进行故障转移时，会将该计算机恢复为 Azure IaaS（经典）
	> - 如果指定的恢复存储帐户的类型为 Azure 存储空间 (ARM)，则在对受保护计算机进行故障转移时，会将该计算机恢复为 Azure IaaS (ARM)

	> 如果你要保护的 VM 有多个附加磁盘，则需使用 OSDiskName 参数指定操作系统磁盘。

3. 等待虚拟机在完成初始复制后进入受保护状态。这将需要一段时间，具体取决于要复制的数据量、可供 Azure 使用的上游带宽等因素。在 VM 进入受保护状态后，作业状态和 StateDescription 将会更新如下。

		
		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. 更新各种恢复属性，例如 VM 角色大小、进行故障转移时需要将 VM 的 NIC 连接到的 Azure 网络。

		PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

		PS C:\> $VMFriendlyName = "Fabrikam-App"

		PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

		PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

		PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

		PS C:\> $UpdateJob


		Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
		                   s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		Type             : Microsoft.RecoveryServices/vaults/replicationJobs
		JobType          : UpdateVmProperties
		DisplayName      : Update the virtual machine
		ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
		State            : Succeeded
		StateDescription : Completed
		StartTime        : 10-12-2015 17:55:53 +00:00
		EndTime          : 10-12-2015 17:55:54 +00:00
		TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
		TargetObjectType : ProtectionEntity
		TargetObjectName : Fabrikam-App
		AllowedActions   : {Restart}
		Tasks            : {UpdateVmPropertiesTask}
		Errors           : {}



## 步骤 8：运行测试故障转移

1. 运行一个测试故障转移作业：
		
		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. 确保已在 Azure 中创建测试 VM（在 Azure 中创建测试 VM 之后，将暂停测试故障转移作业。在恢复该作业后，将清除已创建的项目，此时作业才算完成，如下一步骤所示）

3. 完成测试故障转移

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=Mooncake_0328_2016-->