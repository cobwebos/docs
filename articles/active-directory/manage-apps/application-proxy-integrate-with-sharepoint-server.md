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
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286088"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问

此分步指南介绍了如何将本地 SharePoint 场与 Azure Active Directory （Azure AD）应用程序代理相集成。

## <a name="prerequisites"></a>先决条件

若要执行配置，需要以下资源：
- SharePoint 2013 场或更高版本。
- 具有包含应用程序代理的计划的 Azure Active Directory 租户。 了解[Azure Active Directory 计划和定价](https://azure.microsoft.com/pricing/details/active-directory/)的详细信息。
- Azure AD 租户中的[自定义验证域](../fundamentals/add-custom-domain.md)。
- 本地 Active Directory 与可以[登录到 Azure](../hybrid/plan-connect-user-signin.md)的用户 Azure AD Connect 同步。
- 在企业域中的计算机上安装并运行的应用程序代理连接器。

用应用程序代理配置 SharePoint 需要两个 Url：
- 外部 URL，对最终用户可见并在 Azure Active Directory 中确定。 此 URL 可以使用自定义域。 详细了解如何使用[Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)。
- 内部 URL，只在企业域中是已知的，不能直接使用。

> [!IMPORTANT]
> 若要确保正确映射链接，请对内部 URL 遵循以下建议：
> - 使用 HTTPS
> - 请勿使用自定义端口
> - 在公司 DNS 中，创建一个指向 SharePoint WFE （或负载均衡器）的主机（A），而不是一个别名（CName）

本文将使用以下值：
- 内部 URL： `https://sharepoint`
- 外部 URL： `https://spsites-demo1984.msappproxy.net/`
- SharePoint web 应用程序的应用程序池帐户： `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>步骤 1：在 Azure AD 中配置使用应用程序代理的应用程序

在此步骤中，会在将使用应用程序代理的 Azure Active Directory 租户中创建应用程序。 你将设置外部 URL，并指定内部 URL，这两个 URL 将在后面的 SharePoint 中使用。

1. 按照以下设置所述创建应用。 有关分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   * **内部 URL**：稍后将在 SharePoint 中设置的 SharePoint 内部 URL，如 `https://sharepoint`。
   * **预身份验证方法**：Azure Active Directory
   * **转换标头中的 URL**：否
   * **转换应用程序主体中的 URL**：否

   ![发布 SharePoint 应用程序](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 发布应用后，请执行以下步骤来配置单一登录设置：

   1. 在门户中的应用程序页上，选择“单一登录”。
   1. 对于**单一登录模式**，请选择 "**集成 Windows 身份验证**"。
   1. 将 "**内部应用程序 SPN** " 设置为之前设置的值。 在此示例中，该值将 `HTTP/sharepoint`。
   1. 在 "**委派的登录标识**" 中，为 Active Directory 林配置选择最适合的选项。 例如，如果林中有单个 AD 域，请选择 **"本地 SAM 帐户名称**" （如下所示），但如果用户与 SharePoint 和应用程序代理连接器服务器不在同一个域中，请选择 **"本地用户主体"名称**（未显示）。

   ![为 SSO 配置集成 Windows 身份验证](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 要完成应用程序设置，请转到“用户和组”部分，分配要访问此应用程序的用户。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>步骤 2：配置 SharePoint web 应用程序

必须使用 Kerberos 和相应的备用访问映射来配置 SharePoint web 应用程序，以便与 Azure AD 应用程序代理正常工作。 有两个可能的选项：

1. 创建新的 web 应用程序并仅使用默认区域。 这是使用 SharePoint 获得最佳体验的首选选项（例如，SharePoint 生成的电子邮件警报中的链接始终指向默认区域）。
1. 扩展现有 web 应用程序以对非默认区域配置 Kerberos。

> [!IMPORTANT]
> 不管将使用的区域如何，SharePoint web 应用程序的应用程序池帐户都必须是域帐户，Kerberos 才能正常工作。

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

    1. 打开 **SharePoint 管理中心**站点。
    1. 在“系统设置”下，选择“配置备用访问映射”。 将打开“备用访问映射”框。
    1. 用新的 web 应用程序筛选显示，并确认您看到如下所示的内容：

       ![Web 应用程序的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 如果将现有 web 应用程序扩展到新区域（以防无法使用默认区域）：

    1. 启动**SharePoint Management Shell**并运行以下脚本：

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. 打开 **SharePoint 管理中心**站点。
    1. 在“系统设置”下，选择“配置备用访问映射”。 将打开“备用访问映射”框。
    1. 用已扩展的 web 应用程序筛选显示，并确认你看到如下所示的内容：

        ![扩展应用程序的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>确保 SharePoint web 应用程序在域帐户下运行

使用以下步骤来确定运行 SharePoint web 应用程序的应用程序池的帐户，并确保该帐户是域帐户：

1. 打开 **SharePoint 管理中心**站点。
1. 转到“安全”并选择“配置服务帐户”。
1. 选择 " **Web 应用程序池-YourWebApplicationName**"。

   ![用于配置服务帐户的选项](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 确认**为此组件选择一个帐户**返回一个域帐户，并记住它，因为下一步需要用到它。

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>确保为 Extranet 区域的 IIS 站点配置 HTTPS 证书

因为内部 URL 使用 HTTPS 协议（`https://SharePoint/`），所以必须在 IIS 站点中设置证书。

1. 打开 Windows PowerShell 控制台。
1. 运行以下脚本，以便生成自签名的证书并将其添加到计算机的 MY 存储：

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自签名证书仅适用于测试目的。 在生产环境中，强烈建议改用由证书颁发机构颁发的证书。

1. 打开“Internet Information Services 管理器”控制台。
1. 在树视图中展开服务器，展开“站点”，选择“SharePoint - AAD 代理”站点，然后单击“绑定”。
1. 选择 HTTPS 绑定，然后单击“编辑...”。
1. 在 SSL 证书字段中选择 **SharePoint** 证书，然后单击“确定”。

现在，可以通过 Azure AD 应用程序代理从外部访问 SharePoint 站点。

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>步骤 3：配置 Kerberos 约束委派 (KCD)

用户首先会在 Azure Active Directory 中进行身份验证，然后使用 Kerberos 通过 Azure AD 代理连接器进行身份验证。 若要允许连接器代表 Azure Active Directory 用户获取 Kerberos 令牌，需要使用协议转换来配置 Kerberos 约束委派。 若要了解有关 KCD 的详细信息，请参阅 [Kerberos 约束委派概述](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))。

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>设置 SharePoint 服务帐户的服务主体名称

在本文中，内部 URL 为 `https://sharepoint`，因此 SPN @no__t 为-1。 需要将这些值替换为与环境对应的值。
若要为 SharePoint 应用程序池 @no__t 帐户注册 SPN `HTTP/sharepoint`，请在命令提示符下以域管理员身份运行以下命令：

`setspn -S HTTP/sharepoint Contoso\spapppool`

Setspn 命令会先搜索 SPN，再添加它。 如果已存在，则会出现 "**重复 SPN 值**" 错误。 在这种情况下，如果未在正确的应用程序池帐户下设置现有 SPN，则请考虑删除该 SPN。  
可以通过运行包含-L 选项的 Setspn 命令来验证 SPN 是否已成功添加。 若要详细了解该命令，请参阅 [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))。

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>请确保连接器是受信任的，可以委托到已添加到 SharePoint 应用程序池帐户的 SPN

配置 KCD，使 Azure AD 应用程序代理服务能够向 SharePoint 应用程序池帐户委托用户标识。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器会将上下文传递给目标应用程序（在本例中为 SharePoint）。

若要配置 KCD，请针对每个连接器计算机重复以下步骤：

1. 以域管理员身份登录到域控制器，然后打开**Active Directory 用户和计算机**"。
1. 查找运行 Azure AD 代理连接器的计算机。 在此示例中，它是 SharePoint 服务器本身。
1. 双击该计算机，并单击“委派”选项卡。
1. 确保委派设置指定为“仅信任此计算机来委派指定的服务”。 然后，选择“使用任意身份验证协议”。
1. 单击 "**添加**" 按钮，单击 "**用户或计算机**"，然后找到 SharePoint 应用程序池帐户，例如 `Contoso\spapppool`。
1. 在 SPN 列表中，选择此前为服务帐户创建的 SPN。
1. 单击 **“确定”** 。 再次单击“确定”保存更改。
  
   ![委派设置](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

现在，你可以使用外部 URL 登录到 SharePoint 并使用 Azure 进行身份验证。

## <a name="troubleshoot-sign-in-errors"></a>排查登录错误

如果登录站点未运行，则可以在连接器日志中获取有关该问题的详细信息：在运行连接器的计算机上，打开 "事件查看器"，中转到 "**应用程序和服务日志**"  > **Microsoft** > **AadApplicationProxy** > **连接器**，然后检查**管理**日志。

## <a name="next-steps"></a>后续步骤

* [使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)
