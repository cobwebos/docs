---
title: "Azure Active Directory 域服务：管理指南 | Microsoft Docs"
description: "使用 Azure Powershell 和经典部署模型将 Windows 虚拟机加入托管域。"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 1bb1546fb616131a1e1868a0d0610c4cad5d73e2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>使用 PowerShell 将 Windows Server 虚拟机加入托管域
> [!div class="op_single_selector"]
> * [Azure 经典门户 - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[Resource Manager 和经典](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍使用经典部署模型。 Azure AD 域服务当前不支持 Resource Manager 模型。
>
>

这些步骤演示了如何使用构建基块方法自定义一组 Azure PowerShell 命令以创建和预配置基于 Windows 的 Azure 虚拟机。 这些步骤有助于构建基于 Windows 的 Azure 虚拟机，并将其加入 Azure AD 域服务托管域。

这些步骤采用填空方法来创建 Azure PowerShell 命令集。 如果不熟悉 PowerShell 或想知道指定什么值可成功配置，则此方法很有用。 高级 PowerShell 用户可以使用命令并将变量（以“$”开头的行）替换为他们自己的值。

如果尚未这样做，请按[如何安装和配置 Azure PowerShell](/powershell/azure/overview) 中的说明在本地计算机上安装 Azure PowerShell。 然后，打开 Windows PowerShell 命令提示符。

## <a name="step-1-add-your-account"></a>步骤 1：添加帐户
1. 在 PowerShell 命令提示处，键入 **Add-AzureAccount** 并单击“Enter”。
2. 键入与 Azure 订阅相关联的电子邮件地址并单击“继续”。
3. 键入帐户的密码。
4. 单击“登录”。

## <a name="step-2-set-your-subscription-and-storage-account"></a>步骤 2：设置订阅和存储帐户
通过在 Windows PowerShell 命令提示符处运行以下命令，设置 Azure 订阅和存储帐户。 将引号内的所有内容（包括 < and > 字符）替换为相应的名称。

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

可以从 **Get-AzureSubscription** 命令输出的 SubscriptionName 属性获取相应的订阅名称。 运行 **Select-AzureSubscription** 命令后，可以从 **Get-AzureStorageAccount** 命令输出的 Label 属性获取相应的存储帐户名称。

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>步骤 3：分步演练 - 预配虚拟机，并将其加入托管域
下面是用于创建此虚拟机的相应 Azure PowerShell 命令集，为了便于阅读，每个程序块之间留有空行。

指定关于要预配的 Windows 虚拟机的信息。

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

有关 D、DS 或 G 系列虚拟机的 InstanceSize 值，请参阅 [Azure 的虚拟机和云服务大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。

提供关于托管域的信息。

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

指定云服务的名称。

    $svcname="Contoso100-test"

指定 VM 应加入的虚拟网络的名称。 确保 AAD-DS 托管域可用于此虚拟网络。

    $vnetname="MyPreviewVnet"

选择要用于预配 VM 的 VM 映像。

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

配置 VM - 设置 VM 名称、实例大小和要使用映像。

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

获取 VM 的本地管理员凭据。 选择安全性较高的本地管理员密码。

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

获取属于“AAD DC 管理员”组的用户帐户的凭据，以将 VM 加入托管域。 不指定域名 - 例如，在此示例中我们将“bob”指定为用户名。

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

配置 VM - 指定域加入要求和所需凭据。

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

为 VM 设置子网。

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

可选：指向域的 IP 地址。 如果将 Azure AD 域服务托管域的 IP 地址设置为虚拟网络的 DNS 服务器，则无需进行此步骤。

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

现在，预配已加入域的 Windows VM。

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>用于预配 Windows VM 并自动将其加入 AAD 域服务托管域的脚本
此 PowerShell 命令集为业务线服务器创建虚拟机：

* 使用 Windows Server 2012 R2 Datacenter 映像。
* 是特小型虚拟机。
* 名为 Contoso100-test。
* 会自动加入 contoso100 托管域。
* 添加到与托管域相同的虚拟网络。

以下是创建 Windows 虚拟机并自动将其加入 Azure AD 域服务托管域的完整示例脚本。

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
