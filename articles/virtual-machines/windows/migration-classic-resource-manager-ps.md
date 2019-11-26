---
title: Migrate to Resource Manager with PowerShell
description: This article walks through the platform-supported migration of IaaS resources such as virtual machines (VMs), virtual networks, and storage accounts from classic to Azure Resource Manager by using Azure PowerShell commands
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: f87e7795416431305141de24497e9760eb03641e
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484378"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrate IaaS resources from classic to Azure Resource Manager by using PowerShell
以下步骤演示了如何使用 Azure PowerShell 命令将基础结构即服务 (IaaS) 资源从经典部署模型迁移到 Azure 资源管理器部署模型。

If you want, you can also migrate resources by using the [Azure CLI](../linux/migration-classic-resource-manager-cli.md).

* 如需了解受支持的迁移方案的背景信息，请参阅[平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移](migration-classic-resource-manager-overview.md)。
* 如需详细的指南和迁移演练，请参阅[从技术方面深入探讨如何在支持的平台上完成从经典部署模型到 Azure 资源管理器部署模型的迁移](migration-classic-resource-manager-deep-dive.md)。
* [Review the most common migration errors](migration-classic-resource-manager-errors.md).

<br>
Here's a flowchart to identify the order in which steps need to be executed during a migration process.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>步骤 1：做好迁移规划
Here are a few best practices that we recommend as you evaluate whether to migrate IaaS resources from classic to Resource Manager:

* 通读[受支持的和不受支持的功能和配置](migration-classic-resource-manager-overview.md)。 If you have virtual machines that use unsupported configurations or features, wait for the configuration or feature support to be announced. 也可根据需要删除该功能或移出该配置，以利迁移进行。
* 如果通过自动化脚本来部署目前的基础结构和应用程序，则可尝试使用这些脚本进行迁移，以便创建类似的测试性设置。 也可以使用 Azure 门户设置示例环境。

> [!IMPORTANT]
> Application gateways aren't currently supported for migration from classic to Resource Manager. To migrate a virtual network with an application gateway, remove the gateway before you run a Prepare operation to move the network. 完成迁移后，在 Azure 资源管理器中重新连接该网关。
>
> Azure ExpressRoute gateways that connect to ExpressRoute circuits in another subscription can't be migrated automatically. In such cases, remove the ExpressRoute gateway, migrate the virtual network, and re-create the gateway. For more information, see [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Step 2: Install the latest version of PowerShell
安装 Azure PowerShell 可以通过两个主要的选项：[PowerShell 库](https://www.powershellgallery.com/profiles/azure-sdk/)或 [Web 平台安装程序 (WebPI)](https://aka.ms/webpi-azps)。 WebPI 接收每月的更新。 PowerShell 库会持续接收更新。 本文基于 Azure PowerShell 2.1.0 版。

如需安装说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

<br>

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Step 3: Ensure that you're an administrator for the subscription
To perform this migration, you must be added as a coadministrator for the subscription in the [Azure portal](https://portal.azure.com).

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. On the **Hub** menu, select **Subscription**. 如果看不到该选项，请选择“所有服务”。
3. Find the appropriate subscription entry, and then look at the **MY ROLE** field. For a coadministrator, the value should be _Account admin_.

If you're not able to add a coadministrator, contact a service administrator or coadministrator for the subscription to get yourself added.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Step 4: Set your subscription, and sign up for migration
首先，请启动 PowerShell 提示符。 For migration, set up your environment for both classic and Resource Manager.

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
> Registration is a one-time step, but you must do it once before you attempt migration. 如果不注册，则会出现以下错误消息：
>
> *BadRequest : Subscription is not registered for migration.*

使用以下命令向迁移资源提供程序注册：

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Wait five minutes for the registration to finish. Check the status of the approval by using the following command:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

请确保在继续操作之前，RegistrationState 为 `Registered`。

Now, sign in to your account for the classic deployment model.

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

## <a name="step-5-have-enough-resource-manager-vm-vcpus"></a>Step 5: Have enough Resource Manager VM vCPUs
Make sure that you have enough Azure Resource Manager virtual machine vCPUs in the Azure region of your current deployment or virtual network. 可以使用以下 PowerShell 命令检查 Azure 资源管理器中目前的 vCPU 数量。 若要了解有关 vCPU 配额的详细信息，请参阅[限制和 Azure 资源管理器](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager)。

此示例检查在“美国西部”区域中的可用性。 将示例区域名称替换成自己的名称。

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>步骤 6：运行迁移 IaaS 资源的命令
* [Migrate VMs in a cloud service (not in a virtual network)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [迁移虚拟网络中的 VM](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrate a storage account](#step-62-migrate-a-storage-account)

> [!NOTE]
> 此处描述的所有操作都是幂等的。 如果遇到功能不受支持或配置错误以外的问题，建议重试准备、中止或提交操作。 然后，平台会尝试再次该操作。


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>步骤 6.1：选项 1 - 迁移云服务中的虚拟机（不在虚拟网络中）
Get the list of cloud services by using the following command. Then pick the cloud service that you want to migrate. 如果云服务中的 VM 在虚拟网络中或者具有 Web 角色或辅助角色，该命令会返回错误消息。

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

* **Option 1: Migrate the VMs to a platform-created virtual network.**

    First, validate that you can migrate the cloud service by using the following commands:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    以下命令会显示任何阻止迁移的警告和错误。 If validation is successful, you can move on to the Prepare step.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Option 2: Migrate to an existing virtual network in the Resource Manager deployment model.**

    This example sets the resource group name to **myResourceGroup**, the virtual network name to **myVirtualNetwork**, and the subnet name to **mySubNet**. 将该示例中的名称替换成自己的资源名称。

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    First, validate that you can migrate the virtual network by using the following command:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    以下命令会显示任何阻止迁移的警告和错误。 If validation is successful, you can proceed with the following Prepare step:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

使用前述任一选项成功完成准备操作以后，即可查询 VM 的迁移状态。 Ensure that they're in the `Prepared` state.

此示例将 VM 名称设置为 **myVM**。 将示例名称替换成自己的 VM 名称。

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

使用 PowerShell 或 Azure 门户查看准备就绪的资源的配置。 If you're not ready for migration and you want to go back to the old state, use the following command:

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
> [Migrate a single virtual machine](migrate-single-classic-to-resource-manager.md) created using the classic deployment model by creating a new Resource Manager virtual machine with Managed Disks by using the VHD (OS and data) files of the virtual machine.
<br>

> [!NOTE]
> The virtual network name might be different from what is shown in the new portal. The new Azure portal displays the name as `[vnet-name]`, but the actual virtual network name is of type `Group [resource-group-name] [vnet-name]`. Before you start the migration, look up the actual virtual network name by using the command `Get-AzureVnetSite | Select -Property Name` or view it in the old Azure portal. 

此示例将虚拟网络名称设置为 **myVnet**。 将示例虚拟网络名称替换成自己的名称。

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> 如果虚拟网络包含的 Web 角色/辅助角色或 VM 的配置不受支持，则会出现验证错误消息。

First, validate that you can migrate the virtual network by using the following command:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

以下命令会显示任何阻止迁移的警告和错误。 If validation is successful, you can proceed with the following Prepare step:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

使用 Azure PowerShell 或 Azure 门户查看准备就绪的虚拟机的配置。 If you're not ready for migration and you want to go back to the old state, use the following command:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源：

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Step 6.2: Migrate a storage account
After you're done migrating the virtual machines, perform the following prerequisite checks before you migrate the storage accounts.

> [!NOTE]
> If your storage account has no associated disks or VM data, you can skip directly to the "Validate storage accounts and start migration" section.

* Prerequisite checks if you migrated any VMs or your storage account has disk resources:
    * Migrate virtual machines whose disks are stored in the storage account.

        The following command returns RoleName and DiskName properties of all the VM disks in the storage account. RoleName 是磁盘附加到的虚拟机的名称。 If this command returns disks, then ensure that virtual machines to which these disks are attached are migrated before you migrate the storage account.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Delete unattached VM disks stored in the storage account.

        Find unattached VM disks in the storage account by using the following command:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        If the previous command returns disks, delete these disks by using the following command:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Delete VM images stored in the storage account.

        The following command returns all the VM images with OS disks stored in the storage account.
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
        Delete all the VM images returned by the previous commands by using this command:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Validate storage accounts and start migration.

    使用以下命令验证要迁移的每个存储帐户。 在此示例中，存储帐户名称是 **myStorageAccount**。 将该示例名称替换成自己的存储帐户名称。

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    The next step is to prepare the storage account for migration.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    使用 Azure PowerShell 或 Azure 门户查看准备就绪的存储帐户的配置。 If you're not ready for migration and you want to go back to the old state, use the following command:

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
