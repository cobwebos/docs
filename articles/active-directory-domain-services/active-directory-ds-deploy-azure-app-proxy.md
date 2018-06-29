---
title: Azure Active Directory 域服务：部署 Azure Active Directory 应用程序代理 | Microsoft 文档
description: 在 Azure Active Directory 域服务托管域上使用 Azure AD 应用程序代理
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 43b43be154756838d8c130b2ec1a0dbc40405422
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332348"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服务托管域上部署 Azure AD 应用程序代理
Azure Active Directory (AD) 应用程序代理可发布要通过 Internet 访问的本地应用程序，帮助用户为远程辅助角色提供支持。 使用 Azure AD 域服务，现在可以将本地运行的旧版应用程序提升并转移到 Azure 基础结构服务。 然后，可以使用 Azure AD 应用程序代理发布这些应用程序，以允许组织中的用户进行安全远程访问。

如果不熟悉 Azure AD 应用程序代理，可通过[如何提供对本地应用程序的安全远程访问](../active-directory/manage-apps/application-proxy.md)一文详细了解此功能。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：

1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 要使用 Azure AD 应用程序代理，需要 **Azure AD Basic 或 Premium 许可证**。
4. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>任务 1 - 为 Azure AD 目录启用 Azure AD 应用程序代理
执行以下步骤可为 Azure AD 目录启用 Azure AD 应用程序代理。

1. 在 [Azure 门户](http://portal.azure.com)中，以管理员身份登录。

2. 单击“Azure Active Directory”以显示目录概述。 单击“企业应用程序”。

    ![选择 Azure AD 目录](./media/app-proxy/app-proxy-enable-start.png)
3. 单击“应用程序代理”。 如果没有 Azure AD Basic 或 Azure AD Premium 订阅，将看到用于启用试用版的选项。 将“启用应用程序代理?”切换到“启用”，并单击“保存”。

    ![启用应用代理](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. 若要下载连接器，请单击“连接器”按钮。

    ![下载连接器](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. 在下载页上，接受许可条款和隐私协议，并单击“下载”按钮。

    ![确认下载](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>任务 2 - 预配已加入域的 Windows 服务器，以部署 Azure AD 应用程序代理连接器
需要可在其上安装 Azure AD 应用程序代理连接器的已加入域的 Windows Server 虚拟机。 对于某些应用程序，可以选择预配多个要在其上安装连接器的服务器。 此部署选项提供更好的可用性，并可帮助处理较重的身份验证负载。

在已启用 Azure AD 域服务托管域的同一虚拟网络（或连接/对等虚拟网络）中预配多个连接器服务器。 同样，托管通过应用程序代理发布的应用程序的服务器需要安装在同一 Azure 虚拟网络中。

要预配连接器服务器，请遵循[将 Windows 虚拟机加入托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文中所述的任务。


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>任务 3 - 安装和注册 Azure AD 应用程序代理连接器
先前，已预配 Windows Server 虚拟机，并已将其加入到托管域。 在此任务中，会在此虚拟机上安装 Azure AD 应用程序代理连接器。

1. 将连接器安装包复制到要在其上安装 Azure AD Web 应用程序代理连接器的 VM。

2. 在此虚拟机上运行 **AADApplicationProxyConnectorInstaller.exe**。 接受软件许可条款。

    ![接受条款以安装](./media/app-proxy/app-proxy-install-connector-terms.png)
3. 在安装期间，系统会提示将连接器注册到 Azure AD 目录的应用程序代理。
    * 提供 **Azure AD 全局管理员凭据**。 全局管理员租户可能不同于 Microsoft Azure 凭据。
    * 用于注册连接器的管理员帐户必须属于已启用应用程序代理服务的同一目录。 例如，如果租户域为 contoso.com，则管理员应该为 admin@contoso.com 或该域上的任何其他有效别名。
    * 如果已为要安装连接器的服务器启用“IE 增强的安全配置”，则可能无法显示注册屏幕。 若要允许访问，请按照错误消息中的说明进行操作。 确保 Internet Explorer 增强的安全性已关闭。
    * 如果连接器注册不成功，请参阅 [故障排除应用程序代理](../active-directory/manage-apps/application-proxy-troubleshoot.md)。

    ![已安装的连接器](./media/app-proxy/app-proxy-connector-installed.png)
4. 若要确保连接器正常工作，请运行 Azure AD 应用程序代理连接器疑难解答程序。 运行疑难解答程序后，应看到成功的报告。

    ![疑难解答程序成功](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. 应看到新安装的连接器在 Azure AD 目录的应用程序代理页上列出。

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> 可以选择将连接器安装在多个服务器上，以确保在对通过 Azure AD 应用程序代理发布的应用程序进行身份验证时提供高可用性。 执行上面所列的相同步骤在已加入到托管域的其他服务器上安装连接器。
>
>

## <a name="next-steps"></a>后续步骤
已设置 Azure AD 应用程序代理，并已将其集成到 Azure AD 域服务托管域。

* **将应用程序迁移到 Azure 虚拟机：** 可以将应用程序从本地服务器提升并转移到已加入托管域的 Azure 虚拟机。 这样做可帮助免除在本地运行服务器的基础结构成本。

* **使用 Azure AD 应用程序代理发布应用程序：** 使用 Azure AD 应用程序代理发布在 Azure 虚拟机上运行的应用程序。 有关详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>部署说明 - 使用 Azure AD 应用程序代理发布 IWA（集成 Windows 身份验证）应用程序
通过授予应用程序代理连接器权限来模拟用户并代表用户发送和接收令牌，以使用集成 Windows 身份验证 (IWA) 实现应用程序的单一登录。 为连接器配置 Kerberos 约束委派 (KCD) 以授予访问托管域上的资源所需的权限。 在托管域上使用基于资源的 KCD 机制提高安全性。


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>为 Azure AD 应用程序代理连接器启用基于资源的 Kerberos 约束委派
应针对 Kerberos 约束委派 (KCD) 配置 Azure 应用程序代理连接器，使其可以模拟托管域上的用户。 在 Azure AD 域服务托管域上，没有域管理员权限。 因此，**无法在托管域上配置传统帐户级 KCD**。

请使用基于资源的 KCD，如[此文](active-directory-ds-enable-kcd.md)中所述。

> [!NOTE]
> 必须是“AAD DC 管理员”组的成员，才能使用 AD PowerShell cmdlet 管理托管域。
>
>

可使用 Get-ADComputer PowerShell cmdlet 检索要在其上安装 Azure AD 应用程序代理连接器的计算机的设置。
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

此后，可使用 Set-ADComputer cmdlet 为资源服务器设置基于资源的 KCD。
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

如果已在托管域上部署多个应用程序代理连接器，则需要为每个此类连接器实例配置基于资源的 KCD。


## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [在托管域上配置 Kerberos 约束委派](active-directory-ds-enable-kcd.md)
* [Kerberos 约束委派概述](https://technet.microsoft.com/library/jj553400.aspx)
