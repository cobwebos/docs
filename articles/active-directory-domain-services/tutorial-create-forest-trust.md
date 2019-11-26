---
title: 教程-在 Azure AD 域服务中创建林信任 |Microsoft Docs
description: 了解如何在 Azure AD 域服务的 Azure 门户中为本地 AD DS 域创建单向出站林
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: f861303b7f3bc8d37caf6da0eaf2f4cef4b36ee5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233592"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>教程：在 Azure Active Directory 域服务（预览版）中创建对本地域的出站林信任

在无法同步密码哈希的环境中，或者你的用户使用智能卡登录时，如果不知道密码，你可以在 Azure Active Directory 域服务（AD DS）中使用资源林。 资源林从 Azure AD DS 到一个或多个本地 AD DS 环境使用单向出站信任。 这种信任关系允许用户、应用程序和计算机从 Azure AD DS 托管域的本地域进行身份验证。 Azure AD DS 资源林目前处于预览阶段。

![从 Azure AD DS 到本地 AD DS 的林信任关系图](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在本地 AD DS 环境中配置 DNS 以支持 Azure AD DS 连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 在 Azure AD DS 中创建单向出站林信任
> * 测试和验证身份验证和资源访问的信任关系

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 使用资源林创建并在 Azure AD 租户中配置的 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance-advanced]。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，你将使用 Azure 门户从 Azure AD DS 创建和配置出站林信任。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="networking-considerations"></a>网络注意事项

承载 Azure AD DS 资源林的虚拟网络需要与本地 Active Directory 的网络连接。 应用程序和服务还需要与托管 Azure AD DS 资源林的虚拟网络建立网络连接。 与 Azure AD DS 资源林的网络连接必须保持打开和稳定，否则，用户可能无法对资源进行身份验证或访问。

在 Azure AD DS 中配置林信任之前，请确保 Azure 和本地环境之间的网络满足以下要求：

* 使用专用 IP 地址。 不要依赖于具有动态 IP 地址分配的 DHCP。
* 避免 IP 地址空间重叠，以允许虚拟网络对等互连和路由在 Azure 和本地之间成功通信。
* Azure 虚拟网络需要网关子网来配置站点到站点（S2S） VPN 或 ExpressRoute 连接
* 创建具有足够 IP 地址的子网，以支持你的方案。
* 请确保 Azure AD DS 有自己的子网，不要与应用程序 Vm 和服务共享此虚拟网络子网。
* 对等互连虚拟网络是不可传递的。
    * 必须在要使用 Azure AD DS 资源林信任向本地 AD DS 环境的所有虚拟网络之间创建 Azure 虚拟网络对等互连。
* 提供与本地 Active Directory 林之间的连续网络连接。 请勿使用按需连接。
* 请确保 Azure AD DS 资源林名称与本地 Active Directory 林名称之间存在连续名称解析（DNS）。

## <a name="configure-dns-in-the-on-premises-domain"></a>在本地域中配置 DNS

若要从本地环境正确解析 Azure AD DS 托管域，可能需要将转发器添加到现有的 DNS 服务器。 如果尚未将本地环境配置为与 Azure AD DS 托管域进行通信，请在本地 AD DS 域的管理工作站中完成以下步骤：

1. 选择**开始 |管理工具 |DNS**
1. 右键选择 "DNS 服务器" （如*myAD01*），选择 "**属性**"
1. 选择 "**转发器**"，然后单击 "**编辑**" 以添加其他转发器。
1. 添加 Azure AD DS 托管域的 IP 地址，例如 " *10.0.1.4* " 和 " *10.0.1.5*"。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>在本地域中创建入站林信任

本地 AD DS 域需要 Azure AD DS 托管域的传入林信任。 必须在本地 AD DS 域中手动创建此信任，无法从 Azure 门户创建该信任。

若要在本地 AD DS 域上配置入站信任，请在本地 AD DS 域的管理工作站中完成以下步骤：

1. 选择**开始 |管理工具 |Active Directory 域和信任关系**
1. 右键选择域（如*onprem.contoso.com*），选择 "**属性**"
1. 选择 "**信任**" 选项卡，然后选择 "**新建信任**"
1. 在 Azure AD DS 域名上输入名称（例如*aadds.contoso.com*），然后选择 "**下一步**"
1. 选择用于创建**林信任**的选项，然后创建**一个方法：传入**信任。
1. 选择**仅为此域**创建信任。 在下一步中，你将在 Azure AD DS 托管域的 Azure 门户中创建信任。
1. 选择使用**全林性身份验证**，并输入并确认信任密码。 下一节中的 Azure 门户也会输入同一密码。
1. 使用默认选项单步执行下几个窗口，然后选择 "否" 选项 **，不要确认传出信任**。
1. 选择“完成”

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>在 Azure AD DS 中创建出站林信任

使用本地 AD DS 域配置为解析 Azure AD DS 托管域和创建入站林信任时，现在创建出站林信任。 此出站林信任完成本地 AD DS 域和 Azure AD DS 托管域之间的信任关系。

若要为 Azure 门户中 Azure AD DS 托管域创建出站信任，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择 " **Azure AD 域服务**"，然后选择托管域，如*aadds.contoso.com*
1. 在 Azure AD DS 托管域的左侧菜单中，选择 "**信任**"，然后选择 " **+ 添加**信任"。
1. 输入标识信任的显示名称，然后输入本地受信任的林 DNS 名称，例如*onprem.contoso.com*
1. 提供在上一节中为本地 AD DS 域配置入站林信任时使用的相同的信任密码。
1. 为本地 AD DS 域（例如*10.0.2.4*和*10.0.2.5* ）至少提供两个 DNS 服务器。
1. 准备就绪后，**保存**出站林信任

    [在 Azure 门户中创建出站林信任](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>验证资源身份验证

以下常见方案使你可以验证林信任是否正确地对用户进行身份验证并访问资源：

* [Azure AD DS 资源林中的本地用户身份验证](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [使用本地用户访问 Azure AD DS 资源林中的资源](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [启用文件和打印机共享](#enable-file-and-printer-sharing)
    * [创建安全组并添加成员](#create-a-security-group-and-add-members)
    * [创建用于跨林访问的文件共享](#create-a-file-share-for-cross-forest-access)
    * [验证资源的跨林身份验证](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS 资源林中的本地用户身份验证

应已将 Windows Server 虚拟机加入到 Azure AD DS 资源域。 使用此虚拟机测试本地用户是否可以在虚拟机上进行身份验证。

1. 使用远程桌面和 Azure AD DS 管理员凭据连接到已加入到 Azure AD DS 资源林的 Windows Server VM。 如果收到网络级别身份验证（NLA）错误，请检查你使用的用户帐户不是域用户帐户。

    > [!NOTE]
    > 若要安全地连接到已加入到 Azure AD 域服务的 Vm，可以在支持的 Azure 区域使用[Azure 堡垒主机服务](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打开命令提示符，并使用 `whoami` 命令显示当前经过身份验证的用户的可分辨名称：

    ```console
    whoami /fqdn
    ```

1. 使用 `runas` 命令作为本地域中的用户进行身份验证。 在以下命令中，将 `userUpn@trusteddomain.com` 替换为受信任的本地域中的用户的 UPN。 此命令会提示用户输入密码：

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 如果身份验证成功，将打开一个新的命令提示符。 新命令提示符的标题包含 `running as userUpn@trusteddomain.com`。
1. 使用新的命令提示符中的 `whoami /fqdn` 查看本地 Active Directory 中经过身份验证的用户的可分辨名称。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>使用本地用户访问 Azure AD DS 资源林中的资源

使用加入到 Azure AD DS 资源林的 Windows Server VM，可以测试当用户通过本地域中的用户进行身份验证时，用户可以访问资源林中托管的资源的情况。 下面的示例演示如何创建和测试各种常见方案。

#### <a name="enable-file-and-printer-sharing"></a>启用文件和打印机共享

1. 使用远程桌面和 Azure AD DS 管理员凭据连接到已加入到 Azure AD DS 资源林的 Windows Server VM。 如果收到网络级别身份验证（NLA）错误，请检查你使用的用户帐户不是域用户帐户。

    > [!NOTE]
    > 若要安全地连接到已加入到 Azure AD 域服务的 Vm，可以在支持的 Azure 区域使用[Azure 堡垒主机服务](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 打开**Windows 设置**，搜索并选择 "**网络和共享中心**"。
1. 选择 "**更改高级共享**设置" 选项。
1. 在 "**域配置文件**" 下，选择 "**打开文件和打印机共享**"，然后**保存更改**。
1. 关闭 "**网络和共享中心**"。

#### <a name="create-a-security-group-and-add-members"></a>创建安全组并添加成员

1. 打开“Active Directory 用户和计算机”。
1. 右键选择域名，选择 "**新建**"，然后选择 "**组织单位**"。
1. 在 "名称" 框中，键入*LocalObjects*，然后选择 **"确定"** 。
1. 选择并右键单击导航窗格中的 " **LocalObjects** "。 依次选择 "**新建**" 和 "**组**"。
1. 在 "**组名**" 框中键入*FileServerAccess* 。 对于 "**组作用域**"，选择 "**本地域**"，然后选择 **"确定"** 。
1. 在内容窗格中，双击 " **FileServerAccess**"。 依次选择 "**成员**"、"**添加**"，然后选择 "**位置**"。
1. 从 "**位置**" 视图中选择本地 Active Directory，然后选择 **"确定"** 。
1. 在 "**输入要选择的对象名称**" 框中键入 "*域用户*"。 选择 "**检查名称**"，为本地 Active Directory 提供凭据，然后选择 **"确定"** 。

    > [!NOTE]
    > 您必须提供凭据，因为信任关系仅是一种方法。 这意味着，Azure AD DS 中的用户无法访问资源或搜索受信任的（本地）域中的用户或组。

1. 本地 Active Directory 中的**域用户**组应是**FileServerAccess**组的成员。 选择 **"确定"** 以保存组并关闭窗口。

#### <a name="create-a-file-share-for-cross-forest-access"></a>创建用于跨林访问的文件共享

1. 在加入到 Azure AD DS 资源林的 Windows Server VM 上，创建一个文件夹并提供名称，例如*CrossForestShare*。
1. 右键选择文件夹，然后选择 "**属性**"。
1. 选择 "**安全**" 选项卡，然后选择 "**编辑**"。
1. 在 " *CrossForestShare 的权限*" 对话框中，选择 "**添加**"。
1. 在 "**输入要选择的对象名称**" 中键入*FileServerAccess* ，然后选择 **"确定"** 。
1. 从 "**组或用户名**" 列表中选择 " *FileServerAccess* "。 在 " **FileServerAccess 的权限**" 列表中，选择 "*允许***修改**和**写入**权限"，然后选择 **"确定"** 。
1. 选择 "**共享**" 选项卡，然后选择 "**高级共享 ...** "
1. 选择 "**共享此文件夹**"，然后为**共享名称**（如*CrossForestShare*）中的文件共享输入一个便于记忆的名称。
1. 选择**权限**。 在 "**适用于每个人的权限**" 列表中，选择 "**允许** **更改**权限"。
1. 选择 **"确定"** 两次，然后**关闭**。

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>验证资源的跨林身份验证

1. 使用本地 Active Directory 中的用户帐户登录加入本地 Active Directory 的 Windows 计算机。
1. 使用**Windows 资源管理器**，连接到使用完全限定的主机名和共享（例如 `\\fs1.aadds.contoso.com\CrossforestShare`）创建的共享。
1. 若要验证写入权限，请在文件夹中右键单击，选择 "**新建**"，然后选择 "**文本文档**"。 使用默认名称 "**新建文本文档**"。

    如果正确设置了写入权限，将创建一个新的文本文档。 以下步骤随后会根据需要打开、编辑和删除文件。
1. 若要验证读取权限，请打开 "**新建文本文档**"。
1. 若要验证修改权限，请将文本添加到文件并关闭**记事本**。 系统提示保存更改时，请选择 "**保存**"。
1. 若要验证删除权限，请右键选择 "**新建文本文档**"，然后选择 "**删除**"。 选择 **"是"** 以确认删除文件。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习了如何执行以下操作：

> [!div class="checklist"]
> * 在本地 AD DS 环境中配置 DNS 以支持 Azure AD DS 连接
> * 在本地 AD DS 环境中创建单向入站林信任
> * 在 Azure AD DS 中创建单向出站林信任
> * 测试和验证身份验证和资源访问的信任关系

有关 Azure AD DS 中的林类型的更多概念信息，请参阅[什么是资源林？][concepts-forest]和[林信任如何在 Azure AD DS 中工作？][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md