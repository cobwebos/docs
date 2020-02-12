---
title: 通过 PowerShell 迁移到资源管理器
description: 本文介绍了平台支持的将 IaaS 资源（例如虚拟机（Vm）、虚拟网络和存储帐户）从经典部署模型迁移到 Azure 资源管理器使用 Azure PowerShell 命令
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 802d97e2c9b64fd9d8caeaf479af3f4aec356607
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153122"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器
以下步骤演示了如何使用 Azure PowerShell 命令将基础结构即服务 (IaaS) 资源从经典部署模型迁移到 Azure 资源管理器部署模型。

如果需要，还可以使用[Azure CLI](../linux/migration-classic-resource-manager-cli.md)迁移资源。

* 如需了解受支持的迁移方案的背景信息，请参阅[平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移](migration-classic-resource-manager-overview.md)。
* 如需详细的指南和迁移演练，请参阅[从技术方面深入探讨如何在支持的平台上完成从经典部署模型到 Azure 资源管理器部署模型的迁移](migration-classic-resource-manager-deep-dive.md)。
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)。

<br>
下面是一个流程图，用于确定在迁移过程中需要执行步骤的顺序。

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>步骤 1：做好迁移规划
下面是一些最佳实践，我们建议你在评估是否将 IaaS 资源从经典部署模型迁移到资源管理器：

* 通读[受支持的和不受支持的功能和配置](migration-classic-resource-manager-overview.md)。 如果虚拟机使用不受支持的配置或功能，请等待配置或功能支持公布。 也可根据需要删除该功能或移出该配置，以利迁移进行。
* 如果通过自动化脚本来部署目前的基础结构和应用程序，则可尝试使用这些脚本进行迁移，以便创建类似的测试性设置。 也可以使用 Azure 门户设置示例环境。

> [!IMPORTANT]
> 目前，不支持将应用程序网关从经典部署模型迁移到资源管理器。 若要迁移包含应用程序网关的虚拟网络，请先删除该网关，然后运行准备操作来移动网络。 完成迁移后，在 Azure 资源管理器中重新连接该网关。
>
> 不能自动迁移连接到其他订阅中的 ExpressRoute 线路的 Azure ExpressRoute 网关。 在这种情况下，请删除 ExpressRoute 网关、迁移虚拟网络并重新创建网关。 有关详细信息，请参阅[将 ExpressRoute 线路和关联的虚拟网络从经典部署模型迁移到资源管理器部署模型](../../expressroute/expressroute-migration-classic-resource-manager.md)。

## <a name="step-2-install-the-latest-version-of-powershell"></a>步骤2：安装最新版本的 PowerShell
安装 Azure PowerShell 可以通过两个主要的选项：[PowerShell 库](https://www.powershellgallery.com/profiles/azure-sdk/)或 [Web 平台安装程序 (WebPI)](https://aka.ms/webpi-azps)。 WebPI 接收每月的更新。 PowerShell 库会持续接收更新。 本文基于 Azure PowerShell 2.1.0 版。

如需安装说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

<br>

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>步骤3：确保你是订阅的管理员
若要执行此迁移，必须在[Azure 门户](https://portal.azure.com)中为订阅添加共同管理员。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 "**中心**" 菜单上，选择 "**订阅**"。 如果看不到该选项，请选择“所有服务”。
3. 找到相应的订阅条目，然后查看 "我的**角色**" 字段。 对于共同管理员，该值应为_帐户管理员_。

如果无法添加共同管理员，请联系订阅的服务管理员或共同管理员以自行添加。

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>步骤4：设置订阅并注册迁移
首先，请启动 PowerShell 提示符。 对于迁移，请将环境设置为经典和资源管理器。

登录到 Resource Manager 模型的帐户。

```powershell
    Connect-AzAccount
```

使用以下命令获取可用订阅：

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

设置当前会话的 Azure 订阅。 此示例将默认订阅名称设置为“我的 Azure 订阅”。 将示例订阅名称替换成自己的名称。

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> 注册是一次性步骤，但必须在尝试迁移之前执行一次。 如果不注册，则会出现以下错误消息：
>
> *BadRequest : Subscription is not registered for migration.*

使用以下命令向迁移资源提供程序注册：

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

等待5分钟，完成注册。 使用以下命令检查审批状态：

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

请确保在继续操作之前，RegistrationState 为 `Registered`。

现在，请登录到经典部署模型的帐户。

```powershell
    Add-AzureAccount
```

使用以下命令获取可用订阅：

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

设置当前会话的 Azure 订阅。 此示例将默认订阅设置为“我的 Azure 订阅”。 将示例订阅名称替换成自己的名称。

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-have-enough-resource-manager-vm-vcpus"></a>步骤5：拥有足够的资源管理器 VM 个 vcpu
请确保在当前部署或虚拟网络的 Azure 区域中有足够的 Azure 资源管理器虚拟机个 vcpu。 可以使用以下 PowerShell 命令检查 Azure 资源管理器中目前的 vCPU 数量。 若要了解有关 vCPU 配额的详细信息，请参阅[限制和 Azure 资源管理器](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)。

此示例检查在“美国西部”区域中的可用性。 将示例区域名称替换成自己的名称。

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>步骤 6：运行迁移 IaaS 资源的命令
* [迁移云服务中的 Vm （不在虚拟网络中）](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [迁移虚拟网络中的 VM](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [迁移存储帐户](#step-62-migrate-a-storage-account)

> [!NOTE]
> 此处描述的所有操作都是幂等的。 如果遇到功能不受支持或配置错误以外的问题，建议重试准备、中止或提交操作。 然后，平台会尝试再次该操作。


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>步骤 6.1：选项 1 - 迁移云服务中的虚拟机（不在虚拟网络中）
使用以下命令获取云服务列表。 然后选取要迁移的云服务。 如果云服务中的 VM 在虚拟网络中或者具有 Web 角色或辅助角色，该命令会返回错误消息。

```powershell
    Get-AzureService | ft Servicename
```

获取云服务的部署名称。 在此示例中，服务名称是“我的服务”。 将示例服务名称替换成自己的服务名称。

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

准备迁移云服务中的虚拟机。 可以从两个选项中进行选择。

* **选项1：将 Vm 迁移到平台所创建的虚拟网络。**

    首先，使用以下命令验证是否可以迁移云服务：

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    以下命令会显示任何阻止迁移的警告和错误。 如果验证成功，则可以转到 "准备" 步骤。

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **选项2：迁移到资源管理器部署模型中的现有虚拟网络。**

    此示例将资源组名称设置为**myResourceGroup**，将虚拟网络名称设置为**myVirtualNetwork**，将子网名称设置为**mySubNet**。 将该示例中的名称替换成自己的资源名称。

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    首先，使用以下命令验证是否可以迁移虚拟网络：

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    以下命令会显示任何阻止迁移的警告和错误。 如果验证成功，则可以继续执行以下准备步骤：

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

使用前述任一选项成功完成准备操作以后，即可查询 VM 的迁移状态。 确保它们处于 `Prepared` 状态。

此示例将 VM 名称设置为 **myVM**。 将示例名称替换成自己的 VM 名称。

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

使用 PowerShell 或 Azure 门户查看准备就绪的资源的配置。 如果尚未准备好进行迁移，并且想要返回到旧状态，请使用以下命令：

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>步骤 6.1：选项 2 - 迁移虚拟网络中的虚拟机

若要迁移虚拟网络中的虚拟机，可迁移虚拟网络。 虚拟机随虚拟网络自动迁移。 选取要迁移的虚拟网络。
> [!NOTE]
> 通过使用虚拟机的 VHD （OS 和数据）文件，通过使用托管磁盘创建新的资源管理器虚拟机来迁移使用经典部署模型创建的[单个虚拟机](migrate-single-classic-to-resource-manager.md)。
<br>

> [!NOTE]
> 虚拟网络名称可能与新门户中显示的名称不同。 新 Azure 门户将名称显示为 `[vnet-name]`，但实际虚拟网络名称的类型为 `Group [resource-group-name] [vnet-name]`。 在开始迁移之前，请使用命令 `Get-AzureVnetSite | Select -Property Name` 查找实际的虚拟网络名称，或在旧 Azure 门户中查看。 

此示例将虚拟网络名称设置为 **myVnet**。 将示例虚拟网络名称替换成自己的名称。

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 如果虚拟网络包含的 Web 角色/辅助角色或 VM 的配置不受支持，则会出现验证错误消息。

首先，使用以下命令验证是否可以迁移虚拟网络：

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

以下命令会显示任何阻止迁移的警告和错误。 如果验证成功，则可以继续执行以下准备步骤：

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

使用 Azure PowerShell 或 Azure 门户查看准备就绪的虚拟机的配置。 如果尚未准备好进行迁移，并且想要返回到旧状态，请使用以下命令：

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>步骤6.2：迁移存储帐户
迁移完虚拟机后，在迁移存储帐户之前，请执行以下先决条件检查。

> [!NOTE]
> 如果你的存储帐户没有关联的磁盘或 VM 数据，则可以直接跳到 "验证存储帐户并开始迁移" 部分。

* 先决条件检查是否已迁移任何 Vm，或者存储帐户是否有磁盘资源：
    * 迁移其磁盘存储在存储帐户中的虚拟机。

        以下命令将返回存储帐户中所有 VM 磁盘的 DiskName 和属性。 RoleName 是磁盘附加到的虚拟机的名称。 如果此命令返回磁盘，则在迁移存储帐户之前，请确保已将这些磁盘附加到的虚拟机迁移。
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * 删除存储帐户中存储的未附加 VM 磁盘。

        使用以下命令查找存储帐户中未附加的 VM 磁盘：

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        如果上一个命令返回了磁盘，请使用以下命令删除这些磁盘：

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * 删除存储帐户中存储的 VM 映像。

        以下命令将返回存储帐户中存储有 OS 磁盘的所有 VM 映像。
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         以下命令返回数据磁盘存储在该存储帐户中的所有 VM 映像。
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        使用以下命令删除前面的命令返回的所有 VM 映像：
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* 验证存储帐户并开始迁移。

    使用以下命令验证要迁移的每个存储帐户。 在此示例中，存储帐户名称是 **myStorageAccount**。 将该示例名称替换成自己的存储帐户名称。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    下一步是准备要迁移的存储帐户。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    使用 Azure PowerShell 或 Azure 门户查看准备就绪的存储帐户的配置。 如果尚未准备好进行迁移，并且想要返回到旧状态，请使用以下命令：

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>后续步骤
* [平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移概述](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [有关平台支持的从经典部署模型到 Azure 资源管理器部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [规划从经典部署模型到 Azure 资源管理器的 IaaS 资源迁移](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [用于帮助将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的社区工具](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [查看有关将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的最常见问题](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
