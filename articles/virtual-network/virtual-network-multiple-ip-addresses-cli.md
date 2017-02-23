---
title: "Azure 虚拟机的多个 IP 地址 - Azure CLI | Microsoft Docs"
description: "了解如何使用 Azure CLI | Resource Manager 为虚拟机分配多个 IP 地址。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 394315f81cf694cc2bb3a28b45694361b11e0670
ms.openlocfilehash: 8c2441211f08e8bb22153ff16cbd98c85cb9cf3d


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>使用 Azure CLI 将多个 IP 地址分配到虚拟机

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

本文介绍如何使用 Azure CLI 通过 Azure Resource Manager 部署模型创建虚拟机 (VM)。 无法将多个 IP 地址分配到通过经典部署模型创建的资源。 若要详细了解 Azure 部署模型，请阅读 [Understand deployment models](../resource-manager-deployment-model.md)（了解部署模型）一文。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>创建具有多个 IP 地址的 VM

下面的步骤说明如何根据方案中所述，创建具有多个 IP 地址的示例 VM。 在自己的实施项目中，需要更改变量名称和 IP 地址类型。

1. 遵循[安装和配置 Azure CLI](../xplat-cli-install.md) 一文中的步骤安装并配置 Azure CLI，然后登录到你的 Azure 帐户。

2. 登录并选择相应订阅后，请通过在 PowerShell 中运行以下命令注册预览版：
    ```
    Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

    Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
    运行 ```Get-AzureRmProviderFeature``` 命令时，请在看到以下输出后再继续完成剩余的步骤：
        
    ```powershell
    FeatureName                            ProviderName      RegistrationState
    -----------                            ------------      -----------------      
    AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
    AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
    ```
        
    >[!NOTE] 
    >这可能需要几分钟的时间。

3. 请先[创建资源组](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations)，然后创建[虚拟网络和子网](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet)。 将 ``` --address-prefixes ``` 和 ```--address-prefix``` 字段更改为以下值可以遵循本文中所述的确切方案：

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >上述文章使用“西欧”作为位置来创建资源，但本文使用的位置是“美国中西部”。 请相应地更改位置。

4. 为 VM [创建存储帐户](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account)。

5. 创建 NIC，以及要分配给 NIC 的 IP 配置。 可以根据需要添加、删除或更改配置。 本方案说明了以下配置：

    **IPConfig-1**

    输入以下命令来创建：

    - 具有静态公共 IP 地址的公共 IP 地址资源
    - 具有公共 IP 地址资源和动态专用 IP 地址的 IP 配置

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     输入以下命令，创建一个新的公共 IP 地址资源，以及一个具有静态公共 IP 地址和静态专用 IP 地址的新 IP 配置：
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    输入以下命令，创建具有动态专用 IP 地址且没有公共 IP 地址的 IP 配置：

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >尽管本文是将所有 IP 配置分配到单个 NIC，但你也可以将多个 IP 配置分配到 VM 中的任意 NIC。 若要了解如何创建具有多个 NIC 的 VM，请阅读“创建具有多个 NIC 的 VM”一文。

6. [创建 Linux VM](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms) 一文。 请务必删除 ```  --availset-name myAvailabilitySet \ ``` 属性，因为本方案不需要用到它。 根据方案使用适当的位置。 

    >[!WARNING] 
    > 如果 VM 大小不支持选定的位置，“创建 VM”一文中的步骤 6 将会失败。 运行以下命令，获取美国中西部 VM 的完整列表，例如：`azure vm sizes --location westcentralus` 此位置名称可根据方案更改。

    例如，若要将 VM 大小更改为标准 DS2 v2，只需将以下属性 ```  --vm-size Standard_DS3_v2``` 添加到步骤 6 中的 ``` azure vm create ``` 命令。

7. 输入以下命令查看 NIC 和关联的 IP 配置：

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```
8. 将专用 IP 地址添加到 VM 操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分针对操作系统的步骤即可。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>将 IP 地址添加到 VM

完成以下步骤可将其他专用和公共 IP 地址添加到现有 NIC。 这些示例是基于本文中所述的[方案](#Scenario)制作的。

1. 打开 Azure CLI，然后在单个 CLI 会话中完成本部分的剩余步骤。 如果尚未安装并配置 Azure CLI，请完成[安装和配置 Azure CLI](../xplat-cli-install.md) 一文中的步骤，然后登录到你的 Azure 帐户。

2. 若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。 请不要尝试完成剩余的步骤：
    - 只有在收到一封告知允许你使用预览版的电子邮件之后，才这样做。
    - 如果不遵循所收到的电子邮件中的说明

3. 根据要求完成以下部分之一中的步骤：

    **添加专用 IP 地址**
    
    若要将专用 IP 地址添加到 NIC，必须使用以下命令创建 IP 配置。  如果想要添加动态专用 IP 地址，请在输入命令之前删除 ```-PrivateIpAddress 10.0.0.7```。 指定静态 IP 地址时，该地址必须是未使用的子网地址。

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    使用唯一的配置名称和专用 IP 地址创建任意数目的配置（适用于具有静态 IP 地址的配置）。

    **添加公共 IP 地址**
    
    可以通过将某个公共 IP 地址关联到新的 IP 配置或现有 IP 配置，来添加该地址。 根据需要完成以下部分之一中的步骤。

    > [!NOTE]
    > 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。 在一个订阅中可以使用的公共 IP 地址数有限制。 有关限制的详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#networking-limits)（Azure 限制）一文。
    >

    **将资源关联到新的 IP 配置**
    
    每当在新 IP 配置中添加公共 IP 地址时，还必须添加一个专用 IP 地址，因为所有 IP 配置都必须有专用 IP 地址。 可以添加现有的公共 IP 地址资源，或新建一个。 若要新建此类资源，请输入以下命令：
    
    ```azurecli
      azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

     若要新建具有动态专用 IP 地址和关联的 *myPublicIP3* 公共 IP 地址资源的 IP 配置，请输入以下命令：

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **将资源关联到现有 IP 配置**
   公共 IP 地址资源只能关联到尚未与任何公共 IP 地址资源关联的 IP 配置。 输入以下命令即可确定某个 IP 配置是否具有关联的公共 IP 地址：

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    在返回的输出中查看如下所示的行：
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    *IpConfig-3* 的 **Public IP** 列为空，这表示该 IP 配置当前没有任何关联的公共 IP 地址资源。 可将现有公共 IP 地址资源添加到 IpConfig-3，或输入以下命令来创建一个：

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    输入以下命令，将公共 IP 地址资源关联到名为 *IPConfig-3* 的现有 IP 配置：
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```

7. 输入以下命令，查看分配给 NIC 的专用 IP 地址和公共 IP 地址资源：

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    您应该会看到与下面类似的输出： 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. 根据本文[将 IP 地址添加到 VM 操作系统](#os-config)部分中的说明，将添加到 NIC 的专用 IP 地址添加到 VM 操作系统。 请勿向操作系统添加公共 IP 地址。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Feb17_HO2-->


