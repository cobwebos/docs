---
title: 管理 Azure AD 域服务的 DNS |微软文档
description: 了解如何安装 DNS 服务器工具来管理 DNS 并为 Azure 活动目录域服务托管域创建条件转发器。
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2020
ms.author: iainfou
ms.openlocfilehash: f4bd3f75c3246cb11e88dbaae817eba8ac76b394
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603469"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服务托管域中管理 DNS 并创建条件转发器

在 Azure 活动目录域服务 （Azure AD DS） 中，关键组件是 DNS（域名解析）。 Azure AD DS 包括一个 DNS 服务器，该服务器为托管域提供名称解析。 此 DNS 服务器包括允许服务运行的关键组件的内置 DNS 记录和更新。

在运行自己的应用程序和服务时，您可能需要为未加入域的计算机创建 DNS 记录，为负载均衡器配置虚拟 IP 地址，或设置外部 DNS 转发器。 属于*AAD DC 管理员*组的用户在 Azure AD DS 托管域上被授予 DNS 管理权限，并可以创建和编辑自定义 DNS 记录。

在混合环境中，在其他 DNS 命名空间（如本地 AD DS 环境）中配置的 DNS 区域和记录不会同步到 Azure AD DS。 要解决其他 DNS 命名空间中的命名资源，请创建并使用指向环境中现有 DNS 服务器的条件转发器。

本文介绍如何安装 DNS 服务器工具，然后使用 DNS 控制台在 Azure AD DS 中管理记录和创建条件转发器。

## <a name="before-you-begin"></a>在开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成创建[和配置 Azure 活动目录域服务实例][create-azure-ad-ds-instance]的教程。
* 从 Azure AD DS 虚拟网络连接到托管其他 DNS 命名空间的位置。
    * 此连接可以通过[Azure 快速路由][expressroute]或[Azure VPN 网关][vpn-gateway]连接提供。
* 加入到 Azure AD DS 托管域的 Windows 服务器管理 VM。
    * 如果需要，请完成[创建 Windows 服务器 VM 的教程并将其加入托管域][create-join-windows-vm]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。 

## <a name="install-dns-server-tools"></a>安装 DNS 服务器工具

要在 Azure AD DS 中创建和修改 DNS 记录，需要安装 DNS 服务器工具。 这些工具可以作为功能安装在 Windows 服务器中。 有关如何在 Windows 客户端上安装管理工具的详细信息，请参阅安装[远程服务器管理工具 （RSAT）。][install-rsat]

1. 登录到管理 VM。 有关如何使用 Azure 门户进行连接的步骤，请参阅[连接到 Windows 服务器 VM][connect-windows-server-vm]。
1. 如果在登录 VM 时服务器管理器  默认情况下未打开，请选择“开始”  菜单，然后选择“服务器管理器”  。
1. 在“服务器管理器”窗口的“仪表板”窗格中，选择“添加角色和功能”。   
1. 在“添加角色和功能向导”的“准备工作”页上，选择“下一步”。   
1. 对于“安装类型”，请保留选中“基于角色或基于功能的安装”选项，然后选择“下一步”。   
1. 在“服务器选择”页上，从服务器池中选择当前的 VM（例如 *myvm.aaddscontoso.com*），然后选择“下一步”。  
1. 在“服务器角色”  页上，单击“下一步”  。
1. 在“功能”页上，依次展开“远程服务器管理工具”节点和“角色管理工具”节点。    从角色管理工具列表中选择“DNS 服务器工具”功能。****

    ![选择从可用角色管理工具列表中安装 DNS 服务器工具](./media/manage-dns/install-dns-tools.png)

1. 在“确认”页上选择“安装”。   安装组策略管理工具可能需要一两分钟。
1. 功能安装完成后，选择“关闭”退出“添加角色和功能”向导。  

## <a name="open-the-dns-management-console-to-administer-dns"></a>打开 DNS 管理控制台以管理 DNS

安装 DNS 服务器工具后，可以在 Azure AD DS 托管域上管理 DNS 记录。

> [!NOTE]
> 若要在 Azure AD DS 托管域中管理 DNS，必须登录到*属于 AAD DC 管理员*组的用户帐户。

1. 在"开始"屏幕中，选择 **"管理工具**"。 将显示可用管理工具的列表，包括上一节中安装的**DNS。** 选择**DNS**以启动 DNS 管理控制台。
1. 在 **"连接到 DNS 服务器"** 对话框**中，选择以下计算机**，然后输入托管域的 DNS 域名，如*aaddscontoso.com*：

    ![连接到 DNS 控制台中的 Azure AD DS 托管域](./media/manage-dns/connect-dns-server.png)

1. DNS 控制台连接到指定的 Azure AD DS 托管域。 展开**前瞻查找区域**或**反向查找区域**以创建所需的 DNS 条目或根据需要编辑现有记录。

    ![DNS 控制台 - 管理域](./media/manage-dns/dns-manager.png)

> [!WARNING]
> 使用 DNS 服务器工具管理记录时，请确保不要删除或修改 Azure AD DS 使用的内置 DNS 记录。 内置 DNS 记录包括域 DNS 记录、名称服务器记录，以及其他用于 DC 定位的记录。 如果修改这些记录，虚拟网络上的域服务会中断。

## <a name="create-conditional-forwarders"></a>创建条件转发器

Azure AD DS DNS 区域应仅包含托管域本身的区域和记录。 不要在 Azure AD DS 中创建其他区域来解决其他 DNS 命名空间中的命名资源。 相反，使用 Azure AD DS 托管域中的条件转发器告诉 DNS 服务器要去哪里解析这些资源的地址。

条件转发器是 DNS 服务器中的配置选项，允许您定义 DNS 域（如*contoso.com）* 以将查询转发到。 DNS 查询将转发到该域的配置 DNS，而不是尝试解析该域中记录的查询的本地 DNS 服务器。 此配置可确保返回正确的 DNS 记录，因为您不会在 Azure AD DS 托管域中创建具有重复记录的本地 DNS 区域以反映这些资源。

要在 Azure AD DS 托管域中创建条件转发器，请完成以下步骤：

1. 选择 Azure AD DS DNS 区域，如*aaddscontoso.com*.vb
1. 选择**条件转发器**，然后右选择并选择 **"新条件转发器..."**
1. 输入其他**DNS 域**（如*contoso.com*），然后输入该命名空间的 DNS 服务器的 IP 地址，如以下示例所示：

    ![为 DNS 服务器添加和配置条件转发器](./media/manage-dns/create-conditional-forwarder.png)

1. 选中"在**Active Directory 中存储此条件转发器"复选框，并将其复制如下**，然后选择*此域中所有 DNS 服务器*的选项，如以下示例所示：

    ![DNS 控制台 - 管理域](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > 如果条件转发器存储在*林*中而不是*域*中，则条件转发器将失败。

1. 要创建条件转发器，请选择 **"确定**"。

连接到 Azure AD DS 托管域的 VM 中其他命名空间中资源的名称解析现在应该正确解析。 条件转发器中配置的 DNS 域的查询将传递到相关的 DNS 服务器。

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
