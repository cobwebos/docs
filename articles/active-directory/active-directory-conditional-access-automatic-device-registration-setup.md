---
title: "如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册 | Microsoft Docs"
description: "以自动静默方式向 Azure Active Directory 注册已加入域的 Windows 设备。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 96fb170e7a079fbb4bcfb4a6b1e98970a709406f
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册

若要使用 [Azure Active Directory 基于设备的条件性访问](active-directory-conditional-access-azure-portal.md)，必须向 Azure Active Directory (Azure AD) 注册计算机。 可以使用 [Azure Active Directory PowerShell 模块](https://docs.microsoft.com/en-us/powershell/msonline/)中的 [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet 获取组织中已注册设备的列表。 

这篇文章提供有关如何在组织中将已加入域的 Windows 设备配置为自动注册到 Azure AD 的步骤。


详细信息：

- 有关条件访问的详细信息，请参阅 [Azure Active Directory 基于设备的条件性访问](active-directory-conditional-access-azure-portal.md)。 
- 有关工作区中的 Windows 10 设备以及向 Azure AD 注册时的增强型体验的详细信息，请参阅[适用于企业的 Windows 10：使用设备进行工作](active-directory-azureadjoin-windows10-devices-overview.md)。


## <a name="before-you-begin"></a>开始之前

开始在环境中为 Windows 已加入域的设备配置自动注册之前，应该熟悉支持的方案和约束。  

为了方便阅读介绍内容，本主题使用了以下术语： 

- **Windows 当前设备** - 此术语指运行 Windows 10 或 Windows Server 2016 的已加入域的设备。
- **Windows 下层设备** - 此术语指既不运行 Windows 10 也不运行 Windows Server 2016 的所有已加入域的**受支持** Windows 设备。  


### <a name="windows-current-devices"></a>Windows 当前设备

- 对于运行 Windows 桌面操作系统的设备，我们建议使用 Windows 10 周年更新（版本 1607）或更高版本。 
- 非联合环境（例如密码哈希同步配置）**支持** Windows 当前设备的注册。  


### <a name="windows-down-level-devices"></a>Windows 下层设备

- 支持以下 Windows 下层设备：
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- 以下各项**不支持** Windows 下层设备的注册：
    - 非联合环境（密码哈希同步配置）。  
    - 使用漫游配置文件的设备。 如果你依赖于配置文件或设置漫游，请使用 Windows 10。



## <a name="prerequisites"></a>先决条件

在组织中开始为已加入域的设备启用自动注册之前，需确保运行最新版本的 Azure AD Connect。

Azure AD Connect：

- 保留本地 Active Directory (AD) 中的计算机帐户与 Azure AD 中的设备对象之间的关联。 
- 启用设备相关的其他功能，例如 Windows Hello for Business。



## <a name="configuration-steps"></a>配置步骤

本主题包含所有典型配置方案所需的步骤。  
在下表中了解方案所需的步骤概述：  



| 步骤                                      | Windows 当前设备与密码哈希同步 | Windows 当前设备与联合 | Windows 下层设备 |
| :--                                        | :-:                                    | :-:                            | :-:                |
| 步骤 1：配置服务连接点 | ![勾选标记][1]                            | ![勾选标记][1]                    | ![勾选标记][1]        |
| 步骤 2：设置声明颁发           |                                        | ![勾选标记][1]                    | ![勾选标记][1]        |
| 步骤 3：启用非 Windows 10 设备      |                                        |                                | ![勾选标记][1]        |
| 步骤 4：控制部署和实施     | ![勾选标记][1]                            | ![勾选标记][1]                    | ![勾选标记][1]        |
| 步骤 5：验证已注册的设备          | ![勾选标记][1]                            | ![勾选标记][1]                    | ![勾选标记][1]        |



## <a name="step-1-configure-service-connection-point"></a>步骤 1：配置服务连接点

在注册过程中，设备使用服务连接点 (SCP) 对象来发现 Azure AD 租户信息。 在本地 Active Directory (AD) 中，用于自动注册已加入域的设备的 SCP 对象必须在计算机林的配置命名上下文分区中存在。 每个林只有一个配置命名上下文。 在多林 Active Directory 配置中，服务连接点必须在包含已加入域的计算机的所有林中存在。

可以使用 [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) cmdlet 来检索林的配置命名上下文。  

对于具有 Active Directory 域名 *fabrikam.com* 的林，配置命名上下文是：

`CN=Configuration,DC=fabrikam,DC=com`

在林中，用于自动注册已加入域的设备的 SCP 对象位于：  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

根据 Azure AD Connect 的部署方式，SCP 对象可能已配置。
可使用以下 Windows PowerShell 脚本验证该对象是否存在并检索发现值： 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$scp.Keywords** 输出显示 Azure AD 租户信息，例如：

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

如果该服务连接点不存在，可在 Azure AD Connect 服务器上运行 `Initialize-ADSyncDomainJoinedComputerSync` cmdlet 来创建它：  
cmdlet：

- 在 Azure AD Connect 连接到的 Active Directory 林中创建服务连接点。 
- 要求指定 `AdConnectorAccount` 参数。 这是在 Azure AD Connect 中配置为 Active Directory 连接器帐户的帐户。 


以下脚本演示了该 cmdlet 的用法示例。 在此脚本中，`$aadAdminCred = Get-Credential` 要求键入用户名。 需要以用户主体名称 (UPN) 格式 (`user@example.com`) 提供用户名。 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` cmdlet 使用 Active Directory PowerShell 模块，依赖于域控制器中运行的 Active Directory Web 服务。 运行 Windows Server 2008 R2 和更高版本的域控制器支持 Active Directory Web 服务。 

对于运行 Windows Server 2008 或更低版本的域控制器，可使用以下脚本创建服务连接点。

在多林配置中，应使用以下脚本在计算机所在的每个林中创建服务连接点：
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()


## <a name="step-2-setup-issuance-of-claims"></a>步骤 2：设置声明颁发

在联合 Azure AD 配置中，设备依赖于 Active Directory 联合身份验证服务 (AD FS) 或第三方本地联合身份验证服务向 Azure AD 进行身份验证。 设备将执行身份验证，以获取一个访问令牌来针对 Azure Active Directory 设备注册服务 (Azure DRS) 注册。

Windows 当前设备使用 Windows 集成身份验证向本地联合身份验证服务托管的 WS-Trust 活动终结点（版本 1.3 或 2005）进行身份验证。

> [!NOTE]
> 使用 AD FS 时，必须启用 **adfs/services/trust/13/windowstransport** 或 **adfs/services/trust/2005/windowstransport**。 如果使用的是 Web 身份验证代理，还要确保已通过该代理发布了此终结点。 可以通过 AD FS 管理控制台中的“服务”>“终结点”查看已启用哪些终结点。
>
>如果不使用 AD FS 作为本地联合身份验证服务，请遵照供应商的说明，确保设备支持 WS-Trust 1.3 或 2005 终结点，并且已通过元数据交换文件 (MEX) 发布。

若要完成设备注册，Azure DRS 收到的令牌中必须存在以下声明。 Azure DRS 将在 Azure AD 中创建一个包含部分此类信息的设备对象，然后，Azure AD Connect 使用该对象将新建的设备对象与本地计算机帐户相关联。

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

如果有多个已验证的域名，需要提供计算机的以下声明：

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

如果已颁发 ImmutableID 声明（例如备用登录 ID），需要提供计算机的一个对应声明：

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

以下部分介绍：
 
- 每个声明应使用的值
- AD FS 中的定义形式

定义可以帮助验证值是否存在，或者是否需要创建值。

> [!NOTE]
> 如果不对本地联合身份验证服务器使用 AD FS，请遵照供应商的说明创建相应的配置来颁发这些声明。

### <a name="issue-account-type-claim"></a>颁发帐户类型声明

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** - 此声明必须包含值 **DJ**，用于将设备标识为已加入域的计算机。 在 AD FS 中，可以添加如下所示的颁发转换规则：

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>颁发本地计算机帐户的 objectGUID

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** - 此声明必须包含本地计算机帐户的 **objectGUID** 值。 在 AD FS 中，可以添加如下所示的颁发转换规则：

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>颁发本地计算机帐户的 objectSID

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** - 此声明必须包含本地计算机帐户的 **objectSid** 值。 在 AD FS 中，可以添加如下所示的颁发转换规则：

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>当 Azure AD 中存在多个已验证域名时颁发计算机的 issuerID

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** - 此声明必须包含与颁发令牌的本地联合身份验证服务（AD FS 或第三方）相连接的任何已验证域名的统一资源标识符 (URI)。 在 AD FS 中，可以在上述规则的后面，按特定的顺序添加如下所示的颁发转换规则。 请注意，必须创建一条规则来显式为用户颁发规则。 在下面的规则中，添加了用于标识用户与计算机身份验证的第一条规则。

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


在上述声明中，

- `$<domain>` 是 AD FS 服务 URL
- `<verified-domain-name>` 是一个占位符，需要将其替换为 Azure AD 中已验证域名之一



有关已验证的域名的详细信息，请参阅 [Add a custom domain name to Azure Active Directory](active-directory-add-domain.md)（向 Azure Active Directory 添加自定义域名）。  
若要获取已验证的公司域的列表，可以使用 [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain) cmdlet。 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>当用户存在一个 ImmutableID 时颁发计算机的 ImmutableID（例如，已设置备用登录 ID）

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** - 此声明必须包含计算机的有效值。 在 AD FS 中，可按如下所示创建颁发转换规则：

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>用于创建 AD FS 颁发转换规则的帮助器脚本

可以借助以下脚本创建上述颁发转换规则。

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>备注 

- 此脚本将规则追加到现有规则。 请不要运行该脚本两次，否则会添加规则集两次。 再次运行该脚本之前，请确保这些声明没有相应的规则（在相应的条件下）。

- 如果有多个已验证的域名（可通过 Azure AD 门户或 Get-MsolDomains cmdlet 查看），请在脚本中将 **$multipleVerifiedDomainNames** 的值设置为 **$true**。 此外，请确保删除 Azure AD Connect 创建的或通过其他方式创建的所有现有 issuerid 声明。 下面是此规则的一个示例：


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- 如果已颁发用户帐户的 **ImmutableID** 声明，请在脚本中将 **$oneOfVerifiedDomainNames** 的值设置为 **$true**。

## <a name="step-3-enable-windows-down-level-devices"></a>步骤 3：启用 Windows 下层设备

如果某些已加入域的设备是 Windows 下层设备，则你需要：

- 在 Azure AD 中设置策略，使用户能够注册设备。
 
- 配置本地联合身份验证服务来颁发声明，以便支持使用 **Windows 集成身份验证 (IWA)** 进行设备注册。
 
- 将 Azure AD 设备身份验证终结点添加到本地 Intranet 区域，避免对设备进行身份验证时出现证书提示。

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>在 Azure AD 中设置策略，使用户能够注册设备

若要注册 Windows 下层设备，需确保已设置为允许用户在 Azure AD 中注册设备。 在 Azure 门户中，可在以下位置找到此设置：

`Azure Active Directory > Users and groups > Device settings`
    
必须将以下策略设置为“所有”：“用户可以向 Azure AD 注册其设备”

![注册设备](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>配置本地联合身份验证服务 

接收发往 Azure AD 信赖方的身份验证请求时，本地联合身份验证服务必须支持颁发 **authenticationmehod** 和 **wiaormultiauthn** 声明，其中包含具有如下所示编码值的 resouce_params 参数：

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

此类请求传入时，本地联合身份验证服务必须使用 Windows 集成身份验证对用户进行身份验证，成功后，必须颁发以下两个声明：

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

在 AD FS 中，必须添加一个用于传递身份验证方法的颁发转换规则。  

**添加此规则：**

1. 在 AD FS 管理控制台中，转到 `AD FS > Trust Relationships > Relying Party Trusts`。
2. 右键单击“Microsoft Office 365 标识平台”信赖方信任对象，并选择“编辑声明规则”。
3. 在“颁发转换规则”选项卡中，选择“添加规则”。
4. 在“声明规则”模板列表中选择“使用自定义规则发送声明”。
5. 选择“**下一步**”。
6. 在“声明规则名称”框中键入“身份验证方法声明规则”。
7. 在“声明规则”框中键入以下规则：

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. 在联合服务器上，先将 **\<RPObjectName\>** 替换为 Azure AD 信赖方信任对象的信赖方对象名称，然后键入以下 PowerShell 命令。 此对象通常命名为“Microsoft Office 365 标识平台”。
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>将 Azure AD 设备身份验证终结点添加到本地 Intranet 区域

为了避免注册设备中的用户在向 Azure AD 身份验证时出现证书提示，可将一个策略推送到已加入域的设备，以便在 Internet Explorer 中将以下 URL 添加到本地 Intranet 区域：

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>步骤 4：控制部署和实施

完成所需的步骤后，已加入域的设备便可自动注册到 Azure AD。 设备重新启动或用户登录时，运行 Windows 10 周年更新和 Windows Server 2016 的所有已加入域的设备将自动注册到 Azure AD。 完成域加入操作后重新启动设备时，新设备将注册到 Azure AD。

### <a name="remarks"></a>备注

- 可以使用组策略对象来控制推出已加入域的 Windows 10 和 Windows Server 2016 计算机的自动注册。

- **仅当**已设置实施组策略对象时，Windows 10 November 2015 Update 才会自动注册到 Azure AD。

- 若要实施 Windows 下层计算机的自动注册，可将 [Windows Installer 包](#windows-installer-packages-for-non-windows-10-computers)部署到所选的计算机。

- 如果将组策略对象推送到 Windows 8.1 已加入域的设备，将尝试注册；但是，建议使用 [Windows Installer 包](#windows-installer-packages-for-non-windows-10-computers)来注册所有 Windows 下层设备。 

### <a name="create-a-group-policy-object"></a>创建组策略对象 

若要控制 Windows 当前计算机自动注册的实施，应该将“将已加入域的计算机注册为设备”组策略对象部署到要注册的设备。 例如，可以将策略部署到组织单位或安全组。

**设置策略：**

1. 打开“服务器管理器”，然后转到 `Tools > Group Policy Management`。
2. 转到与要在其中激活 Windows 当前计算机自动注册的域对应的域节点。
3. 右键单击“组策略对象”并选择“新建”。
4. 键入组策略对象的名称。 例如，“自动注册到 Azure AD”。 选择“确定”。
5. 右键单击新建的组策略对象，然后选择“编辑”。
6. 转到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “设备注册”。 右键单击“将已加入域的计算机注册为设备”，然后选择“编辑”。
   
   > [!NOTE]
   > 已从早期版本的组策略管理控制台对该组策略模板进行了重命名。 如果使用早期版本的控制台，请转到 `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`。 

7. 选择“已启用”，然后选择“应用”。
8. 选择“确定”。
9. 将该组策略对象链接到所选位置。 例如，可以将其链接到特定的组织单位。 还可以将其链接到自动注册 Azure AD 的特定安全计算机组。 若要为组织中所有已加入域的 Windows 10 和 Windows Server 2016 计算机设置此策略，请将此组策略对象链接到相应域。

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>用于非 Windows 10 计算机的 Windows Installer 包

若要在联合环境中注册已加入域的 Windows 下层计算机，可以通过下载中心的 [适用于非 Windows 10 计算机的Microsoft 工作区加入](https://www.microsoft.com/en-us/download/details.aspx?id=53554)网页下载并安装这些 Windows Installer 包 (.msi)。

可以使用 System Center Configuration Manager 等软件分发系统部署该包。 此包使用 *quiet* 参数支持标准的无提示安装选项。 从早期版本开始，System Center Configuration Manager Current Branch 一直在提供附加功能，例如，跟踪已完成的注册。 有关详细信息，请参阅 [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager)。

安装程序会在系统上创建一项计划任务，该任务将在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 使用 Windows 集成身份验证完成身份验证后，该任务将使用用户凭据以静默方式向 Azure AD 注册设备。 若要查看计划任务，请在设备中转到“Microsoft” > “工作区加入”，然后转到任务计划程序库。

## <a name="step-5-verify-registered-devices"></a>步骤 5：验证已注册的设备

可以使用 [Azure Active Directory PowerShell 模块](https://docs.microsoft.com/en-us/powershell/msonline/)中的 [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet 检查是否在组织中成功注册了设备。

此 cmdlet 的输出显示设备已在 Azure AD 中注册。 若要获取所有设备，请使用 **-All** 参数，然后使用 **deviceTrustType** 属性筛选结果。 已加入域的设备具有 **Domain Joined** 值。

## <a name="next-steps"></a>后续步骤

* [自动设备注册常见问题](active-directory-device-registration-faq.md)
* [排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 10 和 Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)
* [排查将已加入域的计算机自动注册到 Azure AD 时发生的问题 - 非 Windows 10](active-directory-device-registration-troubleshoot-windows-legacy.md)
* [Azure Active Directory 条件访问](active-directory-conditional-access-azure-portal.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png

