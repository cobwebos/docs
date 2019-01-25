---
title: 通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问 | Microsoft 文档
description: 概要介绍如何将本地 SharePoint 服务器与 Azure AD 应用程序代理相集成。
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: a240e629c5c8f6e43a4c3c52668fcb2b811989b2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468380"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问

本文介绍如何将本地 SharePoint 服务器与 Azure Active Directory (Azure AD) 应用程序代理相集成。

若要通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问，请一步步完成本文各部分的操作。

## <a name="prerequisites"></a>先决条件

本文假设已在环境中安装 SharePoint 2013 或更高版本。 此外，请注意以下先决条件：

* SharePoint 包含本机 Kerberos 支持。 因此，通过 Azure AD 应用程序代理远程访问内部站点的用户预期可以获得单一登录 (SSO) 体验。

* 此方案包括对 SharePoint 服务器进行配置更改。 建议使用过渡环境。 这样，便可以先对过渡服务器进行更新，在转到生产环境之前简化测试周期。

* 我们要求在已发布的 URL 上使用 SSL。 需要在内部 URL 上使用 SSL，确保正确发送/映射链接。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>步骤 1：配置 Kerberos 约束委派 (KCD)

对于使用 Windows 身份验证的本地应用程序，可以通过 Kerberos 身份验证协议以及一项称为 Kerberos 约束委派 (KCD) 的功能来实现单一登录 SSO。 KCD 在经过配置后，可让应用程序代理连接器获取用户的 Windows 令牌，即使该用户尚未直接登录到 Windows。 若要了解有关 KCD 的详细信息，请参阅 [Kerberos 约束委派概述](https://technet.microsoft.com/library/jj553400.aspx)。

若要为 SharePoint 服务器设置 KCD，请完成后面按顺序标记的部分中的步骤：

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>确保 SharePoint Web 应用程序在某个域帐户下运行

首先，请确保 SharePoint Web 应用程序在域帐户下运行，而不是在本地系统、本地服务或网络服务下运行。 这样做是为了能够将服务主体名称 (SPN) 附加到此帐户。 Kerberos 协议使用 SPN 来识别不同的服务。 稍后需要使用该帐户来配置 KCD。

> [!NOTE]
需要为服务预先创建一个 Azure AD 帐户。 建议允许自动密码更改。 有关整套步骤和排查问题的详细信息，请参阅[在 SharePoint 中配置自动密码更改](https://technet.microsoft.com/library/ff724280.aspx)。

若要确保站点在定义的服务帐户下运行，请执行以下步骤：

1. 打开 **SharePoint 管理中心**站点。
2. 转到“安全”并选择“配置服务帐户”。
3. 选择“Web 应用程序池 - SharePoint - 80”。 这些选项可能因 Web 池名称的不同或者 Web 池是否默认使用 SSL 而略有不同。

  ![用于配置服务帐户的选项](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. 如果“为该组件选择帐户”字段设置为“本地服务”或“网络服务”，则需创建一个帐户。 否则，此步骤即告完成，可以转到下一部分。
5. 选择“注册新的托管帐户”。 创建帐户后，必须先设置“Web 应用程序池”，才能使用该帐户。

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>为 SharePoint 服务帐户设置服务主体名称

在配置 KCD 之前，需执行以下操作：

* 标识域帐户，该帐户运行 Azure AD 代理会公开的 SharePoint Web 应用程序。
* 选择一个内部 URL，以便在 Azure AD 代理和 SharePoint 中对其进行配置。 此内部 URL 不得已在 Web 应用程序中使用过，也不会出现在 Web 浏览器中。

假定所选内部 URL 为 <https://sharepoint>，则 SPN 为：

```
HTTP/SharePoint
```

> [!NOTE]
> 对于内部 URL，请遵循以下建议：
> * 使用 HTTPS
> * 请勿使用自定义端口
> * 在 DNS 中创建一个主机 (A)，使之指向 SharePoint WFE（或负载均衡器）而不是别名 (CName)

若要注册此 SPN，请在命令提示符下以域管理员的身份运行以下命令：

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

此命令为 SharePoint 应用程序池帐户 _demo\spAppPoolAccount_ 设置 SPN _HTTP/SharePoint_。

请将 _HTTP/SharePoint_ 替换为内部 URL 的 SPN，将 _demo\spAppPoolAccount_ 替换为环境中的应用程序池帐户。 Setspn 命令会先搜索 SPN，再添加它。 如果它已经存在，则会出现“SPN 值重复”错误。 在这种情况下，如果现有的 SPN 未在正确的应用程序池帐户下设置，则可考虑将其删除。

可以结合 -L 选项运行 Setspn 命令，来验证是否已添加 SPN。 若要详细了解该命令，请参阅 [Setspn](https://technet.microsoft.com/library/cc731241.aspx)。

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>请确保连接器是受信任的，可以委托到已添加到 SharePoint 应用程序池帐户的 SPN

配置 KCD，使 Azure AD 应用程序代理服务能够向 SharePoint 应用程序池帐户委托用户标识。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器会将上下文传递给目标应用程序（在本例中为 SharePoint）。

若要配置 KCD，请针对每个连接器计算机重复以下步骤：

1. 以域管理员的身份登录到 DC，并打开“Active Directory 用户和计算机”。
2. 找到运行连接器的计算机。 在本示例中，它是同一台 SharePoint 服务器。
3. 双击该计算机，并单击“委派”选项卡。
4. 确保委派设置指定为“仅信任此计算机来委派指定的服务”。 然后，选择“使用任意身份验证协议”。
5. 单击“添加”按钮，然后单击“用户或计算机”并找到 SharePoint 应用程序池帐户，例如 _demo\spAppPoolAccount_。
6. 在 SPN 列表中，选择此前为服务帐户创建的 SPN。
7. 单击“确定”。 再次单击“确定”保存更改。
  
  ![委派设置](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>步骤 2：配置 Azure AD 代理

配置了 KCD 后，即可配置 Azure AD 应用程序代理。

1. 使用以下设置发布 SharePoint 网站。 有关分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)。
   * **内部 URL**：以前选择的 SharePoint 内部 URL，例如 **<https://SharePoint/>**。
   * **预身份验证方法**：Azure Active Directory
   * **转换标头中的 URL**：否

   >[!TIP]
   >SharePoint 使用_主机标头_ 值来查找站点， 并根据该值生成链接。 这样做的实质影响是 SharePoint 生成的任何链接是已正确设置为使用外部 URL 的已发布 URL。 将此值设置为“是”还能让连接器将请求转发到后端应用程序。 但是，将此值设置为“否”意味着连接器不会向后端应用程序发送内部主机名， 而是发送主机标头充当已发布 URL。

   ![发布 SharePoint 应用程序](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. 发布应用后，请执行以下步骤来配置单一登录设置：

   1. 在门户中的应用程序页上，选择“单一登录”。
   2. 对于单一登录模式，选择“集成 Windows 身份验证”。
   3. 将“内部应用程序 SPN”设置为此前设置的值。 此示例中为 **HTTP/SharePoint**。
   4. 在“委托的登录标识”中，选择“本地 SAM 帐户名”。

   ![为 SSO 配置集成 Windows 身份验证](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. 要完成应用程序设置，请转到“用户和组”部分，分配要访问此应用程序的用户。 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>步骤 3：将 SharePoint 配置为使用 Kerberos 和 Azure AD 代理 URL

下一步是将 SharePoint Web 应用程序扩展到新的区域，该区域配置了 Kerberos 和相应的备用访问映射，允许 SharePoint 处理发送到内部 URL 的传入请求，并使用为外部 URL 生成的链接进行响应。

1. 启动 **SharePoint 命令行管理程序**。
2. 运行以下脚本，以便将 Web 应用程序扩展到 Extranet 区域并启用 Kerberos 身份验证：

  ```powershell
  # Replace "http://spsites/" with the URL of your web application
  # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
  $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
  Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
  ```

3. 打开 **SharePoint 管理中心**站点。
4. 在“系统设置”下，选择“配置备用访问映射”。 将打开“备用访问映射”框。
5. 选择站点，例如 **SharePoint - 80**。 目前，Extranet 区域尚未正确设置内部 URL：

  ![“备用访问映射”框](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. 单击“添加内部 URL”。
7. 在“URL 协议、主机和端口”文本框中，键入在 Azure AD 代理中配置的“内部 URL”，例如 <https://SharePoint/>。
8. 在下拉列表中选择区域 **Extranet**。
9. 单击“ **保存**”。
10. 备用访问映射现在应如下所示：

  ![正确的备用访问映射](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>步骤 4：确保为 Extranet 区域的 IIS 站点配置 HTTPS 证书

SharePoint 配置现已完成，但由于 Extranet 区域的内部 URL 为 <https://SharePoint/>，因此必须为此站点设置证书。

1. 打开 Windows PowerShell 控制台。
2. 运行以下脚本，以便生成自签名的证书并将其添加到计算机的 MY 存储：

  ```powershell
  # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
  New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
  ```

  > [!NOTE]
  自签名证书仅适用于测试目的。 在生产环境中，强烈建议改用由证书颁发机构颁发的证书。

3. 打开“Internet Information Services 管理器”控制台。
4. 在树视图中展开服务器，展开“站点”，选择“SharePoint - AAD 代理”站点，然后单击“绑定”。
5. 选择 HTTPS 绑定，然后单击“编辑...”。
6. 在 SSL 证书字段中选择 **SharePoint** 证书，然后单击“确定”。

现在，可以通过 Azure AD 应用程序代理从外部访问 SharePoint 站点。

## <a name="next-steps"></a>后续步骤

* [使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)
