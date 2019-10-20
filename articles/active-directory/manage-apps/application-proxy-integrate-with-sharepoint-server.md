---
title: 通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问 | Microsoft 文档
description: 概要介绍如何将本地 SharePoint 服务器与 Azure AD 应用程序代理相集成。
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
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f1351a2ebe6a23dc4d1e31f30f1c69ac862b21
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595438"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问

此循序渐进指南介绍了如何将本地 SharePoint 场与 Azure Active Directory （Azure AD）应用程序代理相集成。

## <a name="prerequisites"></a>必备组件

若要执行配置，需要以下资源：
- SharePoint 2013 场或更高版本。
- 具有包含应用程序代理的计划的 Azure AD 租户。 了解[Azure AD 计划和定价](https://azure.microsoft.com/pricing/details/active-directory/)的详细信息。
- Azure AD 租户中的[已验证的自定义域](../fundamentals/add-custom-domain.md)。
- 本地 Active Directory 与 Azure AD Connect 同步，用户可以通过该用户[登录到 Azure](../hybrid/plan-connect-user-signin.md)。
- 在企业域中的计算机上安装并运行的应用程序代理连接器。

用应用程序代理配置 SharePoint 需要两个 Url：
- 外部 URL，对最终用户可见并在 Azure AD 中确定。 此 URL 可以使用自定义域。 详细了解如何使用[Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)。
- 内部 URL，只在企业域中是已知的，不能直接使用。

> [!IMPORTANT]
> 若要确保正确映射链接，请遵循以下针对内部 URL 的建议：
> - 使用 HTTPS。
> - 请勿使用自定义端口。
> - 在公司域名系统（DNS）中，创建一个主机（A）以指向 SharePoint WFE （或负载均衡器），而不是一个别名（CName）。

本文使用以下值：
- 内部 URL： `https://sharepoint`
- 外部 URL： `https://spsites-demo1984.msappproxy.net/`
- SharePoint web 应用程序的应用程序池帐户： `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>步骤1：在 Azure AD 中配置使用应用程序代理的应用程序

在此步骤中，会在使用应用程序代理的 Azure Active Directory 租户中创建应用程序。 设置外部 URL，并指定内部 URL，这两个 URL 将在以后的 SharePoint 中使用。

1. 按照以下设置所述创建应用。 有关分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   * **内部 url**：将在 sharepoint 中稍后设置的 SHAREPOINT 内部 url，如 `https://sharepoint`。
   * **预身份验证**： Azure Active Directory
   * **转换标头中的 url**：否
   * **转换应用程序主体中的 url**：否

   ![发布 SharePoint 应用程序](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 发布应用后，请按照以下步骤配置单一登录设置：

   1. 在门户中的应用程序页上，选择“单一登录”。
   1. 对于**单一登录模式**，请选择 "**集成 Windows 身份验证**"。
   1. 将 "**内部应用程序 SPN** " 设置为之前设置的值。 对于本示例，值为 `HTTP/sharepoint`。
   1. 在 "**委派的登录标识**" 下，为 Active Directory 林配置选择最适合的选项。 例如，如果林中有单个 Active Directory 域，请选择 **"本地 SAM 帐户名称"** （如以下屏幕截图所示）。 但如果用户与 SharePoint 和应用程序代理连接器服务器不在同一个域中，请选择 **"本地用户主体名称**" （屏幕截图中未显示）。

   ![为 SSO 配置集成 Windows 身份验证](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 要完成应用程序设置，请转到“用户和组”部分，分配要访问此应用程序的用户。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>步骤2：配置 SharePoint web 应用程序

必须使用 Kerberos 和相应的备用访问映射来配置 SharePoint web 应用程序，以便与 Azure AD 应用程序代理正常工作。 有两个可能的选项：

- 创建新的 web 应用程序并仅使用默认区域。 这是首选选项，因为它提供 SharePoint 的最佳体验（例如，SharePoint 生成的电子邮件警报中的链接始终指向默认区域）。
- 扩展现有 web 应用程序以在非默认区域中配置 Kerberos。

> [!IMPORTANT]
> 无论使用哪个区域，SharePoint web 应用程序的应用程序池帐户都必须是域帐户，Kerberos 才能正常工作。

### <a name="provision-the-sharepoint-web-application"></a>设置 SharePoint web 应用程序

- 如果创建新的 web 应用程序并仅使用默认区域（首选选项）：

    1. 启动**SharePoint Management Shell**并运行以下脚本：

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. 打开 **SharePoint 管理中心**站点。
    1. 在“系统设置”下，选择“配置备用访问映射”。 此时将打开 "**备用访问映射集合**" 框。
    1. 用新的 web 应用程序筛选显示内容，并确认您看到如下所示的内容：

       ![Web 应用程序的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 如果将现有 web 应用程序扩展到新区域（以防无法使用默认区域）：

    1. 启动 SharePoint Management Shell 并运行以下脚本：

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. 打开 **SharePoint 管理中心**站点。
    1. 在“系统设置”下，选择“配置备用访问映射”。 此时将打开 "**备用访问映射集合**" 框。
    1. 用已扩展的 web 应用程序筛选显示，并确认你看到如下所示的内容：

        ![扩展应用程序的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>请确保 SharePoint web 应用程序在域帐户下运行

若要确定运行 SharePoint web 应用程序的应用程序池的帐户，并确保该帐户是域帐户，请按照以下步骤操作：

1. 打开 **SharePoint 管理中心**站点。
1. 转到“安全”并选择“配置服务帐户”。
1. 选择 " **Web 应用程序池-YourWebApplicationName**"。

   ![用于配置服务帐户的选项](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 确认 "**为此组件选择帐户**" 将返回域帐户，并记住该帐户，因为下一步需要用到它。

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>确保为 Extranet 区域的 IIS 站点配置了 HTTPS 证书

由于内部 URL 使用 HTTPS 协议（`https://SharePoint/`），因此必须在 Internet Information Services （IIS）站点上设置证书。

1. 打开 Windows PowerShell 控制台。
1. 运行以下脚本来生成自签名证书，并将其添加到计算机的 "我的存储" 中：

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自签名证书仅适用于测试目的。 在生产环境中，我们强烈建议你改用证书颁发机构颁发的证书。

1. 打开 Internet Information Services 管理器控制台。
1. 在树视图中展开服务器，展开 "**站点**"，选择 " **SharePoint-AAD 代理**" 站点，然后选择 "**绑定**"。
1. 选择 " **https 绑定**"，然后选择 "**编辑**"。
1. 在 "SSL 证书" 字段中，选择 " **SharePoint**证书"，然后选择 **"确定"** 。

你现在可以通过 Azure AD 应用程序代理从外部访问 SharePoint 站点。

## <a name="step-3-configure-kerberos-constrained-delegation"></a>步骤3：配置 Kerberos 约束委派

用户首先会在 Azure AD 中进行身份验证，然后通过 Azure AD 代理连接器使用 Kerberos 进行身份验证。 若要允许连接器代表 Azure AD 用户获取 Kerberos 令牌，必须使用协议转换配置 Kerberos 约束委派（KCD）。 若要了解有关 KCD 的详细信息，请参阅[Kerberos 约束委派概述](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))。

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>为 SharePoint 服务帐户设置 SPN

本文将 `https://sharepoint` 内部 URL，因此 `HTTP/sharepoint` 了服务主体名称（SPN）。 您必须将这些值替换为与您的环境相对应的值。
若要为 SharePoint 应用程序池帐户注册 SPN `HTTP/sharepoint` `Contoso\spapppool`，请在命令提示符下以域管理员身份运行以下命令：

`setspn -S HTTP/sharepoint Contoso\spapppool`

@No__t_0 命令将在添加 SPN 之前搜索该 SPN。 如果 SPN 已存在，则会出现 "**重复 Spn 值**" 错误。 在这种情况下，如果未在正确的应用程序池帐户下设置现有 SPN，请考虑删除该 SPN。 可以通过使用-L 选项运行 `Setspn` 命令来验证 SPN 是否已成功添加。 若要详细了解该命令，请参阅 [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))。

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>确保连接器受信任，可委派给添加到 SharePoint 应用程序池帐户的 SPN

配置 KCD，使 Azure AD 应用程序代理服务能够向 SharePoint 应用程序池帐户委托用户标识。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器会将上下文传递给目标应用程序（在本例中为 SharePoint）。

若要配置 KCD，请针对每个连接器计算机执行以下步骤：

1. 以域管理员身份登录到域控制器，然后打开 Active Directory 用户和计算机 "。
1. 查找运行 Azure AD 代理连接器的计算机。 在此示例中，它是 SharePoint 服务器本身。
1. 双击计算机，然后选择 "**委派**" 选项卡。
1. 请确保 "委派选项" 设置为 **"仅信任此计算机来委派指定的服务"** 。 然后，选择“使用任意身份验证协议”。
1. 选择 "**添加**" 按钮，选择 "**用户或计算机**"，然后找到 SharePoint 应用程序池帐户。 例如：`Contoso\spapppool`。
1. 在 SPN 列表中，选择此前为服务帐户创建的 SPN。
1. 选择 **"确定"** ，然后再次选择 **"确定"** 以保存所做的更改。
  
   ![委派设置](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

现在，你可以使用外部 URL 并使用 Azure 进行身份验证，从而登录到 SharePoint。

## <a name="troubleshoot-sign-in-errors"></a>排查登录错误

如果无法登录到站点，则可以在连接器日志中获取有关该问题的详细信息：从运行连接器的计算机上，打开事件查看器，转到 "**应用程序和服务日志**"  > **Microsoft**  > **AadApplicationProxy**  > **连接器**，并检查**管理**日志。

## <a name="next-steps"></a>后续步骤

* [使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)
