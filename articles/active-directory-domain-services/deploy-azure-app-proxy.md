---
title: 为 Azure AD 域服务部署 Azure AD 应用程序代理 |Microsoft Docs
description: 了解如何通过在 Azure Active Directory 域服务托管域中部署和配置 Azure Active Directory 应用程序代理提供对远程辅助角色的内部应用程序的安全访问
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c0fcb8c2c5f9afa7fabe2ffa63a715ec24aa4a26
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720499"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>部署 Azure AD 应用程序代理以安全访问 Azure AD 域服务托管域中的内部应用程序

通过 Azure AD 域服务（Azure AD DS），可以将本地运行的旧应用程序直接迁移到 Azure。 然后 Azure Active Directory （AD）应用程序代理通过安全地发布 Azure AD DS 托管域的内部应用程序部分来帮助你支持远程辅助角色，以便可以通过 internet 访问它们。

如果你不熟悉 Azure AD 应用程序代理并想要了解详细信息，请参阅[如何提供对内部应用程序的安全远程访问](../active-directory/manage-apps/application-proxy.md)。

本文介绍如何创建和配置 Azure AD 应用程序代理连接器，以提供对 Azure AD DS 托管域中的应用程序的安全访问。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>开始之前

若要完成本文，需要具备以下资源和权限：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
    * 使用 Azure AD 应用程序代理需要**Azure AD Premium 许可证**。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。

## <a name="create-a-domain-joined-windows-vm"></a>创建已加入域的 Windows VM

若要将流量路由到环境中运行的应用程序，请安装 Azure AD 应用程序代理连接器组件。 此 Azure AD 应用程序代理连接器必须安装在已加入到 Azure AD DS 托管域的 Windows Server 虚拟机（VM）上。 对于某些应用程序，你可以部署多个服务器，每个服务器都安装了连接器。 此部署选项提供更好的可用性，并可帮助处理较重的身份验证负载。

运行 Azure AD 应用程序代理连接器的 VM 必须位于已启用 Azure AD DS 的相同或对等互连虚拟网络上。 然后，承载使用应用程序代理发布的应用程序的 Vm 还必须部署在同一 Azure 虚拟网络上。

若要为 Azure AD 应用程序代理连接器创建 VM，请完成以下步骤：

1. [创建自定义 OU](create-ou.md)。 你可以将管理此自定义 OU 的权限委派给 Azure AD DS 托管域中的用户。 用于 Azure AD 应用程序代理并运行应用程序的 Vm 必须是自定义 OU 的一部分，而不是默认的*AAD DC 计算机*OU。
1. 域-将运行 Azure AD 应用程序代理连接器的虚拟机以及运行应用程序的[虚拟机连接][create-join-windows-vm]到 Azure AD DS 托管域。 在上一步的自定义 OU 中创建这些计算机帐户。

## <a name="download-the-azure-ad-application-proxy-connector"></a>下载 Azure AD 应用程序代理连接器

执行以下步骤以下载 Azure AD 应用程序代理连接器。 在下一部分中，将下载的安装文件复制到应用代理 VM。

1. 使用在 Azure AD 中具有*企业管理员*权限的用户帐户登录到[Azure 门户](https://portal.azure.com)。
1. 搜索并选择门户顶部**Azure Active Directory** ，然后选择 "**企业应用程序**"。
1. 从左侧菜单中选择 "**应用程序代理**"。 若要创建第一个连接器并启用应用程序代理，请选择 "**下载连接器**" 链接。
1. 在下载页上，接受许可条款和隐私协议，然后选择 "**接受条款" & 下载**。

    ![下载 Azure AD 应用代理连接器](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>安装并注册 Azure AD 应用程序代理连接器

使用可用作 Azure AD 应用程序代理连接器的 VM 时，请复制并运行从 Azure 门户下载的安装程序文件。

1. 将 Azure AD 应用程序代理连接器安装程序文件复制到 VM。
1. 运行安装程序文件，如*aadapplicationproxyconnectorinstaller.exe*。 接受软件许可条款。
1. 在安装过程中，系统会提示在 Azure AD 目录中向应用程序代理注册连接器。
   * 为 Azure AD 目录中的全局管理员提供凭据。 Azure AD 全局管理员凭据可能不同于门户中的 Azure 凭据

        > [!NOTE]
        > 用于注册连接器的全局管理员帐户必须属于你启用应用程序代理服务的同一目录。
        >
        > 例如，如果 Azure AD 域为*contoso.com*，则全局管理员应 `admin@contoso.com` 或该域上的其他有效别名。

   * 如果为安装连接器的 VM 启用了 Internet Explorer 增强的安全配置，则可能会阻止注册屏幕。 若要允许访问，请按照错误消息中的说明进行操作，或在安装过程中关闭 Internet Explorer 增强的安全性。
   * 如果连接器注册失败，请参阅[应用程序代理故障排除](../active-directory/manage-apps/application-proxy-troubleshoot.md)。
1. 安装结束时，会显示带有出站代理的环境的说明。 若要将 Azure AD 应用程序代理连接器配置为通过出站代理进行工作，请运行提供的脚本，如 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`。
1. 在 Azure 门户中的 "应用程序代理" 页上，将列出新的连接器，其状态为 "*活动*"，如以下示例中所示：

    ![新的 Azure AD 应用程序代理连接器在 Azure 门户中显示为 "活动"](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> 若要为通过 Azure AD 应用程序代理进行身份验证的应用程序提供高可用性，可以在多个 Vm 上安装连接器。 重复上一部分中列出的相同步骤，在加入到 Azure AD DS 托管域的其他服务器上安装连接器。

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>启用基于资源的 Kerberos 约束委派

如果要使用集成 Windows 身份验证（IWA）对应用程序使用单一登录，请授予 "Azure AD 应用程序代理连接器" 权限，以模拟用户并代表用户发送和接收令牌。 若要授予这些权限，请为连接器配置 Kerberos 约束委派（KCD）以访问 Azure AD DS 托管域上的资源。 由于在 Azure AD DS 托管域中没有域管理员权限，因此不能在托管域上配置传统帐户级 KCD。 相反，请使用基于资源的 KCD。

有关详细信息，请参阅[Azure Active Directory 域服务中的配置 Kerberos 约束委派（KCD）](deploy-kcd.md)。

> [!NOTE]
> 你必须登录到作为 Azure AD 租户中*AZURE AD DC administrators*组的成员的用户帐户，才能运行以下 PowerShell cmdlet。
>
> 您的应用程序代理连接器 VM 和应用程序 Vm 的计算机帐户必须位于您有权配置基于资源的 KCD 的自定义 OU 中。 无法在内置*AAD DC 计算机*容器中为计算机帐户配置基于资源的 KCD。

使用[get-adcomputer][Get-ADComputer]检索安装了 Azure AD 应用程序代理连接器的计算机的设置。 在已加入域的管理 VM，并以作为*AZURE AD DC administrators*组成员的用户帐户登录，运行以下 cmdlet。

以下示例获取有关名为*appproxy.contoso.com*的计算机帐户的信息。 为在前面的步骤中配置的 Azure AD 应用程序代理 VM 提供自己的计算机名。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.contoso.com
```

对于运行 Azure AD 应用程序代理后面的应用的每个应用程序服务器，请使用[Get-adcomputer][Set-ADComputer] PowerShell cmdlet 配置基于资源的 KCD。 在下面的示例中，Azure AD 应用程序代理连接器授予使用*appserver.contoso.com*计算机的权限：

```powershell
Set-ADComputer appserver.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

如果部署多个 Azure AD 应用程序代理连接器，则必须为每个连接器实例配置基于资源的 KCD。

## <a name="next-steps"></a>后续步骤

将 Azure AD 应用程序代理与 Azure AD DS 集成后，发布用户要访问的应用程序。 有关详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
