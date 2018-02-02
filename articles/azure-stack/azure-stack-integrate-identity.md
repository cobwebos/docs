---
title: "Azure 堆栈数据中心集成的标识"
description: "了解如何将与你的数据中心 AD FS 集成 Azure 堆栈 AD FS"
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 2f15e130859272a729fb0ad6e0b718d4724f2103
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Azure 堆栈数据中心集成的标识
你可以部署 Azure 堆栈使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识提供程序。 在部署 Azure 堆栈之前，必须做出的选择。 使用 AD FS 部署也被称为在断开模式中部署 Azure 堆栈。

下表显示两个标识选项之间的差异：

||以物理方式断开连接|以物理方式连接|
|---------|---------|---------|
|计费|必须是容量<br> 仅企业协议 (EA)|容量或作为你的使用付费<br>EA 或云解决方案提供商 (CSP)|
|标识|必须是 AD FS|Azure AD 或 AD FS|
|应用商店联合|支持<br>BYOL 许可|支持<br>BYOL 许可|
|注册|建议，需要可移动媒体<br> 和单独的连接的设备。|自动|
|修补程序和更新|必需，这需要可移动媒体<br> 和单独的连接的设备。|可以直接下载更新包<br> 从 Internet 到 Azure 的堆栈。|

> [!IMPORTANT]
> 无需重新部署整个 Azure 堆栈解决方案，你无法切换标识提供程序。

## <a name="active-directory-federation-services-and-graph"></a>Active Directory 联合身份验证服务和关系图

使用 AD FS 部署允许在现有 Active Directory 林中使用 Azure 堆栈中的资源进行身份验证的身份。 此现有的 Active Directory 林需要 AD FS 以允许 AD FS 联合身份验证信任创建的部署。

身份验证是一个标识的一部分。 若要管理角色基于访问控制 (RBAC) Azure 堆栈中，必须配置 Graph 组件。 委派对资源的访问后，图组件查找中使用 LDAP 协议的现有 Active Directory 林中的用户帐户。

![Azure AD FS 堆栈体系结构](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

现有的 AD FS 是帐户安全令牌服务 (STS) 发送到 Azure 堆栈 AD FS （资源 STS） 的声明。 在 Azure 堆栈自动化创建声明提供方信任与元数据终结点为现有的 AD FS。

在现有的 AD FS，必须配置一个信赖方信任。 此步骤不由自动化，而且必须由运算符配置。 Azure 堆栈元数据终结点通过运行命令记录在 AzureStackStampDeploymentInfo.JSON 文件中，或通过特权终结点`Get-AzureStackInfo`。

信赖方信任配置还要求你配置由 Microsoft 提供的声明转换规则。

有关关系图的配置，服务帐户必须是假设已读取现有的 Active Directory 中的权限。 则需要此帐户作为用于自动化以启用 RBAC 方案的输入。

作为最后一步中，为默认提供程序订阅配置了新的所有者。 此帐户具有完全访问权限时登录到 Azure 堆栈管理员门户中的所有资源。

要求：


|组件|要求|
|---------|---------|
|图形|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>设置关系图的集成

以下信息是必需作为自动化参数输入：


|参数|说明|示例|
|---------|---------|---------|
|CustomADGlobalCatalog|目标的 Active Directory 林的 FQDN<br>你想要与集成|Contoso.com|
|CustomADAdminCredentials|具有 LDAP 读取权限的用户|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>在现有 Active Directory 中创建用户帐户 （可选）

或者，您可以在现有 Active Directory 中创建用于 Graph 服务的帐户。 如果你还没有你想要使用的帐户，请执行此步骤。

1. 在现有 Active Directory 中，创建以下用户帐户 （建议）：
   - **用户名**: graphservice
   - **密码**： 使用强密码<br>配置为永不过期的密码。

   没有特殊权限或成员身份是必需的。

#### <a name="trigger-automation-to-configure-graph"></a>若要配置关系图的触发器自动化

对于此过程，在数据中心网络，可与 Azure 堆栈中的特权终结点进行通信中使用的计算机。

2. 打开提升的 Windows PowerShell 会话 （以管理员身份运行），并连接到特权终结点的 IP 地址。 使用的凭据**CloudAdmin**进行身份验证。

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. 现在，你已连接到特权的终结点，运行以下命令： 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   出现提示时，指定你想要用于 Graph 服务 （如 graphservice) 的用户帐户的凭据。

   > [!IMPORTANT]
   > 等待弹出的凭据 （Get-credential 不支持特权终结点中），然后输入 Graph 服务帐户凭据。

#### <a name="graph-protocols-and-ports"></a>Graph 协议和端口

Azure 堆栈中的 graph 服务使用以下协议和端口与目标 Active Directory 进行通信：

|Type|端口|协议|
|---------|---------|---------|
|LDAP|389|TCP 和 UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>设置通过下载联合元数据的 AD FS 的集成

以下信息是必需的作为输入的自动化参数：

|参数|说明|示例|
|---------|---------|---------|
|CustomAdfsName|声明提供程序的名称。<cr>似乎在 AD FS 登录页上通过这种方式。|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|联合身份验证元数据链接|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>触发器的自动化功能来配置 Azure 堆栈中的声明提供方信任

对于此过程，将可以进行通信的计算机使用 Azure 堆栈中的特权终结点。 应通过帐户使用的证书**STS AD FS**受 Azure 堆栈。

1. 打开提升的 Windows PowerShell 会话，并连接到特权终结点。

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 现在，你已连接到特权的终结点，运行以下命令使用适用于你环境的参数：

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. 运行以下命令以更新订阅的所有者默认提供程序，使用适用于你环境的参数：

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>设置通过提供联合身份验证元数据文件的 AD FS 的集成

如果以下条件之一为真，则使用此方法：

- 证书链，则为 AD FS 与 Azure 堆栈中的所有其他终结点不同。
- 没有网络连接到现有的 AD FS 服务器从 Azure 堆栈的 AD FS 实例。

以下信息是必需的作为输入的自动化参数：


|参数|说明|示例|
|---------|---------|---------|
|CustomAdfsName|声明提供程序的名称。 它将显示在 AD FS 登录页上通过这种方式。|Contoso|
|CustomADFSFederationMetadataFile|联合身份验证元数据文件|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>创建联合身份验证元数据文件

下面的过程中，你必须使用已通过网络连接到现有的 AD FS 部署成为 STS 的帐户的计算机。 此外，必须安装所需的证书。

1. 打开提升的 Windows PowerShell 会话，并运行以下命令，使用适用于你环境的参数：

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. 将元数据文件复制到可从特权终结点进行访问的共享。


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>触发器的自动化功能来配置 Azure 堆栈中的声明提供方信任

对于此过程，将可以进行通信的计算机使用 Azure 堆栈中的特权终结点。

1. 打开提升的 Windows PowerShell 会话，并连接到特权终结点。

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 现在，你已连接到特权的终结点，运行以下命令使用适用于你环境的参数：

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. 运行以下命令以更新订阅的所有者默认提供程序，使用适用于你环境的参数：

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>在现有 AD FS 部署 （帐户 STS） 上配置信赖方

Microsoft 提供了配置信赖方信任，包括声明转换规则的脚本。 使用脚本为可选的因为你可以手动运行命令。

你可以下载中的帮助程序脚本[Azure 堆栈工具](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity)Github 上。

如果你决定要手动运行的命令，请按照下列步骤：

1. 数据中心的 AD FS 实例或服务器场成员上，将以下内容复制到一个.txt 文件 （例如，另存为 c:\ClaimRules.txt）：

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. 若要启用基于 Windows 窗体的身份验证，请打开 Windows PowerShell 会话以提升的用户，并运行以下命令：

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. 若要添加的信赖方信任，请在 AD FS 实例或服务器场成员上运行以下 Windows PowerShell 命令。 请确保更新 AD FS 终结点，并指向在步骤 1 中创建的文件。

   **为 AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **AD FS 2012/2012 r2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > 必须使用 AD FS MMC 管理单元时使用 Windows Server 2012 或 2012 R2 AD FS 配置颁发授权规则。

4. 如果你使用 Internet Explorer 或 Edge 浏览器来访问 Azure 堆栈，你必须忽略令牌绑定。 否则，在登录尝试失败。 在 AD FS 实例或服务器场成员上，运行以下命令：

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. 若要启用刷新令牌，打开提升的 Windows PowerShell 会话，并运行以下命令：

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>SPN 创建

有许多需要身份验证服务主体名称 (SPN) 的使用方案。 以下是一些示例：

- CLI 用法和 Azure 堆栈的 AD FS 部署
- 当使用 AD FS 部署的 Azure 堆栈的 system Center 管理包
- 当使用 AD FS 部署的 Azure 堆栈中的资源提供程序
- 各种应用程序
- 需要非交互式登录

有关创建 SPN 的详细信息，请参阅[为 AD FS 创建服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs)。


## <a name="troubleshooting"></a>故障排除

### <a name="configuration-rollback"></a>配置回滚

如果发生错误将环境保留在其中你可以不再进行身份验证的状态，则回滚选项是可用。

1. 打开提升的 Windows PowerShell 会话并运行以下命令：

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 然后运行以下 cmdlet:

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   运行后的回滚操作，所有配置更改将都回滚。 仅利用内置的身份验证**CloudAdmin**一点的用户。

   > [!IMPORTANT]
   > 你必须配置默认提供程序订阅的原始所有者

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>收集其他日志

如果任何 cmdlet 失败，则可以通过使用收集其他日志`Get-Azurestacklogs`cmdlet。

1. 打开提升的 Windows PowerShell 会话，并运行以下命令：

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 然后，运行以下 cmdlet:

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>后续步骤

[注册 Azure 堆栈](azure-stack-registration.md)
