---
title: 启用对 SharePoint 的远程访问 - Azure AD 应用程序代理
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
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481290"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问

此分步指南介绍了如何将本地 SharePoint 服务器场与 Azure 活动目录 （Azure AD） 应用程序代理集成。

## <a name="prerequisites"></a>先决条件

要执行配置，您需要以下资源：
- A SharePoint 2013 服务器场或更新。
- 具有包含应用程序代理的计划的 Azure AD 租户。 详细了解[Azure AD 计划和定价](https://azure.microsoft.com/pricing/details/active-directory/)。
- Azure AD 租户中的[自定义已验证域](../fundamentals/add-custom-domain.md)。
- 本地活动目录与 Azure AD 连接同步，用户可以通过该目录[登录到 Azure](../hybrid/plan-connect-user-signin.md)。
- 在公司域中的计算机上安装和运行的应用程序代理连接器。

使用应用程序代理配置共享点需要两个 URL：
- 外部 URL，对最终用户可见，并在 Azure AD 中确定。 此 URL 可以使用自定义域。 了解有关在[Azure AD 应用程序代理 中使用自定义域的更多详细信息](application-proxy-configure-custom-domain.md)。
- 内部 URL，仅在公司域中知道，从不直接使用。

> [!IMPORTANT]
> 要确保链接映射正确，请按照以下建议进行内部 URL：
> - “使用 HTTPS”。
> - 不要使用自定义端口。
> - 在公司域名系统 （DNS） 中，创建主机 （A） 以指向 SharePoint WFE（或负载均衡器），而不是别名 （CName）。

本文使用以下值：
- 内部 URL：`https://sharepoint`
- 外部 URL：`https://spsites-demo1984.msappproxy.net/`
- SharePoint Web 应用程序的应用程序池帐户：`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>第 1 步：在 Azure AD 中配置使用应用程序代理的应用程序

在此步骤中，您将在 Azure 活动目录租户中创建使用应用程序代理的应用程序。 设置外部 URL 并指定内部 URL，这两个 URL 稍后在 SharePoint 中使用。

1. 创建应用，如以下设置所述。 有关分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   * **内部 URL**：将在 SharePoint 中稍后设置的 SharePoint`https://sharepoint`内部 URL，如 。
   * **预身份验证**： Azure 活动目录
   * **翻译标题中的 URL**：否
   * **翻译应用程序正文中的 URL**：否

   ![发布 SharePoint 应用程序](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 发布应用后，按照以下步骤配置单个登录设置：

   1. 在门户中的应用程序页上，选择“单一登录”****。
   1. 对于**单一登录模式**，请选择**集成 Windows 身份验证**。
   1. 将**内部应用程序 SPN**设置为之前设置的值。 在此示例中，值为`HTTP/sharepoint`。
   1. 在 **"委派登录标识**"下，为活动目录林配置选择最合适的选项。 例如，如果林中具有单个活动目录域，请选择**本地 SAM 帐户名称**（如下图所示）。 但是，如果用户与 SharePoint 和应用程序代理连接器服务器不在同一域中，请选择**本地用户主体名称**（未在屏幕截图中显示）。

   ![为 SSO 配置集成 Windows 身份验证](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 要完成应用程序设置，请转到“用户和组”部分，分配要访问此应用程序的用户****。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>第 2 步：配置 SharePoint Web 应用程序

SharePoint Web 应用程序必须配置 Kerberos 和适当的备用访问映射才能与 Azure AD 应用程序代理正确工作。 有两种可能的选项：

- 创建新的 Web 应用程序，并且仅使用默认区域。 这是首选选项，因为它提供了 SharePoint 的最佳体验（例如，SharePoint 生成的电子邮件警报中的链接始终指向默认区域）。
- 扩展现有 Web 应用程序以在非默认区域中配置 Kerberos。

> [!IMPORTANT]
> 无论使用哪个区域，SharePoint Web 应用程序的应用程序池帐户都必须是 Kerberos 正常工作的域帐户。

### <a name="provision-the-sharepoint-web-application"></a>预配 SharePoint Web 应用程序

- 如果创建新的 Web 应用程序，并且仅使用默认区域（首选选项）：

    1. 启动**SharePoint 管理命令行**并运行以下脚本：

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
    1. 在“系统设置”下，选择“配置备用访问映射”。******** 将打开 **"备用访问映射收集"** 框。
    1. 使用新的 Web 应用程序筛选显示，并确认您看到类似内容：

       ![Web 应用程序的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 如果将现有 Web 应用程序扩展到新区域（如果无法使用默认区域）：

    1. 启动 SharePoint 管理命令行并运行以下脚本：

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
    1. 在“系统设置”下，选择“配置备用访问映射”。******** 将打开 **"备用访问映射收集"** 框。
    1. 使用扩展的 Web 应用程序筛选显示，并确认您看到类似内容：

        ![扩展应用程序的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>确保 SharePoint Web 应用程序在域帐户下运行

要标识运行 SharePoint Web 应用程序应用程序池的帐户并确保它是域帐户，请按照以下步骤操作：

1. 打开 **SharePoint 管理中心**站点。
1. 转到“安全”并选择“配置服务帐户”。********
1. 选择**Web 应用程序池 - 您的 Web 应用程序名称**。

   ![用于配置服务帐户的选项](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 确认**选择此组件的帐户**将返回域帐户，并记住它，因为在下一步中需要它。

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>确保为外网区域的 IIS 站点配置了 HTTPS 证书

由于内部 URL 使用 HTTPS`https://SharePoint/`协议 （），因此必须在 Internet 信息服务 （IIS） 站点上设置证书。

1. 打开 Windows 电源外壳控制台。
1. 运行以下脚本以生成自签名证书并将其添加到计算机的 MY 存储：

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自签名证书仅适用于测试目的。 在生产环境中，我们强烈建议您改用证书颁发机构颁发的证书。

1. 打开互联网信息服务管理器控制台。
1. 在树视图中展开服务器，展开**网站**，选择**SharePoint - AAD 代理**站点，然后选择**绑定**。
1. 选择**https 绑定**，然后选择 **"编辑**"。
1. 在 TLS/SSL 证书字段中，选择**SharePoint**证书，然后选择 **"确定**"。

现在，您可以通过 Azure AD 应用程序代理在外部访问 SharePoint 网站。

## <a name="step-3-configure-kerberos-constrained-delegation"></a>第 3 步：配置 Kerberos 约束委派

用户最初将在 Azure AD 中进行身份验证，然后通过使用 Kerberos 通过 Azure AD 代理连接器进行共享点验证。 要允许连接器代表 Azure AD 用户获取 Kerberos 令牌，必须配置具有协议转换的 Kerberos 约束委派 （KCD）。 要了解有关 KCD 的详细信息，请参阅[Kerberos 约束委派概述](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))。

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>为 SharePoint 服务帐户设置 SPN

在本文中，内部 URL 为`https://sharepoint`，因此服务主体名称 （SPN） 为`HTTP/sharepoint`。 您必须将这些值替换为与环境对应的值。
要为 SharePoint 应用程序池帐户`HTTP/sharepoint``Contoso\spapppool`注册 SPN，请作为域的管理员从命令提示符运行以下命令：

`setspn -S HTTP/sharepoint Contoso\spapppool`

该`Setspn`命令在添加 SPN 之前搜索它。 如果 SPN 已存在，则会看到**重复 SPN 值**错误。 在这种情况下，请考虑删除现有的 SPN，如果它未在正确的应用程序池帐户下设置。 您可以通过使用 -L 选项运行命令来`Setspn`验证 SPN 是否已成功添加。 若要详细了解该命令，请参阅 [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))。

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>确保连接器受信任，以便委派到添加到 SharePoint 应用程序池帐户的 SPN

配置 KCD，使 Azure AD 应用程序代理服务能够向 SharePoint 应用程序池帐户委托用户标识。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器将上下文传递给目标应用程序（本例中为 SharePoint）。

要配置 KCD，请按照以下步骤操作每个连接器计算机：

1. 以域管理员身份登录到域控制器，然后打开活动目录用户和计算机。
1. 查找运行 Azure AD 代理连接器的计算机。 在此示例中，它是 SharePoint 服务器本身。
1. 双击计算机，然后选择"**委派**"选项卡。
1. 请确保委派选项设置为 **"信任此计算机"，以便仅委派到指定的服务**。 然后，选择“使用任意身份验证协议”。****
1. 选择 **"添加**"按钮，选择 **"用户"或"计算机**"，然后查找 SharePoint 应用程序池帐户。 例如：`Contoso\spapppool`。
1. 在 SPN 列表中，选择此前为服务帐户创建的 SPN。
1. 选择 **"确定**"，然后再次选择 **"确定"** 以保存更改。
  
   ![委派设置](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

现在，您可以使用外部 URL 登录到 SharePoint 并使用 Azure 进行身份验证。

## <a name="troubleshoot-sign-in-errors"></a>排除登录错误

如果登录到站点不起作用，您可以在连接器日志中获取有关此问题的详细信息：从运行连接器的计算机中打开事件查看器，转到**应用程序和服务日志** > **Microsoft** > **AadApplicationProxy** > **连接器**，并检查**管理**日志。

## <a name="next-steps"></a>后续步骤

* [使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)
