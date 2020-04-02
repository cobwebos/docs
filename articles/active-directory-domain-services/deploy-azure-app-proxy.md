---
title: 为 Azure AD 域服务部署 Azure AD 应用程序代理 |微软文档
description: 了解如何通过在 Azure 活动目录域服务托管域中部署和配置 Azure 活动目录应用程序代理，为远程工作人员提供安全访问
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 3fb731736941293a82baba86165a1205bd8a05c5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519034"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>部署 Azure AD 应用程序代理，以安全访问 Azure AD 域服务托管域中的内部应用程序

使用 Azure AD 域服务（Azure AD DS），可以将本地运行的旧应用程序提升到 Azure 中。 然后，Azure 活动目录 （AD） 应用程序代理通过安全地发布 Azure AD DS 托管域的这些内部应用程序，以便可以通过 Internet 访问这些应用程序，从而帮助您支持远程工作。

如果您是 Azure AD 应用程序代理的新功能，并且想要了解详细信息，请参阅[如何提供安全远程访问内部应用程序](../active-directory/manage-apps/application-proxy.md)。

本文介绍如何创建和配置 Azure AD 应用程序代理连接器，以提供安全访问 Azure AD DS 托管域中的应用程序。

## <a name="before-you-begin"></a>在开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
    * 使用 Azure AD 应用程序代理需要**Azure AD 高级许可证**。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。

## <a name="create-a-domain-joined-windows-vm"></a>创建加入域的 Windows VM

要将流量路由到环境中运行的应用程序，请安装 Azure AD 应用程序代理连接器组件。 此 Azure AD 应用程序代理连接器必须安装在已加入 Azure AD DS 托管域的 Windows 服务器虚拟机 （VM） 上。 对于某些应用程序，可以部署多个安装了连接器的服务器。 此部署选项提供更好的可用性，并可帮助处理较重的身份验证负载。

运行 Azure AD 应用程序代理连接器的 VM 必须位于启用 Azure AD DS 的同一虚拟网络上。 然后承载使用应用程序代理发布的应用程序的 VM 也必须部署在同一 Azure 虚拟网络上。

要为 Azure AD 应用程序代理连接器创建 VM，请完成以下步骤：

1. [创建自定义 OU](create-ou.md)。 您可以将管理此自定义 OU 的权限委派给 Azure AD DS 托管域中的用户。 Azure AD 应用程序代理的 VM 和运行应用程序的 VM 必须是自定义 OU 的一部分，而不是默认*的 AAD DC 计算机*OU。
1. [域将虚拟机][create-join-windows-vm]（运行 Azure AD 应用程序代理连接器的虚拟机）和运行应用程序的虚拟机联接到 Azure AD DS 托管域。 从上一步在自定义 OU 中创建这些计算机帐户。

## <a name="download-the-azure-ad-application-proxy-connector"></a>下载 Azure AD 应用程序代理连接器

执行以下步骤以下载 Azure AD 应用程序代理连接器。 您下载的设置文件将复制到应用代理 VM 的下一部分。

1. 使用在 Azure AD 中具有*企业管理员*权限的用户帐户登录到[Azure 门户](https://portal.azure.com)。
1. 在门户顶部搜索并选择**Azure 活动目录**，然后选择**企业应用程序**。
1. 从左侧的菜单中选择**应用程序代理**。 要创建第一个连接器并启用应用代理，请选择链接以**下载连接器**。
1. 在下载页面上，接受许可条款和隐私协议，然后选择 **"接受条款&下载**"。

    ![下载 Azure AD 应用代理连接器](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>安装并注册 Azure AD 应用程序代理连接器

准备好将 VM 用作 Azure AD 应用程序代理连接器，现在复制并运行从 Azure 门户下载的安装程序文件。

1. 将 Azure AD 应用程序代理连接器设置文件复制到 VM。
1. 运行设置文件，如*AAD应用程序代理连接器安装程序.exe*。 接受软件许可条款。
1. 在安装过程中，系统会提示您将连接器注册到 Azure AD 目录中的应用程序代理。
   * 在 Azure AD 目录中为全局管理员提供凭据。 Azure AD 全局管理员凭据可能与门户中的 Azure 凭据不同

        > [!NOTE]
        > 用于注册连接器的全局管理员帐户必须属于启用应用程序代理服务的同一目录。
        >
        > 例如，如果 Azure AD 域*是aaddscontoso.com，* 则全局管理员应`admin@aaddscontoso.com`为该域上的另一个有效别名。

   * 如果为安装连接器的 VM 启用 Internet Explorer 增强的安全配置，则注册屏幕可能会被阻止。 要允许访问，请按照错误消息中的说明操作，或在安装过程中关闭 Internet Explorer 增强的安全性。
   * 如果连接器注册失败，请参阅[排除应用程序代理故障](../active-directory/manage-apps/application-proxy-troubleshoot.md)。
1. 在设置结束时，将显示一个注释，用于具有出站代理的环境。 要将 Azure AD 应用程序代理连接器配置为通过出站代理工作，请运行提供的脚本，`C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`如 。
1. 在 Azure 门户中的应用程序代理页上，新连接器以*活动*状态列出，如以下示例所示：

    ![在 Azure 门户中显示为活动的新 Azure AD 应用程序代理连接器](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> 要为通过 Azure AD 应用程序代理进行身份验证的应用程序提供高可用性，可以在多个 VM 上安装连接器。 重复上一节中列出的相同步骤，以在加入到 Azure AD DS 托管域的其他服务器上安装连接器。

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>启用基于资源的 Kerberos 约束委派

如果要使用集成 Windows 身份验证 （IWA） 对应用程序使用单一登录，请授予 Azure AD 应用程序代理连接器权限以模拟用户并代表他们发送和接收令牌。 要授予这些权限，请为连接器配置 Kerberos 约束委派 （KCD） 以访问 Azure AD DS 托管域上的资源。 由于 Azure AD DS 托管域中没有域管理员权限，因此无法在托管域上配置传统的帐户级 KCD。 相反，请使用基于资源的 KCD。

有关详细信息，请参阅在[Azure 活动目录域服务中配置 Kerberos 约束委派 （KCD）。](deploy-kcd.md)

> [!NOTE]
> 您必须登录到 Azure AD 租户中的 Azure AD DC*管理员*组的成员的用户帐户才能运行以下 PowerShell cmdlet。
>
> 应用代理连接器 VM 和应用程序 VM 的计算机帐户必须位于自定义 OU 中，您可以在其中配置基于资源的 KCD 的权限。 无法为内置*AAD DC 计算机*容器中的计算机帐户配置基于资源的 KCD。

使用[Get-AD 计算机][Get-ADComputer]检索安装 Azure AD 应用程序代理连接器的计算机的设置。 从加入域的管理 VM 中作为*Azure AD DC 管理员*组的成员登录的用户帐户，运行以下 cmdlet。

下面的示例获取有关名为*appproxy.aaddscontoso.com*的计算机帐户的信息。 为在前面的步骤中配置的 Azure AD 应用程序代理 VM 提供您自己的计算机名称。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

对于运行 Azure AD 应用程序代理后面的应用的每个应用程序服务器，使用[Set-AD 计算机][Set-ADComputer]PowerShell cmdlet 配置基于资源的 KCD。 在以下示例中，Azure AD 应用程序代理连接器被授予使用*appserver.aaddscontoso.com*计算机的权限：

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

如果部署多个 Azure AD 应用程序代理连接器，则必须为每个连接器实例配置基于资源的 KCD。

## <a name="next-steps"></a>后续步骤

将 Azure AD 应用程序代理与 Azure AD DS 集成后，发布应用程序供用户访问。 有关详细信息，请参阅使用[Azure AD 应用程序代理发布应用程序](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
