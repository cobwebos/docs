---
title: "以无提示方式安装 Azure AD 应用代理连接器 | Microsoft Docs"
description: "介绍如何执行 Azure AD 应用程序代理连接器的无提示安装，以提供本地应用的安全远程访问权限。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf3058832ba656a1a18aea194bf4e5ce4e800e76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>为 Azure AD 应用程序代理连接器创建无人参与安装脚本

本主题可帮助创建 Windows PowerShell 脚本来无人参与安装和注册 Azure AD 应用程序代理连接器。

希望执行以下操作时，此功能非常有用：

* 在未启用用户界面或无法使用远程桌面进行访问的 Windows 服务器上安装连接器。
* 一次性安装并注册多个连接器。
* 将连接器安装与注册集成为另一个过程的一部分。
* 创建包含连接器代码但未注册的标准服务器映像。

要使[应用程序代理连接器](application-proxy-understand-connectors.md)工作，必须使用全局管理员和密码将其注册到 Azure AD 目录。 通常在连接器安装期间出现弹出窗口对话框时输入此信息，但是可改为使用 PowerShell 自动执行此过程。

无人参与安装包括两个步骤。 第一步，安装连接器。 第二步，向 Azure AD 注册连接器。 

## <a name="install-the-connector"></a>安装连接器
使用以下步骤免注册安装连接器：

1. 打开命令提示符。
2. 运行以下命令，其中 /q 表示静默安装。 静默安装不会提示接受最终用户许可协议。
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>向 Azure AD 注册连接器
可以使用两种方法注册连接器：

* 使用 Windows PowerShell 凭据对象注册连接器
* 使用离线创建的令牌注册连接器

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>使用 Windows PowerShell 凭据对象注册连接器
1. 创建包含目录管理用户名和密码的 Windows PowerShell 凭据对象 `$cred`。 运行以下命令，替换 \<用户名\> 和 \<密码\>：
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. 转到 C:\Program Files\Microsoft AAD App Proxy Connector 并使用创建的 `$cred` 对象运行以下脚本：
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>使用离线创建的令牌注册连接器
1. 通过 AuthenticationContext 类使用此代码片段中的值创建离线令牌：

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

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


2. 拥有令牌后，使用令牌创建 SecureString：

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. 运行以下 Windows PowerShell 命令，将 \<tenant GUID\> 替换为目录 ID：

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>后续步骤 
* [使用自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)
* [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
* [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)


