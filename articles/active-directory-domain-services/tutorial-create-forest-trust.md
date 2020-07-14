---
title: 教程 - 在 Azure AD 域服务中创建林信任 | Microsoft Docs
description: 了解如何在 Azure 门户中为 Azure AD 域服务创建到本地 AD DS 域的单向出站林
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 40dd7f1b177fd1319b145036c8263ba2c6e30137
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024666"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>教程：在 Azure Active Directory 域服务（预览版）中创建到本地域的出站林信任

在无法同步密码哈希的环境中，或者用户只使用智能卡登录，因此他们不知道密码的情况下，你可以在 Azure Active Directory 域服务 (Azure AD DS) 中使用资源林。 资源林使用从 Azure AD DS 到一个或多个本地 AD DS 环境的单向出站信任。 这种信任关系可让用户、应用程序和计算机通过 Azure AD DS 托管域向本地域进行身份验证。 Azure AD DS 资源林目前为预览版。

![从 Azure AD DS 到本地 AD DS 的林信任关系图](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在本地 AD DS 环境中配置 DNS 以支持 Azure AD DS 连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 在 Azure AD DS 中创建单向出站林信任
> * 测试并验证用于身份验证和资源访问的信任关系

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 使用资源林创建的并在 Azure AD 租户中配置的 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance-advanced]。
    
    > [!IMPORTANT]
    > 确保使用“资源”林创建托管域。 默认选项是创建用户林。 只有资源林才能创建对本地 AD DS 环境的信任。
    >
    > 此外，至少需要为托管域使用“企业版”SKU。 如果需要，请[更改托管域的 SKU][howto-change-sku]。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，你将使用 Azure 门户从 Azure AD DS 创建并配置出站林信任。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="networking-considerations"></a>网络注意事项

托管 Azure AD DS 资源林的虚拟网络需要与本地 Active Directory 建立网络连接。 应用程序和服务也需要与托管 Azure AD DS 资源林的虚拟网络建立网络连接。 与 Azure AD DS 资源林建立的网络连接必须始终保持连通且稳定，否则用户可能无法进行身份验证或访问资源。

在 Azure AD DS 中配置林信任之前，请确保 Azure 与本地环境之间的网络符合以下要求：

* 使用专用 IP 地址。 不要依赖 DHCP 进行动态 IP 地址分配。
* 避免 IP 地址空间重叠，使虚拟网络对等互连和路由能够在 Azure 与本地之间成功通信。
* Azure 虚拟网络需要通过一个网关子网来配置 [Azure 站点到站点 (S2S) VPN][vpn-gateway] 或 [ExpressRoute][expressroute] 连接
* 使用足够的 IP 地址创建子网，以支持你的方案。
* 确保 Azure AD DS 具有自己的子网，且不要与应用程序 VM 和服务共享此虚拟网络子网。
* 对等互连的虚拟网络不是中转性的。
    * 必须在你要在其中使用对本地 AD DS 环境的 Azure AD DS 资源林信任的所有虚拟网络之间创建 Azure 虚拟网络对等互连。
* 与本地 Active Directory 林建立持续的网络连接。 不要使用按需连接。
* 确保 Azure AD DS 资源林名称与本地 Active Directory 林名称之间存在持续的名称解析 (DNS)。

## <a name="configure-dns-in-the-on-premises-domain"></a>在本地域中配置 DNS

若要从本地环境正确解析托管域，可能需要向现有 DNS 服务器添加转发器。 如果尚未将本地环境配置为与托管域通信，请从管理工作站为本地 AD DS 域完成以下步骤：

1. 选择“开始”|“管理工具”|“DNS”
1. 右键单击 DNS 服务器（例如“myAD01”），然后选择“属性”
1. 选择“转发器”，然后选择“编辑”以添加更多转发器。 
1. 添加托管域的 IP 地址，例如 10.0.2.4 和 10.0.2.5 。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>在本地域中创建入站林信任

本地 AD DS 域需要托管域的传入林信任。 必须手动在本地 AD DS 域中创建此信任，而不能在 Azure 门户中创建。

若要在本地 AD DS 域上配置入站信任，请在管理工作站中针对本地 AD DS 域完成以下步骤：

1. 选择“开始”|“管理工具”|“Active Directory 域和信任”
1. 右键单击域（例如“onprem.contoso.com”），然后选择“属性”
1. 依次选择“信任”选项卡、“新建信任” 
1. 在 Azure AD DS 域名中输入名称（例如“aaddscontoso.com”），然后选择“下一步”
1. 选择创建“林信任”的选项，然后选择创建“单向: 传入”信任的选项。 
1. 选择创建“仅限此域”的信任。 在下一步骤中，你将在 Azure 门户中为托管域创建信任。
1. 选择使用“全林性身份验证”，然后输入并确认信任密码。 在下一部分中，也要在 Azure 门户中输入同一密码。
1. 在接下来的几个窗口中使用默认选项完成每个步骤，然后选择选项“否，不要确认传出信任”。
1. 选择“完成”

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>在 Azure AD DS 中创建出站林信任

配置本地 AD DS 域以解析托管域并创建入站林信任后，现在请创建出站林信任。 此出站林信任完成本地 AD DS 域与托管域之间的信任关系。

若要在 Azure 门户中为托管域创建出站信任，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”，然后选择你的托管域，例如 aaddscontoso.com
1. 从托管域左侧的菜单中选择“信任”，然后选择“+ 添加”以添加信任 。

   > [!NOTE]
   > 如果看不到“信任”菜单选项，请在“属性”下检查“林类型”。  只有资源林才能创建信任。 如果林类型是“用户”，则无法创建信任。 目前没有任何办法可以更改托管域的林类型。 需要删除托管域，然后重新创建资源林形式的托管域。

1. 输入用于标识信任的显示名称，然后输入本地受信任林的 DNS 名称，例如 onprem.contoso.com
1. 提供在上一部分为本地 AD DS 域配置入站林信任时使用的同一信任密码。
1. 为本地 AD DS 域提供至少两个 DNS 服务器，例如 10.1.1.4 和 10.1.1.5 
1. 准备就绪后，保存出站林信任

    ![在 Azure 门户中创建出站林信任](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>验证资源身份验证

可以使用以下常用方案来验证林信任是否正确对用户进行身份验证以及是否正确访问资源：

* [从 Azure AD DS 资源林进行本地用户身份验证](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [使用本地用户访问 Azure AD DS 资源林中的资源](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [启用文件和打印机共享](#enable-file-and-printer-sharing)
    * [创建安全组并添加成员](#create-a-security-group-and-add-members)
    * [创建文件共享用于跨林访问](#create-a-file-share-for-cross-forest-access)
    * [验证向资源进行的跨林身份验证](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>从 Azure AD DS 资源林进行本地用户身份验证

应事先将 Windows Server 虚拟机加入托管域。 使用此虚拟机来测试本地用户是否可在虚拟机上进行身份验证。 如果需要，请[创建 Windows VM，并将其加入托管域][join-windows-vm]。

1. 使用 [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) 和 Azure AD DS 管理员凭据连接到已加入 Azure AD DS 资源林的 Windows Server VM。
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

使用已加入 Azure AD DS 资源林的 Windows Server VM，可以测试当用户以本地域中的用户身份从本地域中的计算机进行身份验证时，是否可以访问资源林中托管的资源。 以下示例展示了如何创建并测试各种常用方案。

#### <a name="enable-file-and-printer-sharing"></a>启用文件和打印机共享

1. 使用 [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview) 和 Azure AD DS 管理员凭据连接到已加入 Azure AD DS 资源林的 Windows Server VM。

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
    > 必须提供凭据，因为信任关系只是单向的。 这意味着，Azure AD DS 托管域中的用户无法访问资源或搜索受信任的（本地）域中的用户或组。

1. 本地 Active Directory 中的“域用户”组应是“FileServerAccess”组的成员。  选择“确定”以保存该组并关闭窗口。

#### <a name="create-a-file-share-for-cross-forest-access"></a>创建文件共享用于跨林访问

1. 在已加入 Azure AD DS 资源林的 Windows Server VM 上，创建一个文件夹并提供名称（例如 CrossForestShare）。
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

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 在本地 AD DS 环境中配置 DNS 以支持 Azure AD DS 连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 在 Azure AD DS 中创建单向出站林信任
> * 测试并验证用于身份验证和资源访问的信任关系

有关 Azure AD DS 中的林类型的更多概念信息，请参阅[什么是资源林？][concepts-forest]和[林信任在 Azure AD DS 中的工作原理是什么？][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md
