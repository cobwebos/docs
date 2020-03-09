---
title: 教程 - 为 Azure Active Directory 域服务创建管理 VM | Microsoft Docs
description: 本教程介绍如何创建并配置一个用来管理 Azure Active Directory 域服务实例的 Windows 虚拟机。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: f422d1dd6c76d78448ae4fb1012a5dae8d6108b3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376575"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>教程：创建用于配置和管理 Azure Active Directory 域服务托管域的管理 VM

Azure Active Directory 域服务 (AD DS) 提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、LDAP 和 Kerberos/NTLM 身份验证。 可以使用对本地 Active Directory 域服务域所用的相同远程服务器管理工具 (RSAT) 来管理此托管域。 由于 Azure AD DS 是一种托管服务，因此有些管理任务无法执行，例如，使用远程桌面协议 (RDP) 连接到域控制器。

本教程介绍如何在 Azure 中创建 Windows Server VM，并安装所需的工具来管理 Azure AD DS 托管域。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 了解可在 Azure AD DS 托管域中执行的管理任务
> * 在 Windows Server VM 上安装 Active Directory 管理工具
> * 使用 Active Directory 管理中心执行常见任务

如果你没有 Azure 订阅，可以在开始之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参阅第一个教程[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 托管域的 Windows Server VM。
    * 如果需要，请参阅上一教程[创建 Windows Server VM 并将其加入到托管域][create-join-windows-vm]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。
* 部署在 Azure AD DS 虚拟网络中的 Azure 堡垒主机。
    * 如果需要，请[创建一个 Azure 堡垒主机][azure-bastion]。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，你将使用 Azure 门户来创建并配置一个管理 VM。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>可在 Azure AD DS 中执行的管理任务

Azure AD DS 为用户、应用程序和服务提供一个托管域供其使用。 这种方法改变了可以执行的某些管理任务，以及你在托管域中拥有的特权。 这些任务和权限可能与普通本地 Active Directory 域服务环境中的体验不同。 此外，无法使用远程桌面连接到 Azure AD DS 托管域上的域控制器。

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>可以在 Azure AD DS 托管域上执行的管理任务

“AAD DC 管理员”组的成员被授予 Azure AD DS 托管域上的相应特权，可执行如下所述的任务：

* 配置托管域中“AADDC 计算机”和“AADDC 用户”容器的内置组策略对象 (GPO)。
* 管理托管域上的 DNS。
* 创建和管理托管域上的自定义组织单位 (OU)。
* 获取对已加入托管域的计算机的管理访问权限。

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>你在 Azure AD DS 托管域上不拥有的管理特权

Azure AD DS 托管域处于锁定状态，因此你不拥有在该域上执行某些管理任务的特权。 下面是无法执行的任务示例：

* 扩展托管域的架构。
* 使用远程桌面连接到托管域的域控制器。
* 将域控制器添加到托管域。
* 你不拥有托管域的“域管理员”或“企业管理员”特权。

## <a name="sign-in-to-the-windows-server-vm"></a>登录到 Windows Server VM

上一篇教程已创建一个 Windows Server VM 并将其加入 Azure AD DS 托管域。 现在，让我们使用该 VM 来安装管理工具。 如果需要，[请遵循该教程中的步骤创建 Windows Server VM 并将其加入托管域][create-join-windows-vm]。

> [!NOTE]
> 在本教程中，你将使用 Azure 中已加入 Azure AD DS 托管域的 Windows Server VM。 也可以使用已加入托管域的 Windows 客户端，例如 Windows 10。
>
> 有关如何在 Windows 客户端上安装管理工具的详细信息，请参阅[安装远程服务器管理工具 (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

若要开始，请按如下所述连接到该 Windows Server VM：

1. 在 Azure 门户的左侧选择“资源组”。 选择在其中创建了该 VM 的资源组（例如 *myResourceGroup*），然后选择该 VM（例如 *myVM*）。
1. 在 VM 的 "**概述**" 窗格中，选择 "**连接**"，然后选择 "**堡垒**"。

    ![在 Azure 门户中使用堡垒连接到 Windows 虚拟机](./media/join-windows-vm/connect-to-vm.png)

1. 输入 VM 的凭据，并选择 "**连接**"。

   ![在 Azure 门户中通过堡垒主机连接](./media/join-windows-vm/connect-to-bastion.png)

如果需要，允许 web 浏览器打开要显示的堡垒连接的弹出窗口。 连接到 VM 需要几秒钟的时间。

## <a name="install-active-directory-administrative-tools"></a>安装 Active Directory 管理工具

使用在本地 AD DS 环境中所用相同的管理工具（例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell）管理 Azure AD DS 托管域。 可将这些工具作为远程服务器管理工具 (RSAT) 功能的一部分安装到 Windows Server 和客户端计算机上。 然后，“AAD DC 管理员”组的成员可以在已加入托管域的计算机中使用这些 AD 管理工具来远程管理 Azure AD DS 托管域。

若要在已加入域的 VM 上安装 Active Directory 管理工具，请完成以下步骤：

1. 如果在登录 VM 时服务器管理器默认情况下未打开，请选择“开始”菜单，然后选择“服务器管理器”。
1. 在“服务器管理器”窗口的“仪表板”窗格中，选择“添加角色和功能”。
1. 在“添加角色和功能向导”的“准备工作”页上，选择“下一步”。
1. 对于“安装类型”，请保留选中“基于角色或基于功能的安装”选项，然后选择“下一步”。
1. 在 "**服务器选择**" 页上，从服务器池中选择当前 VM （如*myvm.aaddscontoso.com*），然后选择 "**下一步**"。
1. 在 **“服务器角色”** 页上，单击 **“下一步”** 。
1. 在“功能”页上，依次展开“远程服务器管理工具”节点和“角色管理工具”节点。

    从角色管理工具列表中选择“AD DS 和 AD LDS 工具”功能，然后选择“下一步”。

    ![从“功能”页安装“AD DS 和 AD LDS 工具”](./media/tutorial-create-management-vm/install-features.png)

1. 在“确认”页上选择“安装”。 安装管理工具可能需要一两分钟时间。
1. 功能安装完成后，选择“关闭”退出“添加角色和功能”向导。

## <a name="use-active-directory-administrative-tools"></a>使用 Active Directory 管理工具

安装管理工具后，让我们了解如何使用它们来管理 Azure AD DS 托管域。 请确保已使用属于“AAD DC 管理员”组成员的用户帐户登录到 VM。

1. 从“开始”菜单中选择“Windows 管理工具”。 此时会列出在上一步骤中安装的 AD 管理工具。

    ![在服务器上安装的管理工具列表](./media/tutorial-create-management-vm/list-admin-tools.png)

1. 选择“Active Directory 管理中心”。
1. 若要浏览 Azure AD DS 托管域，请在左窗格中选择域名，例如*aaddscontoso.com*。 列表顶部显示了名为“AADDC 计算机”和“AADDC 用户”的两个容器。

    ![列出 Azure AD DS 托管域的可用容器部分](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. 若要查看属于 Azure AD DS 托管域的用户和组，请选择“AADDC 用户”容器。 来自 Azure AD 租户的用户帐户和组将列在此容器中。

    在以下示例输出中，名为 Contoso Admin 的用户帐户和“AAD DC 管理员”组已在此容器中显示。

    ![在 Active Directory 管理中心查看 Azure AD DS 域用户列表](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. 若要查看已加入 Azure AD DS 托管域的计算机，请选择“AADDC 计算机”容器。 此时会列出当前虚拟机的条目，例如 *myVM*。 已加入 Azure AD DS 托管域的所有计算机的计算机帐户存储在此“AADDC 计算机”容器中。

可以执行常见的 Active Directory 管理中心操作，例如重置用户帐户密码，或管理组成员身份。 这些操作仅适用于直接在 Azure AD DS 托管域中创建的用户和组。 标识信息仅从 Azure AD 同步到 Azure AD DS。 没有从 Azure AD DS 回写到 Azure AD 的操作。 无法更改从 Azure AD 同步的用户的密码或托管组成员身份，并且无法将这些更改同步回来。

还可以使用作为管理工具的一部分安装的“适用于 Windows PowerShell 的 Active Directory 模块”来管理 Azure AD DS 托管域中的常见操作。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 了解可在 Azure AD DS 托管域中执行的管理任务
> * 在 Windows Server VM 上安装 Active Directory 管理工具
> * 使用 Active Directory 管理中心执行常见任务

若要与 Azure AD DS 托管域安全交互，请启用安全的轻型目录访问协议 (LDAPS)。

> [!div class="nextstepaction"]
> [为托管域配置安全 LDAP](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
