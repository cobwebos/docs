---
title: 在 Azure AD 域服务中保护远程 VM 访问 | Microsoft Docs
description: 了解如何在 Azure Active Directory 域服务托管域中通过对远程桌面服务部署使用网络策略服务器 (NPS) 和 Azure 多重身份验证来保护对 VM 的远程访问。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 462e0ff8ef8baadf40ba81ed2ae137d8904fbf7a
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722511"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>在 Azure Active Directory 域服务中保护对虚拟机的远程访问

若要保护对 Azure Active Directory 域服务 (Azure AD DS) 托管域中运行的虚拟机 (VM) 的远程访问，可以使用远程桌面服务 (RDS) 和网络策略服务器 (NPS)。 Azure AD DS 在用户请求通过 RDS 环境进行访问时对用户进行身份验证。 为了提高安全性，你可以集成 Azure 多重身份验证，以便在发生登录事件期间提供额外的身份验证提示。 Azure 多重身份验证使用 NPS 的一个扩展来提供此功能。

> [!IMPORTANT]
> 若要安全地连接到 Azure AD DS 托管域中的 VM，建议使用 Azure Bastion，这是在虚拟网络中预配的一项完全由平台托管的 PaaS 服务。 堡垒主机直接在 Azure 门户中通过 SSL 提供与 VM 的安全无缝远程桌面协议 (RDP) 连接。 通过堡垒主机进行连接时，VM 不需要公共 IP 地址，你无需使用网络安全组在 TCP 端口 3389 上公开对 RDP 的访问。
>
> 强烈建议你在支持 Azure Bastion 的所有区域中使用它。 在不可使用 Azure Bastion 的区域中，请按本文中详述的步骤操作，直到 Azure Bastion 可用。 向已加入允许所有传入 RDP 流量的 Azure AD DS 的 VM 分配公共 IP 地址时一定要谨慎。
>
> 有关详细信息，请参阅[什么是 Azure Bastion？][bastion-overview]。

本文介绍了如何在 Azure AD DS 中配置 RDS，以及如何选择使用 Azure 多重身份验证 NPS 扩展。

![远程桌面服务 (RDS) 概述](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>先决条件

若要完成本文，需准备好以下资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 在你的 Azure Active Directory 域服务虚拟网络中创建的“工作负载”子网。
    * 如果需要，请[为 Azure Active Directory 域服务托管域配置虚拟网络][configure-azureadds-vnet]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>部署和配置远程桌面环境

若要开始，请至少创建两个运行 Windows Server 2016 或 Windows Server 2019 的 Azure VM。 为了实现远程桌面 (RD) 环境的冗余性和高可用性，你可以在以后添加其他主机并对其进行负载均衡。

建议的 RDS 部署包含以下两个 VM：

* RDGVM01 - 运行 RD 连接代理服务器、RD Web 访问服务器和 RD 网关服务器。
* RDSHVM01 - 运行 RD 会话主机服务器。

请确保将 VM 部署到 Azure AD DS 虚拟网络的“工作负载”子网中，然后将 VM 加入托管域。 有关详细信息，请参阅[如何创建 Windows Server VM 并将其加入托管域][tutorial-create-join-vm]。

RD 环境部署包含许多步骤。 可以使用现有的 RD 部署指南，不需要进行任何特定变更便可在托管域中使用：

1. 使用“Azure AD DC 管理员”组中的帐户（例如 contosoadmin）登录到为 RD 环境创建的 VM。
1. 若要创建和配置 RDS，请使用现有的[远程桌面环境部署指南][deploy-remote-desktop]。 根据需要将 RD 服务器组件分布到各个 Azure VM。
    * 特定于 Azure AD DS - 配置 RD 许可时，请将其设置为“每设备”模式而不是“每用户”模式，如部署指南中所述。
1. 如果要使用 Web 浏览器提供访问，请[为用户设置远程桌面 Web 客户端][rd-web-client]。

将 RD 部署到托管域后，你可以像使用本地 AD DS 域一样管理和使用该服务。

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>部署和配置 NPS 和 Azure MFA NPS 扩展

如果要提高用户登录体验的安全性，可以选择将 RD 环境与 Azure 多重身份验证集成。 使用此配置，用户会在登录过程中收到额外的提示，以确认其身份。

为了提供此功能，会随 Azure 多重身份验证 NPS 扩展一起在你的环境中安装 (NPS) 的其他网络策略服务器。 此扩展与 Azure AD 集成，以请求并返回多重身份验证提示的状态。

必须注册用户 [才能使用 Azure 多重身份验证][user-mfa-registration]，这可能需要额外的 Azure AD 许可证。

若要将 Azure 多重身份验证集成到 Azure AD DS 远程桌面环境中，请创建一个 NPS 服务器并安装扩展：

1. 创建连接到 Azure AD DS 虚拟网络中的*工作负荷*子网的附加 Windows Server 2016 或 2019 VM，如*NPSVM01*。 将 VM 加入托管域。
1. 以作为 *AZURE AD DC Administrators* 组的一部分的帐户（如 *contosoadmin*）登录到 NPS VM。
1. 在 **服务器管理器**中，选择 " **添加角色和功能**"，然后安装 " *网络策略和访问服务* " 角色。
1. 使用现有的操作方法文章来 [安装和配置 AZURE MFA NPS 扩展][nps-extension]。

安装了 NPS 服务器和 Azure 多重身份验证 NPS 扩展后，请完成下一部分，将其配置为用于 RD 环境。

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>集成远程桌面网关和 Azure 多重身份验证

若要集成 Azure 多重身份验证 NPS 扩展，请使用现有的操作方法文章，将 [远程桌面网关基础结构与使用网络策略服务器 (NPS) 扩展和 Azure AD 集成][azure-mfa-nps-integration]。

与托管域集成需要以下附加配置选项：

1. 请勿 [在 Active Directory 中注册 NPS 服务器][register-nps-ad]。 此步骤在托管域中失败。
1. 在 [步骤4中，若要配置网络策略][create-nps-policy]，还请选中复选框以 **忽略用户帐户的拨入属性**。
1. 如果为 NPS 服务器和 Azure 多重身份验证 NPS 扩展使用 Windows Server 2019，请运行以下命令来更新安全通道，使 NPS 服务器能够正确通信：

    ```powershell
    sc sidtype IAS unrestricted
    ```

现在，在用户登录时，系统会提示用户提供附加身份验证因素，例如文本消息或 Microsoft Authenticator 应用程序中的提示。

## <a name="next-steps"></a>后续步骤

若要详细了解如何提高部署复原能力，请参阅[远程桌面服务 - 高可用性][rds-high-availability]。

若要详细了解如何保护用户登录，请参阅[工作原理：Azure 多重身份验证][concepts-mfa]。

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
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability