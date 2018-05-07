---
title: Azure Stack 数据中心集成 - 标识
description: 了解如何将 Azure Stack AD FS 与数据中心 AD FS 集成
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: dc6c8ef2953b7495c734ec8b16530cdd812ac792
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Azure Stack 数据中心集成 - 标识
可以使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识提供者来部署 Azure Stack。 必须在部署 Azure Stack 之前做出选择。 使用 AD FS 的部署也称为在断开连接模式下部署 Azure Stack。

下表显示了这两个标识之间的差别：

||从 Internet 断开连接|连接到 Internet|
|---------|---------|---------|
|计费|必须是“容量”<br> 仅限企业协议 (EA)|“容量”或“即用即付”<br>“EA”或“云解决方案提供商”(CSP)|
|标识|必须是“AD FS”|“Azure AD”或“AD FS”|
|Marketplace 联合|支持<br>BYOL 许可|支持<br>BYOL 许可|
|注册|建议选项，需要使用可移动媒体<br> 和独立的连接设备。|自动|
|修补和更新|必需选项，需要使用可移动媒体<br> 和独立的连接设备。|可以直接从 Internet<br> 将更新包下载到 Azure Stack。|

> [!IMPORTANT]
> 如果不重新部署整个 Azure Stack 解决方案，则无法切换标识提供者。

## <a name="active-directory-federation-services-and-graph"></a>Active Directory 联合身份验证服务和 Graph

使用 AD FS 进行部署可让现有 Active Directory 林中的标识对 Azure Stack 中的资源进行身份验证。 此现有 Active Directory 林需要 AD FS 的部署，以便能够创建 AD FS 联合信任。

身份验证是标识的一部分。 若要在 Azure Stack 中管理基于角色的访问控制 (RBAC)，必须配置 Graph 组件。 委托资源的访问权限后，Graph 组件使用 LDAP 协议来查找现有 Active Directory 林中的用户帐户。

![Azure Stack AD FS 体系结构](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

现有 AD FS 是将声明发送到 Azure Stack AD FS（资源 STS）的帐户安全令牌服务 (STS)。 在 Azure Stack 中，自动化功能将与现有 AD FS 的元数据终结点建立声明提供程序信任关系。

在现有 AD FS 中，必须配置信赖方信任。 此步骤不是由自动化执行的，而必须由操作员配置。 Azure Stack 元数据终结点在 AzureStackStampDeploymentInfo.JSON 文件中阐述，或者可以运行命令 `Get-AzureStackInfo` 通过特权终结点来阐述。

配置信赖方信任还需要配置 Microsoft 提供的声明转换规则。

对于 Graph 配置，必须提供在现有 Active Directory 中拥有“读取”权限的服务帐户。 自动化需要使用此帐户作为输入来启用 RBAC 方案。

在最后一个步骤中，将为默认提供商订阅配置新的所有者。 登录到 Azure Stack 管理员门户时，此帐户对所有资源拥有完全访问权限。

要求：


|组件|要求|
|---------|---------|
|图形|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>设置 Graph 集成

关系图仅支持具有单个 Active Directory 林的集成。 如果存在多个林，仅在配置中指定的林将用于提取用户和组。

需要使用以下信息作为自动化参数的输入：


|参数|说明|示例|
|---------|---------|---------|
|CustomADGlobalCatalog|要与之集成的目标 Active Directory<br>林的 FQDN|Contoso.com|
|CustomADAdminCredentials|拥有 LDAP“读取”权限的用户|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>在现有 Active Directory 中创建用户帐户（可选）

可以选择性地在现有 Active Directory 中创建 Graph 服务的帐户。 如果没有可用的帐户，请执行此步骤。

1. 在现有 Active Directory 中创建以下用户帐户（建议）：
   - **用户名**：graphservice
   - **密码**：使用强密码<br>将密码配置为永不过期。

   无需任何特殊权限或成员身份。

#### <a name="trigger-automation-to-configure-graph"></a>触发自动化来配置 Graph

对于此过程，请使用能够与 Azure Stack 中的特权终结点通信的数据中心网络中的计算机。

2. 打开提升了权限的 Windows PowerShell 会话（以管理员身份运行），连接到特权终结点的 IP 地址。 使用 **CloudAdmin** 的凭据进行身份验证。

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. 连接到特权终结点后，运行以下命令： 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   出现提示时，请指定用于 Graph 服务的用户帐户（例如 graphservice）的凭据。 寄存器目录 cmdlet 的输入必须是林名称/根林中域而不是林中的任何其他域。

   > [!IMPORTANT]
   > 等待凭据弹出（特权终结点不支持 Get-Credential），然后输入 Graph 服务帐户凭据。

#### <a name="graph-protocols-and-ports"></a>Graph 协议和端口

Azure 堆栈中的 graph 服务使用以下协议和端口可写的全局编录服务器 (GC) 和密钥分发中心 (KDC) 可以处理在目标的 Active Directory 林的登录请求进行通信。

Azure Stack 中的 Graph 服务使用以下协议和端口来与目标 Active Directory 通信：

|Type|端口|协议|
|---------|---------|---------|
|LDAP|389|TCP 和 UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>通过下载联合元数据来设置 AD FS 集成

以下信息是作为自动化参数的输入所必需的：

|参数|说明|示例|
|---------|---------|---------|
|CustomAdfsName|声明提供程序的名称。<cr>AD FS 登陆页上会显示此名称。|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|联合元数据链接|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>触发自动化以便在 Azure Stack 中配置声明提供程序信任

对于此过程，请使用能够与 Azure Stack 中特权终结点通信的计算机。 Azure Stack 应会信任帐户 **STS AD FS** 使用的证书。

1. 打开权限提升的 Windows PowerShell 会话并连接到特权终结点。

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 连接到特权终结点之后，使用适用于环境的参数运行以下命令：

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. 使用适用于环境的参数运行以下命令，更新默认提供商订阅的所有者：

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>通过提供联合元数据文件来设置 AD FS 集成

如果符合以下任一条件，则可以使用此方法：

- AD FS 的证书链不同于 Azure Stack 中的其他所有终结点。
- 未在 Azure Stack 的 AD FS 实例与现有 AD FS 服务器之间建立网络连接。

以下信息是作为自动化参数的输入所必需的：


|参数|说明|示例|
|---------|---------|---------|
|CustomAdfsName|声明提供程序的名称。 AD FS 登录页上会显示此名称。|Contoso|
|CustomADFSFederationMetadataFile|联合元数据文件|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>创建联合元数据文件

对于以下过程，必须使用与现有 AD FS 部署建立了网络连接的计算机，该计算机将成为帐户 STS。 此外，必须安装所需的证书。

1. 打开权限提升的 Windows PowerShell 会话，并使用适用于环境的参数运行以下命令：

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. 将元数据文件复制到可从特权终结点访问的共享。


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>触发自动化以便在 Azure Stack 中配置声明提供程序信任

对于此过程，请使用能够与 Azure Stack 中特权终结点通信的计算机。

1. 打开权限提升的 Windows PowerShell 会话并连接到特权终结点。

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 连接到特权终结点之后，使用适用于环境的参数运行以下命令：

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. 使用适用于环境的参数运行以下命令，更新默认提供商订阅的所有者：

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>在现有 AD FS 部署上配置信赖方（帐户 STS）

Microsoft 提供了用于配置信赖方信任（包括声明转换规则）的脚本。 不一定要使用此脚本，也可以手动运行命令。

可以从 Github 上的 [Azure Stack 工具](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity)下载帮助器脚本。

如果确定要手动运行命令，请遵循以下步骤：

1. 将以下内容复制到数据中心的 AD FS 实例或场成员上的 .txt 文件中（例如，保存为 c:\ClaimRules.txt）：

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

2. 若要启用基于 Windows 窗体的身份验证，请以权限提升的用户身份打开 Windows PowerShell 会话，并运行以下命令：

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. 若要添加信赖方信任，请在 AD FS 实例或场成员上运行以下 Windows PowerShell 命令。 请务必更新 AD FS 终结点，并指向步骤 1 中创建的文件。

   **对于 AD FS 2016**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **对于 AD FS 2012/2012 R2**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > 使用 Windows Server 2012 或 2012 R2 AD FS 时，必须使用 AD FS MMC 管理单元来配置颁发授权规则。

4. 使用 Internet Explorer 或 Microsoft Edge 浏览器访问 Azure Stack 时，必须忽略令牌绑定。 否则登录尝试会失败。 在 AD FS 实例或场成员上运行以下命令：

   > [!note]  
   > 使用 Windows Server 2012 或 2012 R2 AD FS 时，此步骤不适用。 可以放心跳过此命令并继续集成。

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. 若要启用刷新令牌，请打开权限提升的 Windows PowerShell 会话，并运行以下命令：

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>创建 SPN

在许多情况下，需要使用服务主体名称 (SPN) 进行身份验证。 下面是一些示例：

- 使用 CLI 在 Azure Stack 中部署 AD FS
- 使用 AD FS 部署时的 System Center Management Pack for Azure Stack
- 使用 AD FS 部署时 Azure Stack 中的资源提供程序
- 各种应用程序
- 需要非交互式登录

有关创建 SPN 的详细信息，请参阅[为 AD FS 创建服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs)。


## <a name="troubleshooting"></a>故障排除

### <a name="configuration-rollback"></a>配置回滚

如果发生错误，导致不再能够在环境中进行身份验证，可以使用回滚选项。

1. 打开权限提升的 Windows PowerShell 会话，并运行以下命令：

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 然后运行以下 cmdlet：

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   运行回滚操作后，所有配置更改都会回滚。 只能使用内置的 **CloudAdmin** 用户身份进行身份验证。

   > [!IMPORTANT]
   > 必须配置默认提供商订阅的原始所有者

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>收集其他日志

如果任一 cmdlet 失败，可以使用 `Get-Azurestacklogs` cmdlet 来收集其他日志。

1. 打开权限提升的 Windows PowerShell 会话，并运行以下命令：

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 然后运行以下 cmdlet：

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>后续步骤

[集成外部监视解决方案](azure-stack-integrate-monitor.md)
