---
title: Azure AD 域服务中的安全远程 VM 访问 |Microsoft Docs
description: 了解如何使用网络策略服务器（NPS）和 Azure 多重身份验证通过 Azure Active Directory 域服务托管域中的远程桌面服务部署来保护对 Vm 的远程访问。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: a17f27831dd0a674c1d55cde6974aba5e1bfcfc3
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105720"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>对 Azure Active Directory 域服务中的虚拟机进行安全远程访问

若要保护对 Azure Active Directory 域服务（Azure AD DS）托管域中运行的虚拟机（Vm）的远程访问，你可以使用远程桌面服务（RDS）和网络策略服务器（NPS）。 Azure AD DS 在用户通过 RDS 环境请求访问时对用户进行身份验证。 为了提高安全性，你可以集成 Azure 多重身份验证，以便在登录事件期间提供额外的身份验证提示。 Azure 多重身份验证使用 NPS 扩展来提供此功能。

> [!IMPORTANT]
> 安全连接到 Azure AD DS 托管域中的 Vm 的建议方法是使用 Azure 堡垒，这是在虚拟网络中预配的完全平台托管的 PaaS 服务。 堡垒主机通过 SSL 直接在 Azure 门户中为 Vm 提供安全且无缝的远程桌面协议（RDP）连接。 通过堡垒主机连接时，Vm 不需要公共 IP 地址，并且无需使用网络安全组来公开对 TCP 端口3389上的 RDP 的访问。
>
> 我们强烈建议你在支持它的所有区域使用 Azure 堡垒。 在没有 Azure 堡垒可用性的区域中，请按照本文中所述的步骤操作，直到 Azure 堡垒可用。 请注意将公共 IP 地址分配给加入到 Azure AD DS 的 Vm，其中允许所有传入的 RDP 流量。
>
> 有关详细信息，请参阅[什么是 Azure 堡垒？][bastion-overview]。

本文介绍如何在 Azure AD DS 中配置 RDS，并选择性地使用 Azure 多重身份验证 NPS 扩展。

![远程桌面服务（RDS）概述](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>先决条件

若要完成本文，需准备好以下资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 在 Azure Active Directory 域服务虚拟网络中创建的*工作负荷*子网。
    * 如果需要，请[为 Azure Active Directory 域服务托管域配置虚拟网络][configure-azureadds-vnet]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。 

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>部署和配置远程桌面环境

若要开始，请至少创建两个运行 Windows Server 2016 或 Windows Server 2019 的 Azure Vm。 为了获得远程桌面（RD）环境的冗余性和高可用性，你可以在以后对其他主机进行添加和负载平衡。

建议的 RDS 部署包含以下两个 Vm：

* *RDGVM01* -运行 RD 连接代理 SERVER、RD Web 访问服务器和 RD 网关服务器。
* *RDSHVM01* -运行 RD 会话主机服务器。

请确保将 Vm 部署到 Azure AD DS 虚拟网络的*工作负荷*子网，然后将 vm 加入 Azure AD ds 托管域。 有关详细信息，请参阅如何[创建 Windows SERVER VM 并将其加入到 AZURE AD DS 托管域][tutorial-create-join-vm]。

RD 环境部署包含多个步骤。 可以使用现有的 RD 部署指南，无需任何特定更改即可在 Azure AD DS 托管域中使用：

1. 使用属于*AZURE AD DC Administrators*组的帐户（如*contosoadmin*）登录为 RD 环境创建的 vm。
1. 若要创建和配置 RDS，请使用现有的[远程桌面环境部署指南][deploy-remote-desktop]。 根据需要将 RD 服务器组件分散在 Azure Vm 中。
    * 特定于 Azure AD DS-配置 RD 授权时，请将其设置为 "**每设备**" 模式，而不是 "部署指南"**中所述**。
1. 如果要使用 web 浏览器提供访问权限，请[为用户设置远程桌面 web 客户端][rd-web-client]。

将 RD 部署到 Azure AD DS 托管域后，你可以像使用本地 AD DS 域一样管理和使用该服务。

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>部署和配置 NPS 和 Azure MFA NPS 扩展

如果要提高用户登录体验的安全性，可以选择将 RD 环境与 Azure 多重身份验证集成。 使用此配置，用户会在登录过程中收到额外的提示，以确认其身份。

为了提供此功能，将在你的环境中安装附加网络策略服务器（NPS）以及 Azure 多重身份验证 NPS 扩展。 此扩展与 Azure AD 集成，以请求并返回多重身份验证提示的状态。

必须注册用户[才能使用 Azure 多重身份验证][user-mfa-registration]，这可能需要额外的 Azure AD 许可证。

若要将 Azure 多重身份验证集成到 Azure AD DS 远程桌面环境中，请创建一个 NPS 服务器并安装扩展：

1. 创建连接到 Azure AD DS 虚拟网络中的*工作负荷*子网的附加 Windows Server 2016 或 2019 VM，如*NPSVM01*。 将 VM 加入到 Azure AD DS 托管域。
1. 以作为*AZURE AD DC Administrators*组的一部分的帐户（如*contosoadmin*）登录到 NPS VM。
1. 在**服务器管理器**中，选择 "**添加角色和功能**"，然后安装 "*网络策略和访问服务*" 角色。
1. 使用现有的操作方法文章来[安装和配置 AZURE MFA NPS 扩展][nps-extension]。

安装了 NPS 服务器和 Azure 多重身份验证 NPS 扩展后，请完成下一部分，将其配置为用于 RD 环境。

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>集成远程桌面网关和 Azure 多重身份验证

若要集成 Azure 多重身份验证 NPS 扩展，请使用现有的操作方法文章，使用[网络策略服务器（NPS）扩展和 Azure AD 集成你的远程桌面网关基础结构][azure-mfa-nps-integration]。

与 Azure AD DS 托管域集成需要以下附加配置选项：

1. 请勿[在 Active Directory 中注册 NPS 服务器][register-nps-ad]。 此步骤在 Azure AD DS 托管域中失败。
1. 在[步骤4中，若要配置网络策略][create-nps-policy]，还请选中复选框以**忽略用户帐户的拨入属性**。
1. 如果为 NPS 服务器和 Azure 多重身份验证 NPS 扩展使用 Windows Server 2019，请运行以下命令来更新安全通道，使 NPS 服务器能够正确通信：

    ```powershell
    sc sidtype IAS unrestricted
    ```

现在，在用户登录时，系统会提示用户提供附加身份验证因素，例如文本消息或 Microsoft Authenticator 应用程序中的提示。

## <a name="next-steps"></a>后续步骤

有关提高部署复原能力的详细信息，请参阅[远程桌面服务-高可用性][rds-high-availability]。

有关保护用户登录的详细信息，请参阅[它的工作原理： Azure 多重身份验证][concepts-mfa]。

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
