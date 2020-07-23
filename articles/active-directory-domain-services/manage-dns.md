---
title: 管理 Azure AD 域服务的 DNS |Microsoft Docs
description: 了解如何安装 DNS 服务器工具来管理 DNS，并为 Azure Active Directory 域服务托管域创建条件转发器。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: b48fb62532402338fdf53cd6f9b15bac812c3c2c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86040208"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>管理 DNS 并在 Azure Active Directory 域服务托管域中创建条件转发器

在 Azure Active Directory 域服务（Azure AD DS）中，密钥组件是 DNS （域名解析）。 Azure AD DS 包括为托管域提供名称解析的 DNS 服务器。 此 DNS 服务器包含允许服务运行的关键组件的内置 DNS 记录和更新。

当您运行自己的应用程序和服务时，您可能需要为未加入域的计算机创建 DNS 记录，为负载均衡器配置虚拟 IP 地址，或者设置外部 DNS 转发器。 属于*AAD DC 管理员*组的用户将获得对 Azure AD DS 托管域的 DNS 管理权限，并且可以创建和编辑自定义 DNS 记录。

在混合环境中，其他 DNS 命名空间中配置的 DNS 区域和记录（例如本地 AD DS 环境）不会同步到托管域。 若要解析其他 DNS 命名空间中的命名资源，请创建并使用指向环境中现有 DNS 服务器的条件转发器。

本文介绍如何安装 DNS 服务器工具，然后使用 DNS 控制台来管理记录，并在 Azure AD DS 中创建条件转发器。

## <a name="before-you-begin"></a>开始之前

需要拥有以下资源和权限才能完成本文中的操作：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建和配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 从 Azure AD DS 虚拟网络连接到托管其他 DNS 命名空间的位置。
    * 可以通过[Azure ExpressRoute][expressroute]或[Azure VPN 网关][vpn-gateway]连接来提供此连接。
* 加入托管域的 Windows Server 管理虚拟机。
    * 如果需要，请完成[创建 Windows SERVER VM 并将其加入托管域][create-join-windows-vm]的教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="install-dns-server-tools"></a>安装 DNS 服务器工具

若要创建和修改托管域中的 DNS 记录，需要安装 DNS 服务器工具。 这些工具可以作为一项功能安装在 Windows Server 中。 有关如何在 Windows 客户端上安装管理工具的详细信息，请参阅 install[远程服务器管理工具（RSAT）][install-rsat]。

1. 登录到管理 VM。 有关如何使用 Azure 门户进行连接的步骤，请参阅[连接到 Windows SERVER VM][connect-windows-server-vm]。
1. 如果在登录 VM 时服务器管理器默认情况下未打开，请选择“开始”菜单，然后选择“服务器管理器”。
1. 在“服务器管理器”窗口的“仪表板”窗格中，选择“添加角色和功能”。 
1. 在“添加角色和功能向导”的“准备工作”页上，选择“下一步”。
1. 对于“安装类型”，请保留选中“基于角色或基于功能的安装”选项，然后选择“下一步”。 
1. 在“服务器选择”页上，从服务器池中选择当前的 VM（例如 *myvm.aaddscontoso.com*），然后选择“下一步”。 
1. 在“服务器角色”页上，单击“下一步”。
1. 在“功能”页上，依次展开“远程服务器管理工具”节点和“角色管理工具”节点。   从角色管理工具列表中选择“DNS 服务器工具”功能。****

    ![从可用的角色管理工具列表中选择安装 DNS 服务器工具](./media/manage-dns/install-dns-tools.png)

1. 在“确认”页上选择“安装”。  安装 DNS 服务器工具可能需要一到两分钟的时间。
1. 功能安装完成后，选择“关闭”退出“添加角色和功能”向导。 

## <a name="open-the-dns-management-console-to-administer-dns"></a>打开 DNS 管理控制台来管理 DNS

安装 DNS 服务器工具后，你可以管理托管域上的 DNS 记录。

> [!NOTE]
> 若要管理托管域中的 DNS，你必须登录到作为*AAD DC 管理员*组成员的用户帐户。

1. 从 "开始" 屏幕中，选择 "**管理工具**"。 显示了可用管理工具的列表，其中包括上一节中安装的**DNS** 。 选择 " **dns** " 启动 dns 管理控制台。
1. 在 "**连接到 DNS 服务器**" 对话框中，选择 **"以下计算机**"，然后输入托管域的 DNS 域名，如*aaddscontoso.com*：

    ![在 DNS 控制台中连接到托管域](./media/manage-dns/connect-dns-server.png)

1. DNS 控制台连接到指定的托管域。 展开 "**正向查找区域**" 或 "**反向查找区域**"，以根据需要创建所需的 DNS 条目或编辑现有记录。

    ![DNS 控制台 - 管理域](./media/manage-dns/dns-manager.png)

> [!WARNING]
> 使用 DNS 服务器工具管理记录时，请确保不要删除或修改 Azure AD DS 使用的内置 DNS 记录。 内置 DNS 记录包括域 DNS 记录、名称服务器记录，以及其他用于 DC 定位的记录。 如果修改这些记录，虚拟网络上的域服务会中断。

## <a name="create-conditional-forwarders"></a>创建条件转发器

Azure AD DS DNS 区域应该只包含托管域本身的区域和记录。 不要在托管域中创建其他区域来解析其他 DNS 命名空间中的命名资源。 相反，请使用托管域中的条件转发器告知 DNS 服务器要转到的位置，以便为这些资源解析地址。

条件转发器是 DNS 服务器中的一个配置选项，可让你定义 DNS 域（如*contoso.com*），以将查询转发到。 DNS 查询将转发到为该域配置的 DNS，而不是尝试解析该域中的记录查询的本地 DNS 服务器。 此配置可确保返回正确的 DNS 记录，因为不会在托管域中创建具有重复记录的本地 DNS 区域来反映这些资源。

若要在托管域中创建条件转发器，请完成以下步骤：

1. 选择 DNS 区域，如*aaddscontoso.com*。
1. 选择 "**条件转发器**"，然后右键选择并选择 "**新建条件转发器 ...** "
1. 输入其他**Dns 域**（如*contoso.com*），然后输入该命名空间的 dns 服务器的 IP 地址，如以下示例中所示：

    ![添加和配置 DNS 服务器的条件转发器](./media/manage-dns/create-conditional-forwarder.png)

1. 选中 "**在 Active Directory 中存储此条件转发器" 的框，并按如下所示复制它**，然后为*此域中的所有 DNS 服务器*选择选项，如以下示例中所示：

    ![DNS 控制台-选择此域中的所有 DNS 服务器](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > 如果条件转发器存储在*林中*而不是*域*中，则条件转发器会失败。

1. 若要创建条件转发器，请选择 **"确定"**。

现在，连接到托管域的 Vm 的其他命名空间中的资源的名称解析现在应能正确解析。 条件转发器中配置的 DNS 域的查询会传递到相关的 DNS 服务器。

## <a name="next-steps"></a>后续步骤

有关管理 DNS 的详细信息，请参阅 [Technet 上的“DNS 工具”一文](https://technet.microsoft.com/library/cc753579.aspx)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
