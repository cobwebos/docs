---
title: 以无提示方式安装 Azure AD 应用代理连接器 | Microsoft Docs
description: 介绍如何执行 Azure AD 应用程序代理连接器的无提示安装，以提供本地应用的安全远程访问权限。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5fc1b84e624828d7feb64bd53e8fe8ffff2a7ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88054828"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>为 Azure AD 应用程序代理连接器创建无人参与安装脚本

本主题可帮助创建 Windows PowerShell 脚本来无人参与安装和注册 Azure AD 应用程序代理连接器。

希望执行以下操作时，此功能非常有用：

* 在未启用用户界面或无法使用远程桌面进行访问的 Windows 服务器上安装连接器。
* 一次性安装并注册多个连接器。
* 将连接器安装与注册集成为另一个过程的一部分。
* 创建包含连接器代码但未注册的标准服务器映像。

要使 [应用程序代理连接器](application-proxy-connectors.md) 正常工作，必须使用应用程序管理员和密码将其注册到 Azure AD 的目录。 通常在连接器安装期间出现弹出窗口对话框时输入此信息，但是可改为使用 PowerShell 自动执行此过程。

无人参与安装包括两个步骤。 第一步，安装连接器。 第二步，向 Azure AD 注册连接器。

> [!IMPORTANT]
> 如果要为 Azure 政府云安装连接器，请查看 [先决条件](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#allow-access-to-urls) 和 [安装步骤](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-government-cloud#install-the-agent-for-the-azure-government-cloud)。 这需要启用对一组不同 URL 的访问以及运行安装的其他参数。

## <a name="install-the-connector"></a>安装连接器
使用以下步骤免注册安装连接器：

1. 打开命令提示符。
2. 运行以下命令，其中 /q 表示静默安装。 静默安装不会提示接受最终用户许可协议。

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>向 Azure AD 注册连接器
可以使用两种方法注册连接器：

* 使用 Windows PowerShell 凭据对象注册连接器
* 使用离线创建的令牌注册连接器

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>使用 Windows PowerShell 凭据对象注册连接器
1. 创建包含目录管理用户名和密码的 Windows PowerShell 凭据对象 `$cred`。 运行以下命令，并替换 *\<username\>* 和 *\<password\>* ：

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. 转到 C:\Program Files\Microsoft AAD App Proxy Connector 并使用创建的 `$cred` 对象运行以下脚本****：

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>使用离线创建的令牌注册连接器
1. 通过 AuthenticationContext 类使用以下代码片段或 PowerShell cmdlet 中的值创建离线令牌：

   **使用 c #：**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **使用 PowerShell：**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. 拥有令牌后，使用令牌创建 SecureString：

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. 运行以下 Windows PowerShell 命令， \<tenant GUID\> 并将替换为你的目录 ID：

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>后续步骤
* [使用自己的域名发布应用程序](application-proxy-configure-custom-domain.md)
* [启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
* [解决使用应用程序代理时遇到的问题](application-proxy-troubleshoot.md)
