---
title: 使用 Azure PowerShell 创建 Azure AD 域服务资源林 |Microsoft Docs
description: 本文介绍如何使用 Azure PowerShell 创建和配置 Azure Active Directory 域服务资源林和出站林到本地 Active Directory 域服务环境。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: iainfou
ms.openlocfilehash: eb627b8069bcd9efd1d56adab5eda45dc34a1a10
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921990"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>使用 Azure PowerShell 创建与本地域的 Azure Active Directory 域服务资源林和出站林信任

在无法同步密码哈希的环境中，或者用户只使用智能卡登录，因此他们不知道密码的情况下，你可以在 Azure Active Directory 域服务 (Azure AD DS) 中使用资源林。 资源林使用从 Azure AD DS 到一个或多个本地 AD DS 环境的单向出站信任。 这种信任关系可让用户、应用程序和计算机通过 Azure AD DS 托管域向本地域进行身份验证。 在资源林中，本地密码哈希永远不会同步。

![从 Azure AD DS 到本地 AD DS 的林信任关系图](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

在本文中，学习如何：

> [!div class="checklist"]
> * 使用 Azure PowerShell 创建 Azure AD DS 资源林
> * 使用 Azure PowerShell 在托管域中创建单向出站林信任
> * 在本地 AD DS 环境中配置 DNS 以支持托管域连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 测试并验证用于身份验证和资源访问的信任关系

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 托管域资源林目前不支持 Azure HDInsight 或 Azure 文件。 默认托管域用户林同时支持这两个附加服务。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。

* 安装和配置 Azure PowerShell。
    * 如果需要，请按照说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。
    * 确保使用 [Connect-AzAccount][Connect-AzAccount] cmdlet 登录到 Azure 订阅。
* 安装并配置 Azure AD PowerShell。
    * 如果需要，请按照说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 确保使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户。
* 需要在 Azure AD 目录中拥有“全局管理员”特权才能启用 Azure AD DS。
* 需要在 Azure 订阅中拥有“参与者”特权才能创建所需的 Azure AD DS 资源。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

本文介绍如何使用 Azure 门户从托管域创建和配置出站林信任。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="deployment-process"></a>部署过程

这是创建托管域资源林以及与本地 AD DS 的信任关系的多部分过程。 以下高级步骤构建你的可信混合环境：

1. 创建托管域服务主体。
1. 创建托管域资源林。
1. 使用站点到站点 VPN 或 Express Route 创建混合网络连接。
1. 创建信任关系的托管域方。
1. 创建信任关系的本地 AD DS 端。

在开始之前，请确保了解[网络注意事项、林命名和 DNS 要求](tutorial-create-forest-trust.md#networking-considerations)。 部署后，无法更改托管域林名称。

## <a name="create-the-azure-ad-service-principal"></a>创建 Azure AD 服务主体

Azure AD DS 要求服务主体从 Azure AD 同步数据。 必须先在 Azure AD 租户中创建此主体，然后才能创建托管域资源林。

创建用于 Azure AD DS 的 Azure AD 服务主体，以便进行通信和身份验证。 使用名称为“域控制器服务”的特定应用程序 ID 2565bd9d-da50-47d4-8b85-4c97f669dc36。 请不要更改此应用程序 ID。

使用 [Get-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet 创建 Azure AD 服务主体：

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="create-a-managed-domain-resource-forest"></a>创建托管域资源林

若要创建托管域资源林，请使用 `New-AzureAaddsForest` 脚本。 此脚本是支持创建和管理托管域资源林的一组更广泛的命令的一部分，包括在下一部分中创建单向绑定林。 这些脚本可从[PowerShell 库](https://www.powershellgallery.com/)提供，由 Azure AD 工程团队进行数字签名。

1. 首先，使用[AzResourceGroup][New-AzResourceGroup] cmdlet 创建资源组。 在以下示例中，资源组被命名为 myResourceGroup，并且是在 westus 区域中创建的。  使用自己的名称和所需区域：

    ```azure-powershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. `New-AaddsResourceForestTrust`使用[安装脚本][Install-Script]cmdlet 从[PowerShell 库][powershell-gallery]安装脚本：

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. 查看脚本所需的以下参数 `New-AzureAaddsForest` 。 请确保也具有必备**Azure PowerShell**和**Azure AD PowerShell**模块。 请确保已计划虚拟网络要求，以便提供应用程序和本地连接。

    | “属性”                         | 脚本参数          | 说明 |
    |:-----------------------------|---------------------------|:------------|
    | 订阅                 | *-azureSubscriptionId*    | 用于 Azure AD DS 计费的订阅 ID。 可以使用[get-azurermsubscription][Get-AzureRMSubscription] cmdlet 获取订阅列表。 |
    | 资源组               | *-aaddsResourceGroupName* | 托管域和关联资源的资源组的名称。 |
    | 位置                     | *-aaddsLocation*          | 承载托管域的 Azure 区域。 有关可用区域的详细 Azure AD，请参阅[支持的区域。](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Azure AD DS 管理员    | *-aaddsAdminUser*         | 第一个托管域管理员的用户主体名称。 此帐户必须是 Azure Active Directory 中的现有云用户帐户。 用户以及运行脚本的用户将添加到*AAD DC 管理员*组。 |
    | Azure AD DS 域名      | *-aaddsDomainName*        | 托管域的 FQDN，基于前面有关如何选择林名称的指南。 |

    `New-AzureAaddsForest`如果这些资源尚不存在，该脚本可以创建 Azure 虚拟网络并 AZURE AD DS 子网。 指定该脚本时，可以选择创建工作负荷子网：

    | “属性”                              | 脚本参数                  | 描述 |
    |:----------------------------------|:----------------------------------|:------------|
    | 虚拟网络名称              | *-aaddsVnetName*                  | 托管域的虚拟网络的名称。|
    | 地址空间                     | *-aaddsVnetCIDRAddressSpace*      | 如果创建虚拟网络) ，则 (CIDR 表示法中的虚拟网络地址范围。|
    | Azure AD DS 子网名称           | *-aaddsSubnetName*                | 托管托管域的*aaddsVnetName*虚拟网络的子网名称。 不要将自己的 Vm 和工作负载部署到此子网中。 |
    | Azure AD DS 地址范围         | *-aaddsSubnetCIDRAddressRange*    | AAD DS 实例的子网地址范围，以 CIDR 表示法表示，例如*192.168.1.0/24*。 地址范围必须包含在虚拟网络的地址范围内，并且不同于其他子网。 |
    | 工作负荷子网名称 (可选)    | *-workloadSubnetName*             | *AaddsVnetName*虚拟网络中子网的可选名称，用于创建你自己的应用程序工作负荷。 Vm 和应用程序，并改为连接到对等互连 Azure 虚拟网络。 |
    | 工作负荷地址范围 (可选)  | *-workloadSubnetCIDRAddressRange* | 适用于应用程序工作负荷的可选子网地址范围，如*192.168.2.0/24*。 地址范围必须包含在虚拟网络的地址范围内，并且不同于其他子网。|

1. 现在，使用脚本创建托管域资源林 `New-AzureAaaddsForest` 。 以下示例创建名为*addscontoso.com*的林，并创建工作负荷子网。 提供自己的参数名称和 IP 地址范围或现有虚拟网络。

    ```azure-powershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    创建托管域资源林和支持资源需要花费很长时间。 允许脚本完成。 转到下一节，配置本地网络连接，同时 Azure AD 资源林在后台设置。

## <a name="configure-and-validate-network-settings"></a>配置和验证网络设置

随着托管域继续部署，配置和验证与本地数据中心的混合网络连接。 还需要一个用于管理域的管理 VM，以便定期维护。 你的环境中可能已存在一些混合连接，或者你可能需要与团队中的其他连接来配置连接。

在开始之前，请确保了解[网络注意事项和建议](tutorial-create-forest-trust.md#networking-considerations)。

1. 使用 Azure VPN 或 Azure ExpressRoute 连接创建到本地网络到 Azure 的混合连接。 混合网络配置超出了本文档的范围，你的环境中可能已存在此配置。 有关特定方案的详细信息，请参阅以下文章：

    * [Azure 站点到站点 VPN](/azure/vpn-gateway/vpn-gateway-about-vpngateways)。
    * [Azure ExpressRoute 概述](/azure/expressroute/expressroute-introduction)。

    > [!IMPORTANT]
    > 如果直接创建连接到托管域的虚拟网络，请使用单独的网关子网。 不要在托管域的子网中创建网关。

1. 若要管理托管域，请创建管理 VM，将其加入托管域，并安装所需的 AD DS 管理工具。

    在部署托管域资源林时，[创建 Windows SERVER VM](https://docs.microsoft.com/azure/active-directory-domain-services/join-windows-vm) ，然后[安装核心 AD DS 管理工具](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-management-vm)来安装所需的管理工具。 在成功部署域后，请等待将管理 VM 加入托管域，直到执行以下步骤之一。

1. 验证本地网络和 Azure 虚拟网络之间的网络连接。

    * 例如，确认你的本地域控制器可以使用或远程桌面连接到托管 VM `ping` 。
    * 验证你的管理 VM 是否可以使用等实用工具再次连接到你的本地域控制器 `ping` 。

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。 选择托管域（如*aaddscontoso.com* ），并等待状态报告为 "**正在运行**"。

    运行时，请[更新 Azure 虚拟网络的 DNS 设置](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)，然后[为 Azure AD DS 启用用户帐户](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便为托管域资源林完成配置。

1. 记下 "概述" 页上显示的 DNS 地址。 在以下部分配置信任关系的本地 Active Directory 端时，需要使用这些地址。
1. 重新启动管理 VM 以接收新的 DNS 设置，然后将[VM 加入托管域](join-windows-vm.md#join-the-vm-to-the-managed-domain)。
1. 管理 VM 加入托管域后，请使用远程桌面再次连接。

    在命令提示符下，使用 `nslookup` 和托管域资源林名称来验证资源林的名称解析。

    ```console
    nslookup aaddscontoso.com
    ```

    该命令应返回资源林的两个 IP 地址。

## <a name="create-the-forest-trust"></a>创建林信任

林信任分为两部分：托管域资源林中的单向出站林信任，以及本地 AD DS 林中的单向入站林信任。 手动创建此信任关系的双方。 创建双方后，用户和资源可以使用林信任成功进行身份验证。 托管域资源林支持到本地林的最高 5 1 向出站林信任。

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>创建信任关系的托管域方

使用 `Add-AaddsResourceForestTrust` 脚本创建信任关系的托管域方。 首先， `Add-AaddsResourceForestTrust` 使用[安装脚本][Install-Script]cmdlet 从[PowerShell 库][powershell-gallery]安装脚本：

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

现在，请提供以下信息：

| “属性”                               | 脚本参数     | 描述 |
|:-----------------------------------|:---------------------|:------------|
| Azure AD DS 域名            | *-ManagedDomainFqdn* | 托管域的 FQDN，如*aaddscontoso.com* |
| 本地 AD DS 域名      | *-TrustFqdn*         | 受信任林的 FQDN，如*onprem.contoso.com* |
| 信任友好名称                | *-TrustFriendlyName* | 信任关系的友好名称。 |
| 本地 AD DS DNS IP 地址 | *-TrustDnsIPs*       | 列出的受信任域的 DNS 服务器 IPv4 地址的逗号分隔列表。 |
| 信任密码                     | *-TrustPassword*     | 信任关系的复杂密码。 在本地 AD DS 中创建单向入站信任时也会输入此密码。 |
| 凭据                        | *-凭据*       | 用于向 Azure 进行身份验证的凭据。 用户必须位于*AAD DC 管理员组*中。 如果未提供，则脚本会提示进行身份验证。 |

以下示例创建名为*myAzureADDSTrust*的信任关系到*onprem.contoso.com*。 使用自己的参数名称和密码：。

```azure-powershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> 请记住你的信任密码。 创建信任的本地端时，必须使用相同的密码。

## <a name="configure-dns-in-the-on-premises-domain"></a>在本地域中配置 DNS

若要从本地环境正确解析托管域，可能需要向现有 DNS 服务器添加转发器。 如果尚未将本地环境配置为与托管域进行通信，请在管理工作站中针对本地 AD DS 域完成以下步骤：

1. 选择“开始”|“管理工具”|“DNS”
1. 右键单击 DNS 服务器（例如 myAD01）并选择“属性”
1. 选择“转发器”，然后选择“编辑”以添加更多转发器。 
1. 添加托管域的 IP 地址，例如 " *10.0.1.4* " 和 " *10.0.1.5*"。
1. 在本地命令提示符下，使用托管域资源林域名的**nslookup**验证名称解析。 例如， `Nslookup aaddscontoso.com` 应返回托管域资源林的两个 IP 地址。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>在本地域中创建入站林信任

本地 AD DS 域需要托管域的传入林信任。 必须手动在本地 AD DS 域中创建此信任，而不能在 Azure 门户中创建。

若要在本地 AD DS 域上配置入站信任，请在管理工作站中针对本地 AD DS 域完成以下步骤：

1. 选择“开始”|“管理工具”|“Active Directory 域和信任”
1. 右键单击域（例如 onprem.contoso.com）并选择“属性”
1. 依次选择“信任”选项卡、“新建信任” 
1. 输入托管域的名称（例如*aaddscontoso.com*），然后选择 "**下一步**"
1. 选择创建“林信任”的选项，然后选择创建“单向: 传入”信任的选项。 
1. 选择创建“仅限此域”的信任。 在下一步骤中，你将在 Azure 门户中为托管域创建信任。
1. 选择使用“全林性身份验证”，然后输入并确认信任密码。 在下一部分中，也要在 Azure 门户中输入同一密码。
1. 在接下来的几个窗口中使用默认选项完成每个步骤，然后选择选项“否，不要确认传出信任”。 无法验证信任关系，因为委派的管理员帐户不具有所需的权限。 此行为是设计使然。
1. 选择“完成”

## <a name="validate-resource-authentication"></a>验证资源身份验证

可以使用以下常用方案来验证林信任是否正确对用户进行身份验证以及是否正确访问资源：

* [从 Azure AD DS 资源林进行本地用户身份验证](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [使用本地用户访问 Azure AD DS 资源林中的资源](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [启用文件和打印机共享](#enable-file-and-printer-sharing)
    * [创建安全组并添加成员](#create-a-security-group-and-add-members)
    * [创建文件共享用于跨林访问](#create-a-file-share-for-cross-forest-access)
    * [验证向资源进行的跨林身份验证](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>从 Azure AD DS 资源林进行本地用户身份验证

应已将 Windows Server 虚拟机加入托管域资源域。 使用此虚拟机来测试本地用户是否可在虚拟机上进行身份验证。

1. 使用远程桌面和托管域管理员凭据连接到已加入托管域资源林的 Windows Server VM。 如果网络级别身份验证 (NLA) 错误，请检查你使用的用户帐户不是域用户帐户。

    > [!TIP]
    > 若要安全地连接到已加入到 Azure AD 域服务的 Vm，可以在支持的 Azure 区域使用[Azure 堡垒主机服务](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打开命令提示符，使用 `whoami` 命令显示当前已通过身份验证的用户的可分辨名称：

    ```console
    whoami /fqdn
    ```

1. 以本地域中的用户身份使用 `runas` 命令进行身份验证。 在以下命令中，请将 `userUpn@trusteddomain.com` 替换为受信任本地域中某个用户的 UPN。 该命令会提示你输入用户密码：

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 如果身份验证成功，则会打开一个新的命令提示符。 新命令提示符的标题包含 `running as userUpn@trusteddomain.com`。
1. 在新命令提示符下使用 `whoami /fqdn` 查看本地 Active Directory 中已通过身份验证的用户的可分辨名称。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>使用本地用户访问 Azure AD DS 资源林中的资源

使用已加入托管域资源林的 Windows Server VM，可以测试当用户通过本地域中的用户进行身份验证时，用户可以访问资源林中托管的资源的情况。 以下示例展示了如何创建并测试各种常用方案。

#### <a name="enable-file-and-printer-sharing"></a>启用文件和打印机共享

1. 使用远程桌面和托管域管理员凭据连接到已加入托管域资源林的 Windows Server VM。 如果网络级别身份验证 (NLA) 错误，请检查你使用的用户帐户不是域用户帐户。

    > [!TIP]
    > 若要安全地连接到已加入到 Azure AD 域服务的 Vm，可以在支持的 Azure 区域使用[Azure 堡垒主机服务](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打开“Windows 设置”，然后搜索并选择“网络和共享中心”。 
1. 选择“更改高级共享设置”的选项。
1. 在“域配置文件”下，依次选择“启用文件和打印机共享”、“保存更改”。  
1. 选择“网络和共享中心”。

#### <a name="create-a-security-group-and-add-members"></a>创建安全组并添加成员

1. 打开“Active Directory 用户和计算机”。
1. 右键单击域名，然后依次选择“新建”、“组织单位”。 
1. 在名称框中键入“LocalObjects”，然后选择“确定”。
1. 在导航窗格中选择并右键单击“LocalObjects”。 依次选择“新建”、“组”。 
1. 在“组名称”框中键入“FileServerAccess”。 对于“组作用域”，选择“域本地”，然后选择“确定”。  
1. 在内容窗格中，双击“FileServerAccess”。 依次选择“成员”、“添加”、“位置”。  
1. 从“位置”视图中选择你的本地 Active Directory，然后选择“确定”。 
1. 在“输入要选择的对象名称”框中键入“域用户”。 选择“检查名称”，提供本地 Active Directory 的凭据，然后选择“确定”。 

    > [!NOTE]
    > 必须提供凭据，因为信任关系只是单向的。 这意味着，托管域中的用户无法访问资源或搜索受信任 (本地) 域中的用户或组。

1. 本地 Active Directory 中的“域用户”组应是“FileServerAccess”组的成员。  选择“确定”以保存该组并关闭窗口。

#### <a name="create-a-file-share-for-cross-forest-access"></a>创建文件共享用于跨林访问

1. 在加入托管域资源林中的 Windows Server VM 上，创建一个文件夹并提供名称，例如*CrossForestShare*。
1. 右键单击该文件夹并选择“属性”。
1. 依次选择“安全性”选项卡、“编辑”。 
1. 在“CrossForestShare 的权限”对话框中，选择“添加”。
1. 在“输入要选择的对象名称”中键入“FileServerAccess”，然后选择“确定”。 
1. 从“组或用户名称”列表中选择“FileServerAccess”。 在“FileServerAccess 的权限”列表中，对“修改”和“写入”权限选择“允许”，然后选择“确定”。  
1. 依次选择“共享”选项卡、“高级共享...”。 
1. 选择“共享此文件夹”，然后在“共享名称”中为该文件共享输入一个易记的名称，例如“CrossForestShare”。 
1. 选择“权限”。 在“每个人的权限”列表中，对“更改”权限选择“允许”。  
1. 选择“确定”两次，然后选择“关闭”。 

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>验证向资源进行的跨林身份验证

1. 使用本地 Active Directory 中的用户帐户登录到已加入本地 Active Directory 的 Windows 计算机。
1. 使用完全限定的主机名和共享名称（例如 `\\fs1.aaddscontoso.com\CrossforestShare`）通过“Windows 资源管理器”连接到你创建的共享。
1. 若要验证写入权限，请在文件夹中单击右键，然后依次选择“新建”、“文本文档”。  使用默认名称“新建文本文档”。

    如果正确设置了写入权限，则会创建一个新的文本文档。 然后，可以使用以下步骤相应地打开、编辑和删除文件。
1. 若要验证读取权限，请打开“新建文本文档”。
1. 若要验证修改权限，请在文件中添加文本，然后关闭“记事本”。 当系统提示是否保存更改时，请选择“保存”。
1. 若要验证删除权限，请右键单击“新建文本文档”并选择“删除”。  选择“是”以确认删除文件。

## <a name="update-or-remove-outbound-forest-trust"></a>更新或删除出站林信任

如果需要从托管域更新现有的单向出站林，可以使用 `Get-AaddsResourceForestTrusts` 和 `Set-AaddsResourceForestTrust` 脚本。 这些脚本可帮助你想要更新林信任友好名称或信任密码。 若要从托管域中删除单向出站信任，可以使用 `Remove-AaddsResourceForestTrust` 脚本。 你必须手动删除关联的本地 AD DS 林中的单向入站林信任。

### <a name="update-a-forest-trust"></a>更新林信任

在正常操作中，托管域林和本地林在其各自之间协商定期密码更新过程。 这是正常 AD DS 信任关系安全过程的一部分。 如果信任关系遇到问题并且你想要手动重置为已知密码，则无需手动轮换信任密码。 有关详细信息，请参阅[受信任的域对象密码更改](concepts-forest-trust.md#tdo-password-changes)。

以下示例步骤说明了在需要手动重置出站信任密码时，如何更新现有的信任关系：

1. `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` 使用[安装脚本][Install-Script]cmdlet 从[PowerShell 库][powershell-gallery]安装和脚本：

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. 在可以更新现有信任之前，请先使用脚本获取信任资源 `Get-AaddsResourceForestTrusts` 。 在下面的示例中，将现有信任分配给名为*existingTrust*的对象。 指定你自己的托管域林名称和要更新的本地林名称：

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. 若要更新现有的信任密码，请使用 `Set-AaddsResourceForestTrust` 脚本。 指定上一步中的现有信任对象，然后指定新的信任关系密码。 PowerShell 不会强制执行密码复杂性，因此请确保为环境生成并使用安全密码。

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>删除林信任

如果不再需要从托管域到本地 AD DS 林的单向出站林信任，则可以将其删除。 请确保在删除信任之前，应用程序或服务不需要针对本地 AD DS 林进行身份验证。 还必须手动删除本地 AD DS 林中的单向入站信任。

1. `Remove-AaddsResourceForestTrust`使用[安装脚本][Install-Script]cmdlet 从[PowerShell 库][powershell-gallery]安装脚本：

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. 现在使用脚本删除林信任 `Remove-AaddsResourceForestTrust` 。 在下面的示例中，删除了名为*aaddscontoso.com*的托管域林和本地林*onprem.contoso.com*之间名为*myAzureADDSTrust*的信任。 指定你自己的托管域林名称和要删除的本地林名称：

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

若要从本地 AD DS 林删除单向入站信任，请连接到有权访问本地 AD DS 林的管理计算机，并完成以下步骤：

1. 选择“开始”|“管理工具”|“Active Directory 域和信任”
1. 右键单击域（例如 onprem.contoso.com）并选择“属性”
1. 选择 "**信任**" 选项卡，然后从托管域林中选择现有的传入信任。
1. 选择 "**删除**"，然后确认是否要删除传入信任。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure PowerShell 创建托管域资源林
> * 使用 Azure PowerShell 在托管域中创建单向出站林信任
> * 在本地 AD DS 环境中配置 DNS 以支持托管域连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 测试并验证用于身份验证和资源访问的信任关系

有关 Azure AD DS 中的林类型的更多概念信息，请参阅[什么是资源林？][concepts-forest]和[林信任在 Azure AD DS 中的工作原理是什么？][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/