---
title: 在 NDES 服务器上与 AD 应用程序代理集成
titleSuffix: Azure Active Directory
description: 有关部署 Azure 活动目录应用程序代理以保护 NDES 服务器的指导。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032252"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>在网络设备注册服务 （NDES） 服务器上与 Azure AD 应用程序代理集成

Azure 活动目录 （AD） 应用程序代理允许您在网络内发布应用程序。 这些应用程序是共享点网站、Microsoft Outlook Web 应用和其他 Web 应用程序。 它还通过 Azure 提供对网络外部用户的安全访问。

如果您是 Azure AD 应用程序代理的新功能，并且想要了解详细信息，请参阅[通过 Azure AD 应用程序代理对本地应用程序进行远程访问](application-proxy.md)。

Azure AD 应用程序代理在 Azure 上生成。 它为您提供了大量的网络带宽和服务器基础设施，以更好地抵御分布式拒绝服务 （DDOS） 攻击和卓越的可用性。 此外，无需向本地网络打开外部防火墙端口，也不需要 DMZ 服务器。 所有流量都源自入站。 有关出站端口的完整列表，请参阅[教程：在 Azure 活动目录中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)。

> Azure AD 应用程序代理是一项功能，仅在使用 Azure 活动目录的高级版或基本版时才可用。 有关详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。 
> 如果您有企业移动套件 （EMS） 许可证，则有资格使用此解决方案。
> Azure AD 应用程序代理连接器仅在 Windows 服务器 2012 R2 或更高版本中安装。 这也是 NDES 服务器的要求。

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>在 NDES 服务器上安装和注册连接器

1. 以使用应用程序代理的目录的应用程序管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 例如，如果租户域为 contoso.com，则管理员应为 admin@contoso.com 或该域上的其他任何域别名。
1. 请在右上角选择用户名。 验证是否已登录到使用应用程序代理的目录。 如果需要更改目录，请选择“切换目录”，然后选择使用应用程序代理的目录****。
1. 在左侧导航面板中选择“Azure Active Directory”****。
1. 在“管理”下选择“应用程序代理”********。
1. 选择“下载连接器服务”****。

    ![下载连接器服务，以查看服务条款](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 阅读“服务条款”。 准备就绪后，选择“接受条款并下载”****。
1. 将 Azure AD 应用程序代理连接器设置文件复制到 NDES 服务器。 
   > 您可以在公司网络内具有对 NDES 访问权限的任何服务器上安装连接器。 您不必将其安装在 NDES 服务器本身上。
1. 运行设置文件，如*AAD应用程序代理连接器安装程序.exe*。 接受软件许可条款。
1. 在安装过程中，系统会提示您将连接器注册到 Azure AD 目录中的应用程序代理。
   * 在 Azure AD 目录中为全局或应用程序管理员提供凭据。 Azure AD 全局或应用程序管理员凭据可能与门户中的 Azure 凭据不同。

        > [!NOTE]
        > 用于注册连接器的全局或应用程序管理员帐户必须属于启用应用程序代理服务的同一目录。
        >
        > 例如，如果 Azure AD 域*contoso.com，* 则全局/应用程序管理员应为`admin@contoso.com`该域上的另一个有效别名。

   * 如果安装连接器的服务器启用 Internet Explorer 增强的安全配置，则注册屏幕可能会被阻止。 要允许访问，请按照错误消息中的说明操作，或在安装过程中关闭 Internet Explorer 增强的安全性。
   * 如果连接器注册失败，请参阅[排除应用程序代理故障](application-proxy-troubleshoot.md)。
1. 在设置结束时，将显示一个注释，用于具有出站代理的环境。 要将 Azure AD 应用程序代理连接器配置为通过出站代理工作，请运行提供的脚本，`C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`如 。
1. 在 Azure 门户中的应用程序代理页上，新连接器以*活动*状态列出，如以下示例所示：

    ![在 Azure 门户中显示为活动的新 Azure AD 应用程序代理连接器](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > 要为通过 Azure AD 应用程序代理进行身份验证的应用程序提供高可用性，可以在多个 VM 上安装连接器。 重复上一节中列出的相同步骤，以在加入到 Azure AD DS 托管域的其他服务器上安装连接器。

1. 成功安装后，返回 Azure 门户。

1. 选择“企业应用程序”。****

   ![确保您与合适的利益相关者接洽](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. 选择 **+新应用程序**，然后选择**本地应用程序**。 

1. 在 **"添加您自己的本地应用程序中**"，配置以下字段：

   * **名称**：输入应用程序的名称。
   * **内部 URL**： 输入您安装连接器的 NDES 服务器的内部 URL/FQDN。
   * **预身份验证**：选择**传递**。 无法使用任何形式的预身份验证。 用于证书请求 （SCEP） 的协议不提供此类选项。
   * 将提供的**外部 URL**复制到剪贴板。

1. 选择 **"添加"** 以保存应用程序。

1. 通过将步骤 10 中复制的链接粘贴到浏览器中，测试是否可以通过 Azure AD 应用程序代理访问 NDES 服务器。 您应该会看到默认的 IIS 欢迎页面。

1. 作为最终测试，将*mscep.dll*路径添加到上一步骤中粘贴的现有 URL：

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. 您应该会看到**HTTP 错误 403 = 禁止**响应。

1. 将提供的 NDES URL（通过 Microsoft Intune）更改为设备，此更改可能位于 Microsoft 端点配置中心或 Intune 云中。

   * 对于配置中心，请转到证书注册点 （CRP） 并调整 URL。 此 URL 是设备向它们发出并提出其挑战的内容。
   * 对于仅 Intune 云（也称为 Intune 独立），编辑或创建新的 SCEP 策略并添加新 URL。

## <a name="next-steps"></a>后续步骤

将 Azure AD 应用程序代理与 NDES 集成后，发布应用程序供用户访问。 有关详细信息，请参阅使用[Azure AD 应用程序代理发布应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。
