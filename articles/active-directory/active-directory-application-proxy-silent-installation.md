---
title: "如何以无提示方式安装 Azure AD 应用程序代理连接器 | Microsoft Docs"
description: "介绍如何执行 Azure AD 应用程序代理连接器的无提示安装，以提供本地应用的安全远程访问权限。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fe96fb2159a7d0dba0ad391d25f38f79cf8aeeb3


---
# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>如何以无提示方式安装 Azure AD 应用程序代理连接器
你希望能够将安装脚本发送到多个 Windows 服务器或未启用用户界面的 Windows Servers。 本主题介绍如何创建使无人参与安装可以安装和注册 Azure AD 应用程序代理连接器的 Windows PowerShell 脚本。

## <a name="enabling-access"></a>启用访问
应用程序代理的工作原理是通过在网络内部安装一个名为“连接器”的精简 Windows Server 服务。 若要使应用程序代理连接器工作，必须使用全局管理员和密码将其注册到 Azure AD 目录。 通常这在弹出窗口对话框中的连接器安装期间输入。 或者，可以使用 Windows PowerShell 创建用于输入注册信息的凭据对象，或创建自己的令牌，使用它输入注册信息。

## <a name="step-1--install-the-connector-without-registration"></a>步骤 1：安装连接器而不注册
安装 Connector MSI 而不注册连接器，如下所示：

1. 打开命令提示符。
2. 运行以下命令，其中 /q 表示静默安装，即安装不会提示用户接受最终用户许可协议。
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>步骤 2：向 Azure Active Directory 注册连接器
这可以使用以下方法之一完成：

* 使用 Windows PowerShell 凭据对象注册连接器
* 使用离线创建的令牌注册连接器

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>使用 Windows PowerShell 凭据对象注册连接器
1. 通过运行以下命令创建 Windows PowerShell 凭据对象，其中“<username>”和“<password>”应替换为目录的用户名和密码：
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. 转到 **C:\Program Files\Microsoft AAD App Proxy Connector** 并使用创建的 PowerShell 凭据对象运行脚本，其中 $cred 是创建的 PowerShell 凭据对象名称：
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>使用离线创建的令牌注册连接器
1. 通过 AuthenticationContext 类使用代码片段中的值创建离线令牌：

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





1. 拥有令牌后，使用令牌创建 SecureString： <br>
   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
2. 运行以下 Windows PowerShell 命令，其中 SecureToken 是之前创建的令牌名称，tenantID 是租户的 GUID： <br>
   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="see-also"></a>另请参阅
* [启用 Azure Active Directory 的应用程序代理](active-directory-application-proxy-enable.md)
* [使用你自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)

有关最新新闻和更新，请参阅 [应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Dec16_HO5-->


