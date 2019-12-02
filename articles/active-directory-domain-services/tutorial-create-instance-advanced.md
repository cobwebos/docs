---
title: 教程 - 创建 Azure Active Directory 域服务实例 | Microsoft Docs
description: 本教程介绍如何使用 Azure 门户创建和配置 Azure Active Directory 域服务实例并指定高级配置选项。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 334a5c3c76f1ebaf4c8c36020110ef9c0bcc8d69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208675"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance-with-advanced-configuration-options"></a>教程：使用高级配置选项创建和配置 Azure Active Directory 域服务实例

Azure Active Directory 域服务 (Azure AD DS) 提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 使用这些域服务就无需自行部署、管理和修补域控制器。 Azure AD DS 与现有的 Azure AD 租户集成。 这种集成可让用户使用其企业凭据登录，而你可以使用现有的组和用户帐户来保护对资源的访问。

可以[使用默认配置选项创建托管域][tutorial-create-instance]以实现联网和同步，也可以手动定义这些设置。 本教程介绍如何定义这些高级配置选项，以便使用 Azure 门户来创建和配置 Azure AD DS 实例。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为托管域配置 DNS 和虚拟网络设置
> * 创建 Azure AD DS 实例
> * 将管理用户添加到域管理
> * 启用密码哈希同步

如果你没有 Azure 订阅，可以在开始之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 需要在 Azure AD 目录中拥有“全局管理员”特权才能启用 Azure AD DS。 
* 需要在 Azure 订阅中拥有“参与者”特权才能创建所需的 Azure AD DS 资源。 

尽管 Azure AD DS 不需要，但建议为 Azure AD 租户[配置自助式密码重置 (SSPR)][configure-sspr]。 用户可以在没有 SSPR 的情况下更改其密码，但如果用户忘记其密码并需要重置密码，SSPR 会有所帮助。

> [!IMPORTANT]
> 创建 Azure AD DS 托管域后，无法将实例移到其他资源组、虚拟网络、订阅等。部署 Azure AD DS 实例时，请注意选择最合适的订阅、资源组、区域和虚拟网络。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，你将使用 Azure 门户来创建和配置 Azure AD DS 实例。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-instance-and-configure-basic-settings"></a>创建实例并配置基本设置

若要启动“启用 Azure AD 域服务”  向导，请完成以下步骤：

1. 在 Azure 门户菜单或“主页”页上，选择“创建资源”   。
1. 在搜索栏中输入“域服务”，然后从搜索建议中选择“Azure AD 域服务”。  
1. 在“Azure AD 域服务”页上选择“创建”。  “启用 Azure AD 域服务”  向导随即启动。
1. 选择要在其中创建托管域的 Azure“订阅”  。
1. 选择托管域应属于的“资源组”  。 选择“新建”，或选择现有的资源组。 

创建 Azure AD DS 实例时，请指定 DNS 名称。 选择此 DNS 名称时请注意以下事项：

* **内置域名：** 默认将使用目录的内置域名（带 *.onmicrosoft.com* 后缀）。 若要启用通过 Internet 对托管域进行安全 LDAP 访问，则不能创建数字证书来保护与此默认域建立的连接。 Microsoft 拥有 *.onmicrosoft.com* 域，因此，证书颁发机构 (CA) 不会颁发证书。
* **自定义域名：** 最常见的方法是指定自定义域名，通常是你已拥有且可路由的域名。 使用可路由的自定义域时，流量可根据需要正确传送，以支持你的应用程序。
* **不可路由的域后缀：** 一般情况下，我们建议避免使用不可路由的域名后缀，例如 *contoso.local*。 *.local* 后缀不可路由，并可能导致 DNS 解析出现问题。

> [!TIP]
> 如果创建自定义域名，请注意现有的 DNS 命名空间。 建议为域名添加唯一的前缀。 例如，如果 DNS 根名称为 contoso.com  ，则使用自定义域名 corp.contoso.com  或 ds.contoso.com  创建 Azure AD DS 托管域。 在具有本地 AD DS 环境的混合环境中，这些前缀可能已在使用中。 对 Azure AD DS 使用唯一的前缀。
>
> 可以将根 DNS 名称用于 Azure AD DS 托管域，但是可能需要为环境中的其他服务创建一些其他 DNS 记录。 例如，如果运行使用根 DNS 名称托管站点的 Web 服务器，则可能存在命名冲突，从而需要其他 DNS 条目。
>
> 在这些教程和操作指南文章中，使用自定义域 contoso.com  作为简短示例。 在所有命令中，指定你自己的域名，其中可以包含唯一的前缀。
>
> 有关详细信息，请参阅[为域选择命名前缀][naming-prefix]。

还存在以下 DNS 名称限制：

* **域前缀限制：** 不能创建前缀长度超过 15 个字符的托管域。 指定域名的前缀（例如 *contoso.com* 域名中的 *contoso*）所包含的字符不得超过 15 个。
* **网络名称冲突：** 托管域的 DNS 域名不能已存在于虚拟网络中。 具体而言，请检查可能导致名称冲突的以下情况：
    * Azure 虚拟网络中是否已存在具有相同 DNS 域名的 Active Directory 域。
    * 计划在其中启用托管域的虚拟网络是否与本地网络建立了 VPN 连接。 在此方案中，确保在本地网络上没有具有相同 DNS 域名的域。
    * 虚拟网络中是否存在具有该名称的 Azure 云服务。

填写 Azure 门户的“基本信息”窗口中的字段，以创建 Azure AD DS 实例： 

1. 输入托管域的 **DNS 域名**，并注意前面所述的问题。
1. 选择应在其中创建托管域的 Azure“位置”  。 如果选择支持可用性区域的区域，则 Azure AD DS 资源会跨区域分布以实现额外的冗余。

    可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。

    对于要跨区域分布 Azure AD DS，无需进行任何配置。 Azure 平台会自动处理资源的区域分配。 若要查看区域可用性的详细信息，请参阅[Azure 中的可用性区域是什么？][availability-zones]

1. *林*是 Active Directory 域服务用来对一个或多个域进行分组的逻辑构造。 默认情况下，Azure AD DS 托管域作为用户林创建  。 此类林可同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的所有用户帐户。 *资源*林仅同步直接在 Azure AD 中创建的用户和组。 资源林目前处于预览状态。 有关*资源*林的详细信息，包括为何使用资源林以及如何在本地 AD DS 域中创建林信任，请参阅 [Azure AD DS 资源林概述][resource-forests]。

    对于本教程，请选择创建用户林  。

    ![为 Azure AD 域服务实例配置基本设置](./media/tutorial-create-instance-advanced/basics-window.png)

1. 若要手动配置其他选项，请选择“下一步 - 网络”。  否则，请选择“查看 + 创建”以接受默认的配置选项，然后跳到用于[部署托管域](#deploy-the-managed-domain)的部分。  选择此创建选项时，会配置以下默认设置：

* 创建名为 *aadds-vnet* 的虚拟网络，该网络使用的 IP 地址范围为 *10.0.1.0/24*。
* 创建名为 *aadds-vnet* 的子网，该子网使用的 IP 地址范围为 *10.0.1.0/24*。
* 将所有用户从 Azure AD 同步到 Azure AD DS 托管域。 

## <a name="create-and-configure-the-virtual-network"></a>创建并配置虚拟网络

若要提供连接，需要创建 Azure 虚拟网络和专用子网。 Azure AD DS 将在此虚拟网络子网中启用。 在本教程中，你将创建一个虚拟网络，不过，也可以选择使用现有的虚拟网络。 无论使用哪种方法，都必须创建一个专用子网供 Azure AD DS 使用。

此专用虚拟网络子网的一些注意事项包括：

* 该子网的地址范围中必须至少包含 3-5 个可用 IP 地址才能支持 Azure AD DS 资源。
* 请不要选择“网关”子网来部署 Azure AD DS。  不支持将 Azure AD DS 部署到“网关”子网。 
* 不要将任何其他虚拟机部署到该子网。 应用程序和 VM 通常使用网络安全组来保护连接。 在单独的子网中运行这些工作负荷可以在不中断与托管域的连接的情况下应用这些网络安全组。
* 启用 Azure AD DS 后，无法将托管域迁移到其他虚拟网络。

有关如何规划和配置虚拟网络的详细信息，请参阅 [Azure Active Directory 域服务的网络注意事项][network-considerations]。

按如下所示填写“网络”窗口中的字段： 

1. 在“网络”页上，  从下拉菜单选择要将 Azure AD DS 部署到其中的虚拟网络，或者选择“新建”。 
    1. 如果选择创建虚拟网络，请输入虚拟网络的名称（例如 *myVnet*），然后提供地址范围（例如 *10.0.1.0/24*）。
    1. 创建具有明确名称的专用子网，例如 *DomainServices*。 提供地址范围，例如 *10.0.1.0/24*。

    ![创建用于 Azure AD 域服务的虚拟网络和子网](./media/tutorial-create-instance-advanced/create-vnet.png)

    请确保选择专用 IP 地址范围内的某个地址范围。 选择公共地址空间中你不拥有的 IP 地址范围会导致 Azure AD DS 出错。

1. 选择一个虚拟网络子网，例如 *DomainServices*。
1. 准备就绪后，选择“下一步 - 管理”  。

## <a name="configure-an-administrative-group"></a>配置管理组

名为“AAD DC 管理员”的特殊管理组用于管理 Azure AD DS 域。  此组的成员在已加入托管域的 VM 上拥有管理权限。 在加入域的 VM 上，此组将添加到本地管理员组。 此组的成员还可以使用远程桌面远程连接到已加入域的 VM。

你在使用 Azure AD DS 的托管域上不拥有“域管理员”或“企业管理员”权限。   这些权限由服务保留，不会提供给租户中的用户使用。 “AAD DC 管理员”组允许执行某些特权操作。  这些操作包括将计算机加入域、将用户添加到已加入域的 VM 上的管理组，以及配置组策略。

向导会自动在 Azure AD 目录中创建“AAD DC 管理员”组。  如果在 Azure AD 目录中拥有具有此名称的现有组，则向导会选择此组。 在部署过程中，可以视需要将更多用户添加到此“AAD DC 管理员”组。  稍后可以完成这些步骤。

1. 若要将更多用户添加到“AAD DC 管理员”组，请选择“管理组成员身份”。  

    ![配置“AAD DC 管理员”组的组成员身份](./media/tutorial-create-instance-advanced/admin-group.png)

1. 选择“添加成员”按钮，然后搜索并选择 Azure AD 目录中的用户。  例如，搜索自己的帐户，并将其添加到“AAD DC 管理员”组。 
1. 如果在 Azure AD DS 托管域中存在需要注意的警报，则根据需要更改通知的接收者或添加更多的接收者。
1. 准备就绪后，选择“下一步 - 同步”  。

## <a name="configure-synchronization"></a>配置同步

Azure AD DS 允许同步 Azure AD 中的所有用户和组，或者仅按范围同步特定的组。   如果选择同步所有用户和组，则以后无法选择仅执行按范围同步。  有关按范围同步的详细信息，请参阅 [Azure AD 域服务的按范围同步][scoped-sync]。

1. 本教程选择了同步**所有**用户和组。 这是默认的同步选项。

    ![对 Azure AD 中的用户和组执行完全同步](./media/tutorial-create-instance-advanced/sync-all.png)

1. 选择“查看 + 创建”  。

## <a name="deploy-the-managed-domain"></a>部署托管域

在向导的“摘要”  页上，检查托管域的配置设置。 可以后退到向导中的任何步骤进行更改。 若要通过这些配置选项采用一致的方式将 Azure AD DS 托管域重新部署到另一 Azure AD 租户，也可**下载用于自动化操作的模板**。

1. 若要创建托管域，请选择“创建”。  系统会显示一个通知，指出在创建 Azure AD DS 托管域后，某些配置选项（例如 DNS 名称或虚拟网络）不能更改。 若要继续操作，请选择“确定”。 
1. 预配托管域的过程可能最多需要一小时。 门户中会显示一条通知，其中显示了 Azure AD DS 部署的进度。 选择该通知可查看部署的详细进度。

    ![Azure 门户中显示的“正在部署”通知](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. 选择资源组（例如 *myResourceGroup*），然后从 Azure 资源列表中选择 Azure AD DS 实例，例如 *contoso.com*。 “概述”选项卡显示了当前“正在部署”的托管域。   在完全预配托管域之前无法对其进行配置。

    ![预配期间的域服务状态](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. 托管域完全预配之后，“概览”  选项卡会将域状态显示为“正在运行”  。

    ![成功预配后的域服务状态](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

托管域与 Azure AD 租户相关联。 在预配过程中，Azure AD DS 会在 Azure AD 租户中创建名为 Domain Controller Services  和 AzureActiveDirectoryDomainControllerServices  的两个企业应用程序。 需要这些企业应用程序来为托管域提供服务。 不要删除这些应用程序。

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>更新 Azure 虚拟网络的 DNS 设置

成功部署 Azure AD DS 后，请配置虚拟网络，以允许其他连接的 VM 和应用程序使用托管域。 若要提供此连接，请更新虚拟网络的 DNS 服务器设置，以指向部署 Azure AD DS 的两个 IP 地址。

1. 托管域的“概述”选项卡显示了一些“必需的配置步骤”。   第一个配置步骤是更新虚拟网络的 DNS 服务器设置。 正确配置 DNS 设置后，不再会显示此步骤。

    列出的地址是在虚拟网络中使用的域控制器。 在本示例中，这些地址为 *10.1.0.4* 和 *10.1.0.5*。 稍后可在“属性”选项卡上找到这些 IP 地址。 

    ![使用 Azure AD 域服务 IP 地址配置虚拟网络的 DNS 设置](./media/tutorial-create-instance-advanced/configure-dns.png)

1. 若要更新虚拟网络的 DNS 服务器设置，请选择“配置”按钮。  系统会自动为虚拟网络配置 DNS 设置。

> [!TIP]
> 如果在前面的步骤中选择了现有的虚拟网络，连接到该网络的任何 VM 只会在重启后才能获取新的 DNS 设置。 可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 来重启 VM。

## <a name="enable-user-accounts-for-azure-ad-ds"></a>启用 Azure AD DS 的用户帐户

若要对托管域上的用户进行身份验证，Azure AD DS 需要密码哈希，其格式适用于 NT LAN Manager (NTLM) 和 Kerberos 身份验证。 除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

> [!NOTE]
> 经过适当的配置后，可用的密码哈希将存储在 Azure AD DS 托管域中。 删除 Azure AD DS 托管域也会删除其中存储的所有密码哈希。 如果以后创建 Azure AD DS 托管域，Azure AD 中已同步的凭据信息不可重复使用 - 必须重新配置密码哈希同步，以再次存储密码哈希。 以前加入域的 VM 或用户无法立即进行身份验证 - Azure AD 需要在新的 Azure AD DS 托管域中生成并存储密码哈希。 有关详细信息，请参阅 [Azure AD DS 和 Azure AD Connect 的密码哈希同步过程][password-hash-sync-process]。

对于在 Azure AD 中创建的仅限云的用户帐户而言，生成和存储这些密码哈希的步骤不同于使用 Azure AD Connect 从本地目录同步的用户帐户。 仅限云的用户帐户是在 Azure AD 目录中使用 Azure 门户或 Azure AD PowerShell cmdlet 创建的帐户。 这些用户帐户不是从本地目录同步的。 本教程使用一个基本的仅限云的用户帐户。 有关使用 Azure AD Connect 所需的其他步骤的详细信息，请参阅[将从本地 AD 同步的用户帐户的密码哈希同步到托管域][on-prem-sync]。

> [!TIP]
> 如果 Azure AD 租户既有仅限云的用户，又有来自本地 AD 的用户，则需完成两组步骤。

对于仅限云的用户帐户，用户必须更改其密码才能使用 Azure AD DS。 此密码更改过程会导致在 Azure AD 中生成并存储用于 Kerberos 和 NTLM 身份验证的密码哈希。 对于需要使用 Azure AD DS 的所有租户中用户，可以使其密码过期，以强制他们在下次登录时更改密码，或指示他们手动更改密码。 对于本教程，我们将手动更改用户密码。

只有在 Azure AD 租户中[配置自助式密码重置][configure-sspr]后，用户才能重置其密码。

若要更改仅限云的用户的密码，用户必须完成以下步骤：

1. 转到 Azure AD 访问面板页 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。
1. 在右上角选择自己的姓名，然后从下拉菜单中选择“个人资料”。 

    ![选择配置文件](./media/tutorial-create-instance-advanced/select-profile.png)

1. 在“个人资料”页上，选择“更改密码”。  
1. 在“更改密码”页上输入现有（旧）密码，然后输入并确认新密码。 
1. 选择“提交”。 

更改密码后，需要几分钟才能在 Azure AD DS 中使用新密码，并成功登录已加入托管域的计算机。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 为托管域配置 DNS 和虚拟网络设置
> * 创建 Azure AD DS 实例
> * 将管理用户添加到域管理
> * 启用 Azure AD DS 的用户帐户并生成密码哈希

若要了解此托管域的运作情况，请创建一个虚拟机并将其加入域。

> [!div class="nextstepaction"]
> [将 Windows Server 虚拟机加入托管域](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md

<!-- EXTERNAL LINKS -->
