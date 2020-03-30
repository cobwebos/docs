---
title: 教程 - 在 Azure AD 域服务中创建林信任 |微软文档
description: 了解如何在 Azure 门户中为 Azure AD 域服务创建到本地 AD DS 域的单向出站林
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5620d1cdc7dc71bdac17057b9a13a74150b12d5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612515"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>教程：在 Azure 活动目录域服务（预览）中创建到本地域的出站林信任

在无法同步密码哈希或用户使用智能卡专门登录，以便他们不知道其密码的环境中，可以在 Azure 活动目录域服务 （AD DS） 中使用资源林。 资源林使用从 Azure AD DS 到一个或多个本地 AD DS 环境的单向出站信任。 此信任关系允许用户、应用程序和计算机根据 Azure AD DS 托管域的本地域进行身份验证。 Azure AD DS 资源林当前处于预览状态。

![从 Azure AD DS 到本地 AD DS 的林信任图](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在本地 AD DS 环境中配置 DNS 以支持 Azure AD DS 连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 在 Azure AD DS 中创建单向出站林信任
> * 测试和验证身份验证和资源访问的信任关系

如果你没有 Azure 订阅，可以在开始之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 使用资源林创建并在 Azure AD 租户中配置的 Azure 活动目录域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance-advanced]。
    
    > [!IMPORTANT]
    > 请确保使用*资源*林创建 Azure AD DS 托管域。 默认选项将创建*用户*林。 只有资源林才能创建对 AD DS 环境的信任。 您还需要为托管域使用最少的*企业*SKU。 如果需要[，更改 Azure AD DS 托管域的 SKU。][howto-change-sku]

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，使用 Azure 门户创建和配置 Azure AD DS 的出站林信任。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="networking-considerations"></a>网络注意事项

承载 Azure AD DS 资源林的虚拟网络需要连接到本地活动目录。 应用程序和服务还需要与托管 Azure AD DS 资源林的虚拟网络进行网络连接。 与 Azure AD DS 资源林的网络连接必须始终打开且稳定，否则用户可能无法对资源进行身份验证或访问。

在 Azure AD DS 中配置林信任之前，请确保 Azure 和本地环境之间的网络满足以下要求：

* 使用专用 IP 地址。 不要依赖具有动态 IP 地址分配的 DHCP。
* 避免重叠的 IP 地址空间，以允许虚拟网络对等互连和路由在 Azure 和本地之间成功通信。
* Azure 虚拟网络需要网关子网来配置站点到站点 （S2S） VPN 或 ExpressRoute 连接
* 创建具有足够 IP 地址的子网以支持您的方案。
* 确保 Azure AD DS 有自己的子网，不要与应用程序 VM 和服务共享此虚拟网络子网。
* 对等虚拟网络不是过渡的。
    * 必须在要使用 Azure AD DS 资源林信任到本地 AD DS 环境的所有虚拟网络之间创建 Azure 虚拟网络对等互连。
* 提供与本地活动目录林的连续网络连接。 不要使用按需连接。
* 确保 Azure AD DS 资源林名和本地活动目录林名之间具有连续名称解析 （DNS）。

## <a name="configure-dns-in-the-on-premises-domain"></a>在本地域中配置 DNS

要从本地环境正确解析 Azure AD DS 托管域，可能需要向现有 DNS 服务器添加转发器。 如果尚未配置本地环境以与 Azure AD DS 托管域通信，请从本地 AD DS 域的管理工作站完成以下步骤：

1. 选择 **"开始" |管理工具 |DNS**
1. 右选择 DNS 服务器（如*myAD01）* 选择**属性**
1. 选择**转发器**，然后**编辑**以添加其他转发器。
1. 添加 Azure AD DS 托管域的 IP 地址，例如*10.0.1.4*和*10.0.1.5*。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>在本地域中创建入站林信任

本地 AD DS 域需要 Azure AD DS 托管域的传入林信任。 此信任必须在本地 AD DS 域中手动创建，无法从 Azure 门户创建。

要配置本地 AD DS 域上的入站信任，请从本地 AD DS 域的管理工作站完成以下步骤：

1. 选择 **"开始" |管理工具 |活动目录域和信任**
1. 右选择域，如*onprem.contoso.com，* 选择**属性**
1. 选择 **"信任"** 选项卡，然后**选择"新信任"**

   > [!NOTE]
   > 如果看不到 **"信任"** 菜单选项，请在 *"林"类型的***"属性**"下进行检查。 只有*资源*林才能创建信任。 如果林类型为 *"用户*"，则无法创建信任。 当前无法更改 Azure AD DS 托管域的林类型。 您需要删除托管域并将其重新创建为资源林。

1. 在 Azure AD DS 域名上输入名称，如*aaddscontoso.com*，然后选择 **"下一步"**
1. 选择创建**林信任**的选项，然后创建**一个单向：传入**信任。
1. 选择**仅为此域**创建信任。 在下一步中，您将在 Azure 门户中为 Azure AD DS 托管域创建信任。
1. 选择使用**森林范围的身份验证**，然后输入并确认信任密码。 下一节的 Azure 门户中也会输入相同的密码。
1. 使用默认选项执行接下来的几个窗口，然后选择 **"否"选项，不要确认传出信任**。
1. 选择 **"完成"**

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>在 Azure AD DS 中创建出站林信任

配置本地 AD DS 域以解决 Azure AD DS 托管域，并创建了入站林信任，现在创建了出站林信任。 此出站林信任完成本地 AD DS 域和 Azure AD DS 托管域之间的信任关系。

要在 Azure 门户中为 Azure AD DS 托管域创建出站信任，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**，然后选择托管域，例如*aaddscontoso.com*
1. 从 Azure AD DS 托管域左侧的菜单中，选择 **"信任"，** 然后选择 **"添加**信任"。
1. 输入标识信任的显示名称，然后输入本地受信任的林 DNS 名称，如*onprem.contoso.com*
1. 提供上一节中为本地 AD DS 域配置入站林信任时使用的信任密码。
1. 为本地 AD DS 域至少提供两台 DNS 服务器，如*10.0.2.4*和*10.0.2.5*
1. 准备就绪后，**保存**出站林信任

    [在 Azure 门户中创建出站林信任](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>验证资源身份验证

以下常见方案允许您验证林信任是否正确验证用户和对资源的访问：

* [来自 Azure AD DS 资源林的本地用户身份验证](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [使用本地用户访问 Azure AD DS 资源林中的资源](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [启用文件和打印机共享](#enable-file-and-printer-sharing)
    * [创建安全组并添加成员](#create-a-security-group-and-add-members)
    * [为跨林访问创建文件共享](#create-a-file-share-for-cross-forest-access)
    * [验证对资源的跨林身份验证](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>来自 Azure AD DS 资源林的本地用户身份验证

应将 Windows 服务器虚拟机连接到 Azure AD DS 资源域。 使用此虚拟机可以测试本地用户可以在虚拟机上进行身份验证。

1. 使用远程桌面和 Azure AD DS 管理员凭据连接到连接到 Azure AD DS 资源林的 Windows 服务器 VM。 如果出现网络级身份验证 （NLA） 错误，请检查所使用的用户帐户不是域用户帐户。

    > [!NOTE]
    > 要安全地连接到连接到 Azure AD 域服务的 VM，可以在支持的 Azure 区域中使用[Azure 堡垒主机服务](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打开命令提示并使用 该`whoami`命令显示当前经过身份验证的用户的可分辨名称：

    ```console
    whoami /fqdn
    ```

1. 使用`runas`命令从本地域进行身份验证为用户。 在以下命令中，从`userUpn@trusteddomain.com`受信任的本地域替换为用户的 UPN。 该命令提示您输入用户的密码：

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 如果身份验证成功，将打开一个新的命令提示。 新命令提示符的标题包括`running as userUpn@trusteddomain.com`。
1. 在新`whoami /fqdn`命令提示中使用从本地活动目录查看经过身份验证的用户的可分辨名称。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>使用本地用户访问 Azure AD DS 资源林中的资源

使用加入到 Azure AD DS 资源林的 Windows Server VM，可以测试以下方案：当用户从本地域中的计算机与本地域中的用户进行身份验证时，可以访问资源林中托管的资源。 以下示例演示如何创建和测试各种常见方案。

#### <a name="enable-file-and-printer-sharing"></a>启用文件和打印机共享

1. 使用远程桌面和 Azure AD DS 管理员凭据连接到连接到 Azure AD DS 资源林的 Windows 服务器 VM。 如果出现网络级身份验证 （NLA） 错误，请检查所使用的用户帐户不是域用户帐户。

    > [!NOTE]
    > 要安全地连接到连接到 Azure AD 域服务的 VM，可以在支持的 Azure 区域中使用[Azure 堡垒主机服务](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打开**Windows 设置**，然后搜索并选择**网络和共享中心**。
1. 选择 **"更改高级共享设置"** 选项。
1. 在 **"域配置文件**"下，选择 **"打开文件和打印机共享**"，然后**保存更改**。
1. 关闭**网络和共享中心**。

#### <a name="create-a-security-group-and-add-members"></a>创建安全组并添加成员

1. 打开“Active Directory 用户和计算机”****。
1. 右选择域名，选择 **"新建**"，然后选择 **"组织单位**"。
1. 在名称框中，键入 *"局部对象*"，然后选择 **"确定**"。
1. 选择并右键单击导航窗格中的 **"本地对象**"。 选择 **"新建**"，然后**组**。
1. 在**组名称**框中键入*文件服务器访问*。 对于**组作用域**，选择 **"域本地**"，然后选择 **"确定**"。
1. 在内容窗格中，双击**文件服务器访问**。 选择 **"成员**"，选择 **"添加**"，然后选择 **"位置**"。
1. 从**位置**视图中选择本地活动目录，然后选择 **"确定**"。
1. 在 **"输入要选择的对象名称**"中键入*域用户*。 选择 **"检查名称**"，为本地活动目录提供凭据，然后选择 **"确定**"。

    > [!NOTE]
    > 您必须提供凭据，因为信任关系只是一种方式。 这意味着 Azure AD DS 中的用户无法访问资源或搜索受信任（本地）域中的用户或组。

1. 本地活动目录中的**域用户**组应是**FileServerAccess**组的成员。 选择 **"确定"** 以保存组并关闭窗口。

#### <a name="create-a-file-share-for-cross-forest-access"></a>为跨林访问创建文件共享

1. 在加入 Azure AD DS 资源林的 Windows 服务器 VM 上，创建文件夹并提供名称，如*交叉林共享*。
1. 右选择文件夹并选择 **"属性**"。
1. 选择"**安全**"选项卡，然后选择 **"编辑**"。
1. 在 *"跨林共享的权限"对话框中*，选择 **"添加**"。
1. 键入*文件服务器访问*输入**对象名称以选择**，然后选择 **"确定**"。
1. 从**组或用户名**列表中选择*文件服务器访问权限*。 在 **"文件服务器访问权限"** 列表中，选择 *"允许***修改**和**写入**权限"，然后选择 **"确定**"。
1. 选择"**共享**"选项卡，然后选择 **"高级共享..."，"**
1. 选择 **"共享此文件夹**"，然后在 **"共享共享共享**共享"中为文件共享输入一个令人难忘的名称，如*CrossForestShare*。
1. 选择**权限**。 在"**所有人的权限"** 列表中，选择 **"允许****更改**"权限。
1. 选择 **"确定**两次"，然后**关闭**。

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>验证对资源的跨林身份验证

1. 使用本地活动目录中的用户帐户登录本地活动目录的 Windows 计算机。
1. 使用**Windows 资源管理器**，连接到使用完全限定的主机名和共享（如`\\fs1.aaddscontoso.com\CrossforestShare`）创建的共享。
1. 要验证写入权限，请在文件夹中右选择，选择 **"新建**"，然后选择 **"文本文档**"。 使用默认名称 **"新文本文档**"。

    如果写入权限设置正确，将创建新的文本文档。 然后，以下步骤将根据需要打开、编辑和删除文件。
1. 要验证读取权限，请打开 **"新文本文档**"。
1. 要验证修改权限，请向文件添加文本并关闭**记事本**。 当提示保存更改时，选择 **"保存**"。
1. 要验证删除权限，请右选择 **"新建文本文档"** 并选择 **"删除**"。 选择 **"是**"以确认文件删除。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 在本地 AD DS 环境中配置 DNS 以支持 Azure AD DS 连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 在 Azure AD DS 中创建单向出站林信任
> * 测试和验证身份验证和资源访问的信任关系

有关 Azure AD DS 中的林类型的详细信息，请参阅[什么是资源林？][concepts-forest]和[在 Azure AD DS 中，林信任如何工作？][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
