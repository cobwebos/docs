---
title: 红帽企业 Linux 自带订阅 Azure 映像 |微软文档
description: 了解在 Azure 上为红帽企业 Linux 带来您自己的订阅映像。
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264591"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>红帽企业 Linux 在 Azure 中自带订阅的黄金映像

红帽企业 Linux （RHEL） 映像通过即用即付或自带订阅 （BYOS） （红帽金像）模型在 Azure 中可用。 本文概述了 Azure 中的红帽金色图像。

>[!NOTE]
> RHEL BYOS 金像映像在 Azure 公共（商业）和 Azure 政府云中可用。 它们在 Azure 中国或 Azure 黑林云中不可用。

## <a name="important-points-to-consider"></a>需要考虑的要点

- 此程序中提供的红帽金像是生产就绪的 RHEL 映像，类似于 Azure 应用商店中的 RHEL 即用即付映像。
- 这些映像遵循[Azure 上的红帽企业 Linux 映像中描述的](./redhat-images.md)当前策略。
- 标准支持策略适用于从这些映像创建的 VM。
- 红帽金像提供的 VM 不携带与 RHEL 即用即付图像相关的 RHEL 费用。
- 图像是无权的。 您必须使用红帽订阅管理器注册和订阅 VM，以便直接从红帽获取更新。
- 目前无法在 BYOS 和 Linux 映像的即用即付计费模型之间动态切换。 要切换计费模型，必须从相应的映像重新部署 VM。

>[!NOTE]
> 第 2 代 RHEL BYOS 映像目前无法通过市场产品/市场提供。 如果需要第 2 代 RHEL BYOS 映像，请访问红帽订阅管理中的云访问仪表板。 有关详细信息，请参阅[红帽文档](https://access.redhat.com/articles/4847681)。

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>访问红帽金像的要求和条件

1. 熟悉[红帽云访问计划](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)条款。 在[红帽订阅管理器](https://access.redhat.com/management/cloud)启用云访问红帽订阅。 您需要拥有要注册为云访问的 Azure 订阅。

1. 如果为云访问启用的红帽订阅满足资格要求，则将自动启用 Azure 订阅以进行金像访问。

### <a name="expected-time-for-image-access"></a>映像访问的预期时间

完成云访问启用步骤后，红帽将验证您是否有资格使用红帽金像。 如果验证成功，您将在三小时内获得对黄金图像的访问权限。

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>使用 Azure 门户中的红帽金色图像

1. Azure 订阅收到对红帽金像的访问权限后，可以在[Azure 门户](https://portal.azure.com)中找到它们。 转到**创建资源** > **，查看所有**。

1. 在页面顶部，您将看到您有私人优惠。

    ![市场私人优惠](./media/rhel-byos-privateoffers.png)

1. 选择紫色链接，或向下滚动到页面底部以查看您的私人优惠。

1. UI 中的其他预配与任何其他现有的红帽映像没有什么不同。 选择 RHEL 版本，然后按照提示预配 VM。 此过程还允许您在最后一步接受图像的条款。

>[!NOTE]
>到目前为止，这些步骤不会启用您的红帽金像用于编程部署。 如"附加信息"部分所述，需要执行其他步骤。

本文档的其余部分重点介绍用于预配和接受图像上的术语的 CLI 方法。 就最终结果（预配的 RHEL 金像 VM）而言，UI 和 CLI 是完全可互换的。

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>使用 Azure CLI 中的红帽金色图像

以下说明使用 Azure CLI 引导您完成 RHEL VM 的初始部署过程。 这些说明假定已安装 Azure [CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli)

>[!IMPORTANT]
>请确保在发布者、优惠、计划和图像引用中使用以下所有命令中的所有小写字母。

1. 检查您是否在所需的订阅中。

    ```azurecli
    az account show -o=json
    ```

1. 为红帽金像 VM 创建资源组。

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 接受图像术语。

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >*每个 Azure 订阅（每个映像 SKU*）需要接受一次这些条款。

1. （可选）使用以下命令验证 VM 部署：

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. 通过运行与上一个示例中显示的相同命令（没有参数）`--validate`来预配 VM。

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH 进入您的 VM，并验证您拥有无权限映像。 要执行此步骤，运行`sudo yum repolist`。 对于 RHEL 8，请使用`sudo dnf repolist`。 输出要求您使用订阅管理器将 VM 注册到红帽。

>[!NOTE]
>在 RHEL `dnf` 8`yum`上，可互换。 有关详细信息，请参阅[RHEL 8 管理指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings)。

## <a name="use-the-red-hat-gold-images-from-powershell"></a>使用强力壳牌的红帽金像

以下脚本是一个示例。 将资源组、位置、VM 名称、登录信息和其他变量替换为您选择的配置。 发布者和计划信息必须小写。

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>加密红帽企业 Linux 带来您自己的订阅黄金映像

红帽企业Linux BYOS黄金映像可以通过[使用 Azure 磁盘加密](../../linux/disk-encryption-overview.md)进行保护。 *必须先*注册订阅，然后才能启用加密。 有关如何注册 RHEL BYOS 金像的详细信息，请参阅[如何使用红帽订阅管理器注册和订阅系统到红帽客户门户](https://access.redhat.com/solutions/253273)。 如果您有有效的红帽订阅，还可以阅读[创建红帽客户门户激活密钥](https://access.redhat.com/articles/1378093)。

[红帽自定义映像](../../linux/redhat-create-upload-vhd.md)不支持 Azure 磁盘加密。 其他 Azure 磁盘加密要求和先决条件记录[在 Linux VM 的 Azure 磁盘加密](../../linux/disk-encryption-overview.md#additional-vm-requirements)中。

有关应用 Azure 磁盘加密的步骤，请参阅 Linux VM 和相关文章[上的 Azure 磁盘加密方案](../../linux/disk-encryption-linux.md)。

## <a name="additional-information"></a>其他信息

- 如果尝试在未为此产品/服务启用的订阅上预配 VM，则会收到以下消息：

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    在这种情况下，请与 Microsoft 或红帽联系以启用您的订阅。

- 如果从 RHEL BYOS 映像修改快照并尝试将该自定义映像发布到[共享映像库](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)，则必须提供与快照原始源匹配的计划信息。 例如，该命令可能如下所示：

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    请注意最终行中的计划参数。

    自定义映像不支持[Azure 磁盘加密](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)。

- 如果使用自动化从 RHEL BYOS 映像预配 VM，则必须提供与示例命令中显示的计划参数类似。 例如，如果使用 Terraform，则在[计划块](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)中提供计划信息。

## <a name="next-steps"></a>后续步骤

- 有关云访问的分步指南和程序详细信息，请参阅[红帽云访问文档](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)。
- 要了解有关红帽更新基础结构的详细信息，请参阅[Azure 红帽更新基础结构](./redhat-rhui.md)。
- 要了解有关 Azure 中的所有红帽映像，请参阅[文档页](./redhat-images.md)。
- 有关所有版本的 RHEL 的红帽支持策略的信息，请参阅[红帽企业 Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)页面。
- 有关 RHEL 黄金图像的其他文档，请参阅[红帽文档](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)。
