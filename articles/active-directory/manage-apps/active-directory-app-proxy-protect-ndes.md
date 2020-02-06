---
title: 与 NDES 服务器上的 AD 应用程序代理集成
titleSuffix: Azure Active Directory
description: 有关部署 Azure Active Directory 应用程序代理以保护 NDES 服务器的指南。
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77032252"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>与网络设备注册服务（NDES）服务器上的 Azure AD 应用程序代理集成

Azure Active Directory （AD）应用程序代理可让你在网络内发布应用程序。 这些应用程序包括 SharePoint 站点、Microsoft Outlook Web App 和其他 Web 应用程序。 它还通过 Azure 对网络外部的用户提供安全访问。

如果你不熟悉 Azure AD 应用程序代理并想要了解详细信息，请参阅[通过 Azure AD 应用程序代理远程访问本地应用程序](application-proxy.md)。

Azure AD 应用程序代理是在 Azure 上构建的。 它为您提供了大量的网络带宽和服务器基础结构，以便更好地防范分布式拒绝服务（DDOS）攻击和卓越的可用性。 而且，无需向本地网络开放外部防火墙端口，也不需要任何外围服务器。 所有流量均源自入站。 有关出站端口的完整列表，请参阅[教程：在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)。

> Azure AD 应用程序代理是一项仅当你使用 Azure Active Directory 的高级版或基本版时才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。 
> 如果你有企业移动性套件（EMS）许可证，则可以使用此解决方案。
> Azure AD 应用程序代理连接器仅安装在 Windows Server 2012 R2 或更高版本上。 这也是 NDES 服务器的要求。

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>在 NDES 服务器上安装并注册连接器

1. 以使用应用程序代理的目录的应用程序管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 例如，如果租户域为 contoso.com，则管理员应为 admin@contoso.com 或该域上的其他任何域别名。
1. 请在右上角选择用户名。 验证是否已登录到使用应用程序代理的目录。 如果需要更改目录，请选择“切换目录”，然后选择使用应用程序代理的目录。
1. 在左侧导航面板中选择“Azure Active Directory”。
1. 在“管理”下选择“应用程序代理”。
1. 选择“下载连接器服务”。

    ![下载连接器服务，以查看服务条款](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 阅读“服务条款”。 准备就绪后，选择“接受条款并下载”。
1. 将 Azure AD 应用程序代理连接器安装程序文件复制到 NDES 服务器。 
   > 可以在企业网络中的任何服务器上安装连接器，并可访问 NDES。 不需要在 NDES 服务器上安装它。
1. 运行安装程序文件，如*aadapplicationproxyconnectorinstaller.exe*。 接受软件许可条款。
1. 在安装过程中，系统会提示在 Azure AD 目录中向应用程序代理注册连接器。
   * 为 Azure AD 目录中的全局管理员或应用程序管理员提供凭据。 Azure AD 的全局或应用程序管理员凭据可能不同于门户中的 Azure 凭据。

        > [!NOTE]
        > 用于注册连接器的全局或应用程序管理员帐户必须属于你启用应用程序代理服务的同一目录。
        >
        > 例如，如果 Azure AD 域为*contoso.com*，则全局/应用程序管理员应 `admin@contoso.com` 或该域上的其他有效别名。

   * 如果在安装连接器的服务器上启用了 Internet Explorer 增强的安全配置，则可能会阻止注册屏幕。 若要允许访问，请按照错误消息中的说明进行操作，或在安装过程中关闭 Internet Explorer 增强的安全性。
   * 如果连接器注册失败，请参阅[应用程序代理故障排除](application-proxy-troubleshoot.md)。
1. 安装结束时，会显示带有出站代理的环境的说明。 若要将 Azure AD 应用程序代理连接器配置为通过出站代理进行工作，请运行提供的脚本，如 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`。
1. 在 Azure 门户中的 "应用程序代理" 页上，将列出新的连接器，其状态为 "*活动*"，如以下示例中所示：

    ![新的 Azure AD 应用程序代理连接器在 Azure 门户中显示为 "活动"](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > 若要为通过 Azure AD 应用程序代理进行身份验证的应用程序提供高可用性，可以在多个 Vm 上安装连接器。 重复上一部分中列出的相同步骤，在加入到 Azure AD DS 托管域的其他服务器上安装连接器。

1. 安装成功后，返回到 Azure 门户。

1. 选择“企业应用程序”。

   ![确保你正在吸引正确的利益干系人](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. 选择 " **+ 新建应用程序**"，然后选择 **"本地应用程序**"。 

1. 在 "**添加自己的本地应用程序**" 中，配置下列字段：

   * **名称**：输入应用程序的名称。
   * **内部 url**：输入在其上安装连接器的 NDES 服务器的内部 URL/FQDN。
   * **预身份验证**：选择 "**传递**"。 不能使用任何形式的预身份验证。 用于证书请求（SCEP）的协议不提供此类选项。
   * 将提供的**外部 URL**复制到剪贴板。

1. 选择 " **+ 添加**" 以保存应用程序。

1. 测试是否可以通过 Azure AD 应用程序代理访问 NDES 服务器，方法是将在步骤10中复制的链接粘贴到浏览器中。 应会看到默认的 IIS 欢迎页。

1. 作为最终测试，将*mscep*路径添加到在上一步中粘贴的现有 URL：

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. 应会看到**HTTP 错误 403-禁止**响应。

1. 将提供的 NDES URL （通过 Microsoft Intune）更改为设备，此更改可能在 Microsoft 终结点配置中心或 Intune 云中。

   * 对于 "配置中心"，请前往证书注册点（CRP）并调整 URL。 此 URL 是设备向其提供并提出其挑战的内容。
   * 仅适用于 Intune 云（也称为 Intune 独立版），可以编辑或创建新的 SCEP 策略并添加新的 URL。

## <a name="next-steps"></a>后续步骤

将 Azure AD 应用程序代理与 NDES 集成后，发布用户要访问的应用程序。 有关详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。
