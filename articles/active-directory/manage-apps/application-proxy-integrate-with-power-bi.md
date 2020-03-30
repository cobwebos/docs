---
title: 使用 Azure AD 应用程序代理启用对 Power BI 的远程访问
description: 介绍有关如何将本地 Power BI 与 Azure AD 应用程序代理集成的基础知识。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111585"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理启用对 Power BI 移动版的远程访问

本文讨论如何使用 Azure AD 应用程序代理使 Power BI 移动应用连接到 Power BI 报告服务器 （PBIRS） 和 SQL 服务器报告服务 （SSRS） 2016 及更高版本。 通过此集成，远离公司网络的用户可以从 Power BI 移动应用访问其 Power BI 报告，并受 Azure AD 身份验证的保护。 此保护包括[安全优势](application-proxy-security.md#security-benefits)，如条件访问和多重身份验证。  

## <a name="prerequisites"></a>先决条件

本文假定您已部署报表服务和[启用了应用程序代理](application-proxy-add-on-premises-application.md)。

- 启用应用程序代理需要在 Windows 服务器上安装连接器并完成[先决条件](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)，以便连接器可以与 Azure AD 服务进行通信。  
- 发布 Power BI 时，我们建议您使用相同的内部和外部域。 要了解有关自定义域的更多信息，请参阅[在应用程序代理 中使用自定义域](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)。
- 此集成可用于 Power **BI 移动 iOS 和 Android**应用程序。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>第 1 步：配置 Kerberos 约束委派 （KCD）

对于使用 Windows 身份验证的本地应用程序，可以通过 Kerberos 身份验证协议以及一项称为 Kerberos 约束委派 (KCD) 的功能来实现单一登录 SSO。 配置后，KCD 允许应用程序代理连接器为用户获取 Windows 令牌，即使用户尚未直接登录到 Windows。 要了解有关 KCD 的详细信息，请参阅[Kerberos 约束委派概述](https://technet.microsoft.com/library/jj553400.aspx)和[Kerberos 约束委派，以便使用应用程序代理对应用进行单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。

Reporting Services 端无其他需要配置的内容。 只需确保具有有效的服务主体名称 （SPN），以启用正确的 Kerberos 身份验证。 还要确保已启用报告服务服务器进行协商身份验证。

要为报告服务设置 KCD，请继续执行以下步骤。

### <a name="configure-the-service-principal-name-spn"></a>配置服务主体名称 （SPN）

SPN 是使用 Kerberos 身份验证的服务的唯一标识符。 您需要确保报表服务器具有正确的 HTTP SPN。 有关如何为报表服务器配置正确的服务主体名称 (SPN) 的信息，请参阅[为报表服务器注册服务主体名称 (SPN)](https://msdn.microsoft.com/library/cc281382.aspx)。
可以结合 -L 选项运行 Setspn 命令，来验证是否已添加 SPN。 若要详细了解该命令，请参阅 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)。

### <a name="enable-negotiate-authentication"></a>启用协商身份验证

要使报表服务器使用 Kerberos 身份验证，请将报表服务器的身份验证类型配置为 RSWindowsNegotiate。 使用 rsreportserver.config 文件配置此设置。

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

有关详细信息，请参阅[修改 Reporting Services 配置文件](https://msdn.microsoft.com/library/bb630448.aspx)和[在报表服务器上配置 Windows 身份验证](https://msdn.microsoft.com/library/cc281253.aspx)。

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>确保连接器受信任，以便委派到添加到报告服务应用程序池帐户的 SPN
配置 KCD，以便 Azure AD 应用程序代理服务可以将用户标识委派给报告服务应用程序池帐户。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，在这种情况下，该服务器将上下文传递给目标应用程序或报告服务。

要配置 KCD，请对每台连接器计算机重复以下步骤：

1. 以域管理员身份登录到域控制器，然后打开**活动目录用户和计算机**。
2. 找到运行连接器的计算机。  
3. 双击计算机，然后选择"**委派**"选项卡。
4. 将委派设置设置为**信任此计算机，以便仅委派到指定的服务**。 然后，选择“使用任意身份验证协议”。****
5. 选择 **"添加**"，然后选择 **"用户"或"计算机**"。
6. 输入用于报告服务的服务帐户。 这是在 Reporting Services 配置中将 SPN 添加到其中的帐户。
7. 单击“确定”。 要保存更改，请再次单击 **"确定**"。

有关详细信息，请参阅[Kerberos 约束委派，以便使用应用程序代理对应用进行单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>第 2 步：通过 Azure AD 应用程序代理发布报表服务

现在，您已准备好配置 Azure AD 应用程序代理。

1. 使用以下设置通过应用程序代理发布报表服务。 有关如何通过应用程序代理发布应用程序的分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   - **内部 URL**：输入连接器可以在公司网络中到达的报告服务器的 URL。 确保可以从安装连接器的服务器联系到此 URL。 最佳做法是使用顶级域，例如`https://servername/`避免通过应用程序代理发布的子路径的问题。 例如，使用`https://servername/`和`https://servername/reports/`而不是`https://servername/reportserver/`或 。
     > [!NOTE]
     > 我们建议使用与报表服务器的安全 HTTPS 连接。 有关如何[配置本地模式报表服务器上](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)的 SSL 连接，请参阅配置 SSL 连接。
   - **外部 URL**： 输入 Power BI 移动应用将连接到的公共 URL。 例如，如果使用了自定义域，`https://reports.contoso.com`则可能如下所示。 要使用自定义域，请上载域的证书，并将 DNS 记录指向应用程序的默认msappproxy.net域。 有关详细步骤，请参阅[在 Azure AD 应用程序代理 中使用自定义域](application-proxy-configure-custom-domain.md)。

   - **预身份验证方法**： Azure 活动目录

2. 发布应用后，请执行以下步骤来配置单一登录设置：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在门户中的应用程序页上，选择“单一登录”****。

   b.保留“数据库类型”设置，即设置为“共享”。 对于**单一登录模式**，请选择**集成 Windows 身份验证**。

   c. 将**内部应用程序 SPN**设置为之前设置的值。  

   d.单击“下一步”。 针对你要代表你的用户使用的连接器选择“委派的登录标识”。**** 有关详细信息，请参阅[使用不同的本地和云标识](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击 **"保存"** 以保存更改。

要完成应用程序的设置，请转到 **"用户和组"** 部分并分配用户以访问此应用程序。

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>第 3 步：修改应用程序的回复 URI

在 Power BI 移动应用可以连接和访问报表服务之前，必须配置步骤 2 中自动为您创建的应用程序注册。 

1. 在 Azure 活动目录**概述页上**，选择**应用注册**。
2. 在"**所有应用程序**"选项卡下搜索您在步骤 2 中创建的应用程序。
3. 选择应用程序，然后选择**身份验证**。
4. 根据正在使用的平台添加以下重定向 URI。

   为 Power BI 移动**iOS**配置应用时，添加以下公共客户端类型的重定向 URI（移动&桌面）：
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   为 Power BI 移动**Android**配置应用时，添加以下公共客户端类型的重定向 URI（移动&桌面）：
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 必须添加重定向 URI，应用程序才能正常工作。 如果要为 Power BI 移动 iOS 和 Android 配置应用，则将以下公共客户端类型的重定向 URI（移动&桌面）添加到为 iOS 配置的重定向 URI`urn:ietf:wg:oauth:2.0:oob`列表中： 。

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>第 4 步：从 Power BI 移动应用连接

1. 在 Power BI 移动应用中，连接到报告服务实例。 为此，请输入通过应用程序代理发布的应用程序的**外部 URL。**

   ![使用外部 URL 为 BI 移动应用供电](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. 选择“连接”****。 您将被定向到 Azure 活动目录登录页。

3. 输入用户的有效凭据，然后选择 **"登录**"。 您将看到来自报告服务服务器的元素。

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>第 5 步：为托管设备配置 Intune 策略（可选）

您可以使用 Microsoft Intune 管理公司员工使用的客户端应用。 Intune 允许您使用数据加密和其他访问要求等功能。 要通过 Intune 了解有关应用管理的更多详细信息，请参阅 Intune 应用管理。 要使 Power BI 移动应用程序使用 Intune 策略，请使用以下步骤。

1. 转到**Azure 活动目录**，然后**应用注册**。
2. 注册本机客户端应用程序时，请选择步骤 3 中配置的应用程序。
3. 在应用程序的页面上，选择 API**权限**。
4. 单击 **"添加权限**"。 
5. **在我的组织使用的 API**下，搜索"微软移动应用程序管理"并选择它。
6. 将**设备管理管理应用.ReadWrite**权限添加到应用程序
7. 单击 **"授予管理员同意"** 以授予对应用程序的权限访问权限。
8. 通过引用[如何创建和分配应用保护策略来](https://docs.microsoft.com/intune/app-protection-policies)配置所需的 Intune 策略。

## <a name="troubleshooting"></a>疑难解答

如果应用程序在尝试加载报表超过几分钟后返回错误页，则可能需要更改超时设置。 默认情况下，应用程序代理支持最多需要 85 秒才能响应请求的应用程序。 要将此设置延长到 180 秒，请在应用程序的"应用代理设置"页中选择后端超时为**Long。** 有关如何创建快速可靠的报表的提示，请参阅[Power BI 报告最佳实践](https://docs.microsoft.com/power-bi/power-bi-reports-performance)。

## <a name="next-steps"></a>后续步骤

- [使本机客户端应用程序能够与代理应用程序进行交互](application-proxy-configure-native-client-application.md)
- [在 Power BI 移动应用中查看本地报表服务器报表和 KPI](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
