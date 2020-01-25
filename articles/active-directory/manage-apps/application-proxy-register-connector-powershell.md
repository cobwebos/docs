---
title: Azure AD 앱 프록시 커넥터 자동 설치 | Microsoft Docs
description: Azure AD 애플리케이션 프록시 커넥터를 무인으로 설치하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다.
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
ms.date: 01/23/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca40e9717394690374f5ca289a69e5c22551eb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712008"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Azure AD 애플리케이션 프록시 커넥터에 대한 무인 설치 스크립트 만들기

이 토픽은 Azure AD 애플리케이션 프록시 커넥터에 대한 무인 설치 및 등록을 수행할 수 있도록 Windows PowerShell 스크립트를 만드는 데 도움이 됩니다.

이 기능은 다음을 수행하는 데 유용 합니다.

* 사용자 인터페이스를 사용하도록 설정하지 않은 Windows 서버에 커넥터를 설치하거나 원격 데스크톱을 사용하여 액세스할 수 없습니다.
* 한 번에 여러 커넥터를 설치하고 등록합니다.
* 커넥터 설치 및 등록을 다른 절차의 일부분으로 통합합니다.
* 커넥터 비트를 포함하지만 등록되지 않은 표준 서버 이미지를 만듭니다.

要使[应用程序代理连接器](application-proxy-connectors.md)正常工作，必须使用应用程序管理员和密码将其注册到 Azure AD 的目录。 일반적으로 이러한 정보는 커넥터 설치 중에 팝업 대화 상자에서 입력되지만, 대신 PowerShell을 사용하여 이 프로세스를 자동화할 수도 있습니다.

무인 설치를 위한 두 단계가 있습니다. 먼저 커넥터를 설치합니다. 두 번째, Azure AD에 커넥터를 등록합니다. 

## <a name="install-the-connector"></a>커넥터 설치
등록 없이 커넥터를 설치하려면 다음 단계를 사용합니다.

1. 명령 프롬프트를 엽니다.
2. 다음 명령을 실행합니다. /q는 자동 설치를 의미합니다. 자동 설치는 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Azure AD에 커넥터 등록
커넥터를 등록하는 데 사용할 수 있는 두 가지 방법이 있습니다.

* Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록
* 오프라인에서 만든 토큰을 사용하여 커넥터 등록

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록
1. 디렉터리에 대한 관리 사용자 이름 및 암호를 포함하는 Windows PowerShell 자격 증명 개체 `$cred`를 만듭니다. 다음 명령에서 *\<username\>* 및 *\<암호\>* 를 바꿔서 실행합니다.
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. **C:\Program Files\Microsoft AAD App Proxy Connector**로 이동하여 사용자가 만든 `$cred` 개체를 사용하여 다음 스크립트를 실행합니다.
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>오프라인에서 만든 토큰을 사용하여 커넥터 등록
1. 이 코드 조각의 값 또는 아래의 PowerShell cmdlet을 이용하여 AuthenticationContext 클래스를 사용하는 오프라인 토큰을 만듭니다.

    **C# 사용:**

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
        static readonly Uri ConnectorRedirectAddress = new Uri("https://login.microsoftonline.com/common/oauth2/nativeclient");

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

    **PowerShell 사용:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "https://login.microsoftonline.com/common/oauth2/nativeclient" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. 토큰을 만들었으면 토큰을 사용하여 SecureString을 만듭니다.

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. 다음 Windows PowerShell 명령을 실행하여 \<테넌트 GUID\>를 디렉터리 ID로 대체합니다.

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>다음 단계 
* [고유한 도메인 이름을 사용하여 애플리케이션 게시](application-proxy-configure-custom-domain.md)
* [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
* [애플리케이션 프록시에서 발생한 문제 해결](application-proxy-troubleshoot.md)


