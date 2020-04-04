---
title: 在 Azure AD 域服务中保护远程 VM 访问 |微软文档
description: 了解如何使用网络策略服务器 （NPS） 和 Azure 多重身份验证在 Azure 活动目录域服务托管域中的远程桌面服务部署保护对 VM 的远程访问。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8bc36dfdf3010b2bde485228f6ee110b0b826d31
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654756"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>保护对 Azure 活动目录域服务中的虚拟机的远程访问

要保护对在 Azure 活动目录域服务 （Azure AD DS） 托管域中运行的虚拟机 （VM） 的远程访问，可以使用远程桌面服务 （RDS） 和网络策略服务器 （NPS）。 Azure AD DS 在用户请求通过 RDS 环境进行访问时对其进行身份验证。 为了增强安全性，可以集成 Azure 多重身份验证，在登录事件期间提供其他身份验证提示。 Azure 多重身份验证使用 NPS 的扩展来提供此功能。

> [!IMPORTANT]
> 在 Azure AD DS 托管域中安全地连接到 VM 的推荐方法是使用 Azure Bastion，这是一种在虚拟网络内预配的完全平台托管的 PaaS 服务。 堡垒主机通过 SSL 直接在 Azure 门户中提供与 VM 的安全无缝远程桌面协议 （RDP） 连接。 通过堡垒主机进行连接时，VM 不需要公共 IP 地址，并且不需要使用网络安全组来公开对 TCP 端口 3389 上的 RDP 的访问。
>
> 我们强烈建议您在支持 Azure 堡垒的所有区域中使用 Azure 堡垒。 在没有 Azure 堡垒可用性的区域中，请按照本文中详细介绍的步骤操作，直到 Azure 堡垒可用。 注意将公共 IP 地址分配给连接到 Azure AD DS 的 VM，允许所有传入 RDP 流量。
>
> 有关详细信息，请参阅什么是[Azure 堡垒？][bastion-overview]

本文介绍如何在 Azure AD DS 中配置 RDS，并选择性地使用 Azure 多重身份验证 NPS 扩展。

![远程桌面服务 （RDS） 概述](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>先决条件

要完成本文，您需要以下资源：

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 在 Azure 活动目录域服务虚拟网络中创建的*工作负载*子网。
    * 如果需要，[为 Azure 活动目录域服务托管域配置虚拟网络][configure-azureadds-vnet]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。**

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>部署和配置远程桌面环境

要开始，请至少创建两个运行 Windows Server 2016 或 Windows Server 2019 的 Azure VM。 对于远程桌面 （RD） 环境的冗余性和高可用性，您可以稍后添加和负载平衡其他主机。

建议的 RDS 部署包括以下两个 VM：

* *RDGVM01* - 运行 RD 连接代理服务器、RD Web 访问服务器和 RD 网关服务器。
* *RDSHVM01* - 运行 RD 会话主机服务器。

确保 VM 已部署到 Azure AD DS 虚拟网络*的工作负载*子网，然后将 VM 加入 Azure AD DS 托管域。 有关详细信息，请参阅如何[创建 Windows 服务器 VM 并将其加入到 Azure AD DS 托管域][tutorial-create-join-vm]。

RD 环境部署包含许多步骤。 无需在 Azure AD DS 托管域中使用任何特定更改即可使用现有的 RD 部署指南：

1. 使用属于*Azure AD DC 管理员*组的帐户（如*contosoadmin）* 登录到为 RD 环境创建的 VM。
1. 要创建和配置 RDS，请使用现有的[远程桌面环境部署指南][deploy-remote-desktop]。 根据需要在 Azure VM 中分发 RD 服务器组件。
1. 如果要使用 Web 浏览器提供访问权限，请[为用户设置远程桌面 Web 客户端][rd-web-client]。

将 RD 部署到 Azure AD DS 托管域中，您可以像使用本地 AD DS 域一样管理和使用服务。

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>部署和配置 NPS 和 Azure MFA NPS 扩展

如果要提高用户登录体验的安全性，可以选择将 RD 环境与 Azure 多重身份验证集成。 使用此配置，用户在登录期间会收到一个额外的提示，以确认其身份。

要提供此功能，在您的环境中安装一个额外的网络策略服务器 （NPS） 以及 Azure 多重身份验证 NPS 扩展。 此扩展与 Azure AD 集成，以请求并返回多重身份验证提示的状态。

必须注册用户[才能使用 Azure 多重身份验证][user-mfa-registration]，这可能需要其他 Azure AD 许可证。

要将 Azure 多重身份验证集成到 Azure AD DS 远程桌面环境中，请创建 NPS 服务器并安装扩展：

1. 创建连接到 Azure AD DS 虚拟网络中*的工作负载*子网的其他 Windows 服务器 2016 或 2019 VM，例如*NPSVM01。* 将 VM 加入 Azure AD DS 托管域。
1. 作为*Azure AD DC 管理员*组（如*contosoadmin）* 的一部分的帐户登录到 NPS VM。
1. 从**服务器管理器**中，选择 **"添加角色和功能**"，然后安装*网络策略和访问服务*角色。
1. 使用现有的"执行"文章[安装和配置 Azure MFA NPS 扩展][nps-extension]。

安装 NPS 服务器和 Azure 多重身份验证 NPS 扩展后，完成下一节以将其配置为与 RD 环境一起使用。

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>集成远程桌面网关和 Azure 多重身份验证

要集成 Azure 多重身份验证 NPS 扩展，请使用现有的操作文章[使用网络策略服务器 （NPS） 扩展和 Azure AD 集成远程桌面网关基础结构][azure-mfa-nps-integration]。

与 Azure AD DS 托管域集成需要以下其他配置选项：

1. 不要在[活动目录中注册 NPS 服务器][register-nps-ad]。 此步骤在 Azure AD DS 托管域中失败。
1. 在[步骤 4 中配置网络策略][create-nps-policy]，还选中"**忽略用户帐户拨入属性"复选框**。
1. 如果对 NPS 服务器使用 Windows Server 2019 和 Azure 多重身份验证 NPS 扩展，请运行以下命令以更新安全通道以允许 NPS 服务器正确通信：

    ```powershell
    sc sidtype IAS unrestricted
    ```

现在，用户登录时会提示他们输入其他身份验证因素，例如 Microsoft 身份验证器应用中的短信或提示。

## <a name="next-steps"></a>后续步骤

有关提高部署恢复能力的详细信息，请参阅[远程桌面服务 - 高可用性][rds-high-availability]。

有关保护用户登录的详细信息，请参阅[其工作原理：Azure 多重身份验证][concepts-mfa]。

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
