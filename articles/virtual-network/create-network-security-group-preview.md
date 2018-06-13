---
title: 使用 Azure 网络和应用程序安全组（预览版）筛选网络流量 | Microsoft Docs
description: 学习如何创建网络和应用程序安全组（预览版）来限制进出虚拟机的网络流量的类型。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2018
ms.locfileid: "30294220"
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>使用网络和应用程序安全组（预览版）筛选网络流量

在本教程中，将学习如何创建网络安全组来通过应用程序安全组筛选进出虚拟机组的网络流量。 若要深入了解网络安全组和应用程序安全组，请参阅[安全性概述](security-overview.md)。 

以下各部分提供了使用 Azure 命令行接口 ([Azure CLI](#azure-cli)) 和 [Azure PowerShell](#powershell) 创建网络安全组的步骤。 不管使用哪种工具来创建网络安全组，结果都是一样的。 单击工具链接，转到教程中该工具的对应部分。 

本文提供了通过资源管理器部署模型（创建网络安全组时建议使用的部署模型）创建网络安全组的步骤。 如果需要创建网络安全组（经典），请参阅[创建网络安全组（经典）](virtual-networks-create-nsg-classic-ps.md)。 如果不熟悉 Azure 的部署模型，请阅读[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

> [!NOTE]
> 本教程利用了当前处于预览版的网络安全组功能。 预览版功能的可用性和可靠性与正式版不同。 如果希望仅使用正式版中的功能实现网络安全组，请参阅[创建网络安全组](virtual-networks-create-nsg-arm-pportal.md)。 

## <a name="azure-cli"></a>Azure CLI

无论是从 Windows、 Linux 还是 macOS 执行命令，Azure CLI 命令都相同。 不过在操作系统 shell 之间存在脚本差异。 以下步骤中的脚本在 Bash shell 中执行。 

1. [安装并配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 输入 `az --version` 命令，确保使用的是版本 2.0.18 或更高版本的 Azure CLI。 如果不是，请安装最新版本。
3. 使用 `az login` 命令登录到 Azure。
4. 输入以下命令，针对预览版进行注册：
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. 输入以下命令，确认已针对预览版进行了注册：

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > 注册可能需要多达一小时才能完成。 在上一命令返回的输出中的“状态”显示为 Registered 之前，不要继续执行剩余的步骤。 如果在完成注册之前继续执行，则剩余的步骤将失败。

6. 运行以下 bash 脚本，创建资源组：

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. 创建三个应用程序安全组，每个服务器类型一个：

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. 创建网络安全组：

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. 在 NSG 内创建安全规则，将应用程序安全组设置为目标：
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. 创建虚拟网络： 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. 将网络安全组关联到虚拟网络中的子网：

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. 创建三个网络接口，每个服务器类型一个： 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    只有在步骤 9 中创建的对应安全规则会根据网络接口所属的应用程序安全组应用于网络接口。 例如，对于 myNic2，只有 AppRule 规则会生效，因为该网络接口是 AppServers 应用程序安全组的成员并且该规则将 AppServers 应用程序安全组指定为其目标。 WebRule 和 DatabaseRule 规则不会应用于 myNic2，因为该网络接口不是 WebServers 和 DatabaseServers 应用程序安全组的成员。 但是，对于 *myNic1*，*WebRule* 和 *AppRule* 规则都会生效，因为 *myNic1* 网络接口同时是 *WebServers* 和 *AppServers* 应用程序安全组的成员并且这些规则将 *WebServers* 和 *AppServers* 应用程序安全组指定为其目标。 

13. 为每个服务器类型创建一台虚拟机，将对应的网络接口附加到每台虚拟机。 此示例将创建 Windows 虚拟机，但是你可以将 *win2016datacenter* 更改为 *UbuntuLTS* 来改为创建 Linux 虚拟机。

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. 可选：若要删除在本教程中创建的资源，请完成[删除资源](#delete-cli)中所述的步骤。

## <a name="powershell"></a>PowerShell

1. 安装并配置 [PowerShell](/powershell/azure/install-azurerm-ps)。
2. 确保安装了 4.4.0 版或更高版本的 AzureRm 模块。 可以通过输入 `Get-Module -ListAvailable AzureRM` 命令来检查当前安装的版本。 如果需要进行安装或升级，请从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureRM)安装最新版本的 AzureRM 模块。
3. 在 PowerShell 会话中，使用 `login-azurermaccount` 命令以 [Azure 帐户](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登录到 Azure。
4. 输入以下命令，针对预览版进行注册：
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. 输入以下命令，确认已针对预览版进行了注册：

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > 注册可能需要多达一小时才能完成。 在上一命令返回的输出中的“RegistrationState”显示为“Registered”之前，不要继续执行剩余的步骤。 如果在完成注册之前继续执行，则剩余的步骤将失败。
        
6. 创建资源组：

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. 创建三个应用程序安全组，每个服务器类型一个：

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. 为每个服务器类型创建安全规则。
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. 创建网络安全组：

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. 创建一个子网配置并将网络安全组关联到它：
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. 创建虚拟网络： 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. 创建三个网络接口，每个服务器类型一个。 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    只有在步骤 8 中创建的对应安全规则会根据网络接口所属的应用程序安全组应用于网络接口。 例如，对于 myNic2，只有 AppRule 规则会生效，因为该网络接口是 AppServers 应用程序安全组的成员并且该规则将 AppServers 应用程序安全组指定为其目标。 WebRule 和 DatabaseRule 规则不会应用于 myNic2，因为该网络接口不是 WebServers 和 DatabaseServers 应用程序安全组的成员。 但是，对于 *myNic1*，*WebRule* 和 *AppRule* 规则都会生效，因为 *myNic1* 网络接口同时是 *WebServers* 和 *AppServers* 应用程序安全组的成员并且这些规则将 *WebServers* 和 *AppServers* 应用程序安全组指定为其目标。 

13. 为每个服务器类型创建一台虚拟机，将对应的网络接口附加到每台虚拟机。 此示例将创建 Windows 虚拟机，但在执行脚本之前，可以将 *-Windows* 更改为 *-Linux*，将 *MicrosoftWindowsServer* 更改为 *Canonical*，将 *WindowsServer* 更改为 *UbuntuServer* 并将 *2016-Datacenter* 更改为 *14.04.2-LTS*，从而改为创建 Linux 虚拟机。

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. 可选：若要删除在本教程中创建的资源，请完成[删除资源](#delete-cli)中所述的步骤。

## <a name="remove-a-nic-from-an-asg"></a>从 ASG 中删除 NIC
从应用程序安全组中删除网络接口后，将不会对已删除的网络接口应用指定应用程序安全组的规则。

### <a name="azure-cli"></a>Azure CLI

若要从所有应用程序安全组中删除 *myNic3*，请输入以下命令：

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

若要从所有应用程序安全组中删除 *myNic3*，请输入以下命令：

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>删除资源

完成本教程后，可以删除创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 在门户的搜索框中，输入 **myResourceGroup**。 在搜索结果中，单击“myResourceGroup”。
2. 在“myResourceGroup”边栏选项卡中，单击“删除”图标。
3. 若要确认删除，请在“键入资源组名称”框中输入 **myResourceGroup**，然后单击“删除”。

### <a name="delete-cli"></a>Azure CLI

在 CLI 会话中输入以下命令：

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 会话中输入以下命令：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

