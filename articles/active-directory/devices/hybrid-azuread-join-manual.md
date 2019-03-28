---
title: 手动配置加入到混合 Azure Active Directory 的设备 | Microsoft Docs
description: 了解如何手动配置加入到混合 Azure Active Directory 的设备。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3944031101b954c380b5db6503002dae57ea16d
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518467"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>教程：手动配置加入到混合 Azure Active Directory 的设备 

使用 Azure Active Directory (Azure AD) 中的设备管理，可以确保用户从满足安全性和符合性标准的设备访问资源。 有关详细信息，请参阅 [Azure Active Directory 中的设备管理简介](overview.md)。


> [!TIP]
> 如果使用 Azure AD Connect 是合适的选项，请参阅[托管](hybrid-azuread-join-managed-domains.md)域或[联合](hybrid-azuread-join-federated-domains.md)域的相关教程。 使用 Azure AD Connect，可以大大简化混合 Azure AD 加入配置。

如果你有本地 Active Directory 环境，并且想要将已加入域的设备联接到 Azure AD，则可以通过配置联接到混合 Azure AD 的设备来实现。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 手动配置混合 Azure AD 加入
> * 配置服务连接点
> * 设置声明颁发
> * 启用 Windows 下层设备
> * 验证联接的设备
> * 对实现进行故障排除
 




## <a name="prerequisites"></a>先决条件

本教程假定你熟悉以下内容：
    
-  [Azure Active Directory 中的设备管理简介](../device-management-introduction.md)    
-  [计划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)
-  [控制设备的混合 Azure AD 加入](hybrid-azuread-join-control.md)


在组织中开始启用已加入混合 Azure AD 的设备之前，请确保：

- 运行的是最新版本的 Azure AD Connect。
- Azure AD Connect 已将要加入混合 Azure AD 的设备的计算机对象同步到 Azure AD。 如果这些计算机对象属于特定组织单位 (OU)，则也需要在 Azure AD Connect 中配置这些 OU 以进行同步。

  

Azure AD Connect：

- 保留本地 Active Directory 实例中的计算机帐户与 Azure AD 中的设备对象之间的关联。 
- 启用与设备相关的其他功能，例如 Windows Hello for Business。

请确保可从组织网络内的计算机访问以下 URL，以便将计算机注册到 Azure AD：

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- 组织的 STS（适用于联盟域），应包含在用户的本地 Intranet 设置中

如果组织计划使用无缝 SSO，则必须可从组织内的计算机访问以下 URL， 并且还必须将该 URL 添加到用户的本地 Intranet 区域。

- https://autologon.microsoftazuread-sso.com

此外，应在用户的 Intranet 区域中启用以下设置：“允许通过脚本更新状态栏”。

如果组织对本地 Active Directory 使用托管（非联合）设置并且不使用 Active Directory 联合身份验证服务 (AD FS) 与 Azure AD 联合，则 Windows 10 上的混合 Azure AD 加入依赖于 Active Directory 中要同步到 Azure AD 的计算机对象。 确保包含需要加入混合 Azure AD 的计算机对象的任何 OU 都启用了 Azure AD Connect 同步配置中的同步。

对于 Windows 10 设备（1703 或更早版本），如果组织需要通过出站代理访问 Internet，则必须实现 Web 代理自动发现 (WPAD)，使 Windows 10 计算机可以注册到 Azure AD。 

从 Windows 10 1803 开始，即使由联合域中的设备通过 AD FS 进行的混合 Azure AD 加入尝试失败，在 Azure AD Connect 已配置为将计算机/设备对象同步到 Azure AD 的情况下，设备也会尝试使用同步的计算机/设备完成混合 Azure AD 加入操作。

## <a name="verify-configuration-steps"></a>验证配置步骤

可以为各种类型的 Windows 设备平台配置联接到混合 Azure AD 的设备。 本主题包含所有典型配置方案所需的步骤。  

在下表中了解方案所需的步骤概述：  



| Steps                                      | Windows 当前设备与密码哈希同步 | Windows 当前设备与联合 | Windows 下层设备 |
| :--                                        | :-:                                    | :-:                            | :-:                |
| 配置服务连接点 | ![勾选标记][1]                            | ![勾选标记][1]                    | ![勾选标记][1]        |
| 设置声明颁发           |                                        | ![勾选标记][1]                    | ![勾选标记][1]        |
| 启用非 Windows 10 设备      |                                        |                                | ![勾选标记][1]        |
| 验证联接的设备          | ![勾选标记][1]                            | ![勾选标记][1]                    | ![勾选标记][1]        |



## <a name="configure-a-service-connection-point"></a>配置服务连接点

在注册过程中，设备使用服务连接点 (SCP) 对象来发现 Azure AD 租户信息。 在本地 Active Directory 实例中，计算机林的配置命名上下文分区中必须存在用于加入混合 Azure AD 的设备的 SCP 对象。 每个林只有一个配置命名上下文。 在多林 Active Directory 配置中，服务连接点必须存在于所有包含已加入域的计算机的林中。

可以使用 [**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) cmdlet 来检索林的配置命名上下文。  

对于具有 Active Directory 域名 *fabrikam.com* 的林，配置命名上下文是：

`CN=Configuration,DC=fabrikam,DC=com`

在林中，用于自动注册已加入域的设备的 SCP 对象位于：  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

SCP 对象可能已进行配置，具体取决于 Azure AD Connect 的部署方式。
可使用以下 Windows PowerShell 脚本验证该对象是否存在并检索发现值： 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

$scp.Keywords 输出显示 Azure AD 租户信息。 下面是一个示例：

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

如果该服务连接点不存在，可在 Azure AD Connect 服务器上运行 `Initialize-ADSyncDomainJoinedComputerSync` cmdlet 来创建它： 必须有企业管理员凭据，才能运行此 cmdlet。  

cmdlet：

- 在 Azure AD Connect 连接到的 Active Directory 林中创建服务连接点。 
- 要求指定 `AdConnectorAccount` 参数。 此帐户在 Azure AD Connect 中配置为 Active Directory 连接器帐户。 


以下脚本演示了该 cmdlet 的用法示例。 在此脚本中，`$aadAdminCred = Get-Credential` 要求键入用户名。 需要以用户主体名称 (UPN) 格式 (`user@example.com`) 提供用户名。 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` cmdlet：

- 使用 Active Directory PowerShell 模块和 Azure Active Directory 域服务 (Azure AD DS) 工具。 这些工具依赖于在域控制器上运行的 Active Directory Web 服务。 运行 Windows Server 2008 R2 和更高版本的域控制器支持 Active Directory Web 服务。
- 仅受 MSOnline PowerShell 模块 1.1.166.0 版支持。 若要下载此模块，请使用[此链接](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/)。   
- 如果未安装 Azure AD DS 工具，`Initialize-ADSyncDomainJoinedComputerSync` 会失败。 可以通过服务器管理器（在“功能” > “远程服务器管理工具” > “角色管理工具”下）安装 Azure AD DS 工具。

对于运行 Windows Server 2008 或更低版本的域控制器，请使用以下脚本来创建服务连接点。 在多林配置中，请使用以下脚本在计算机所在的每个林中创建服务连接点。
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your Active Directory configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

在前面的脚本中，`$verifiedDomain = "contoso.com"` 为占位符。 将它替换为 Azure AD 中的已验证域名之一。 需要先拥有域，然后才能使用它。

若要详细了解验证域名，请参阅[将自定义域名添加到 Azure Active Directory](../active-directory-domains-add-azure-portal.md)。 

若要获取已验证的公司域的列表，可以使用 [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) cmdlet。 

![公司域的列表](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>设置声明颁发

在联合 Azure AD 配置中，设备依赖 AD FS 或 Microsoft 合作伙伴的本地联合身份验证服务向 Azure AD 进行身份验证。 设备将执行身份验证，以获取一个访问令牌来针对 Azure Active Directory 设备注册服务 (Azure DRS) 注册。

Windows 当前设备使用 Windows 集成身份验证向本地联合身份验证服务托管的 WS-Trust 活动终结点（版本 1.3 或 2005）进行身份验证。

> [!NOTE]
> 使用 AD FS 时，必须启用 **adfs/services/trust/13/windowstransport** 或 **adfs/services/trust/2005/windowstransport**。 如果使用的是 Web 身份验证代理，还要确保已通过该代理发布了此终结点。 可以通过 AD FS 管理控制台中的“服务” > “终结点”查看已启用哪些终结点。
>
>如果不使用 AD FS 作为本地联合身份验证服务，请按供应商的说明操作，确保供应商支持 WS-Trust 1.3 或 2005 终结点，并且已通过元数据交换文件 (MEX) 发布这些终结点。

若要完成设备注册，Azure DRS 收到的令牌中必须存在以下声明。 Azure DRS 会根据该信息的部分内容在 Azure AD 中创建设备对象。 然后，Azure AD Connect 使用该信息将新建的设备对象与本地计算机帐户相关联。

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

如果有多个已验证的域名，需要提供计算机的以下声明：

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

如果已颁发 ImmutableID 声明（例如备用登录 ID），需要提供计算机的一个对应声明：

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

以下部分介绍：
 
- 每个声明应使用的值。
- AD FS 中的定义形式。

定义可以帮助验证值是否存在，或者是否需要创建值。

> [!NOTE]
> 如果不对本地联合身份验证服务器使用 AD FS，请遵照供应商的说明创建相应的配置来颁发这些声明。

### <a name="issue-account-type-claim"></a>颁发帐户类型声明

`http://schemas.microsoft.com/ws/2012/01/accounttype` 声明必须包含值 **DJ**，用于将设备标识为已加入域的计算机。 在 AD FS 中，可以添加如下所示的颁发转换规则：

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

`http://schemas.microsoft.com/identity/claims/onpremobjectguid` 声明必须包含本地计算机帐户的 **objectGUID** 值。 在 AD FS 中，可以添加如下所示的颁发转换规则：

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

`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` 声明必须包含本地计算机帐户的 **objectSid** 值。 在 AD FS 中，可以添加如下所示的颁发转换规则：

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

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>当 Azure AD 中存在多个已验证域名时颁发计算机的 issuerID

`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` 声明必须包含与颁发令牌的本地联合身份验证服务（AD FS 或合作伙伴）相连接的任何已验证域名的统一资源标识符 (URI)。 在 AD FS 中，可以在上述规则的后面，按特定的顺序添加如下所示的颁发转换规则。 注意，必须创建一条规则来显式为用户颁发规则。 在下面的规则中，添加的第一个规则用于标识用户而不是计算机身份验证。

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


在前面的声明中，`<verified-domain-name>` 为占位符。 将它替换为 Azure AD 中的已验证域名之一。 例如，使用 `Value = "http://contoso.com/adfs/services/trust/"`。



若要详细了解验证域名，请参阅[将自定义域名添加到 Azure Active Directory](../active-directory-domains-add-azure-portal.md)。  

若要获取已验证的公司域的列表，可以使用 [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) cmdlet。 

![公司域的列表](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-an-alternate-login-id-is-set"></a>当用户存在一个 ImmutableID 时颁发计算机的 ImmutableID（例如，设置备用登录 ID）

`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` 声明必须包含计算机的有效值。 在 AD FS 中，可按如下所示创建颁发转换规则：

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

可以借助以下脚本创建前述颁发转换规则。

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
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
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

#### <a name="remarks"></a>备注 

- 此脚本将规则追加到现有规则。 请不要运行该脚本两次，否则会添加规则集两次。 再次运行该脚本之前，请确保这些声明没有相应的规则（在相应的条件下）。

- 如果有多个已验证的域名（可通过 Azure AD 门户或 **Get-MsolDomain** cmdlet 查看），请在脚本中将 **$multipleVerifiedDomainNames** 的值设置为 **$true**。 此外，请确保删除那些可能是通过 Azure AD Connect 创建的或通过其他方式创建的所有现有的 **issuerid** 声明。 下面是此规则的一个示例：

      c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- 如果已颁发用户帐户的 **ImmutableID** 声明，请在脚本中将 **$immutableIDAlreadyIssuedforUsers** 的值设置为 **$true**。

## <a name="enable-windows-down-level-devices"></a>启用 Windows 下层设备

如果某些已加入域的设备是 Windows 下层设备，则需要：

- 在 Azure AD 中设置策略，使用户能够注册设备。 
- 配置本地联合身份验证服务，通过颁发声明来支持使用 Windows 集成身份验证 (IWA) 进行设备注册。 
- 将 Azure AD 设备身份验证终结点添加到本地 Intranet 区域，避免对设备进行身份验证时出现证书提示。
- 控制 Windows 下层设备 


### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>在 Azure AD 中设置策略，让用户能够注册设备

若要注册 Windows 下层设备，请确保已启用设置，允许用户在 Azure AD 中注册设备。 在 Azure 门户中，可以在“Azure Active Directory” > “用户和组” > “设备设置”下找到此设置。
    
以下策略必须设置为 **All**：**用户可以向 Azure AD 注册其设备**。

![允许用户注册设备的“全部”按钮](./media/hybrid-azuread-join-manual/23.png)


### <a name="configure-the-on-premises-federation-service"></a>配置本地联合身份验证服务 

接收发往 Azure AD 信赖方的身份验证请求时，本地联合身份验证服务必须支持颁发 **authenticationmehod** 和 **wiaormultiauthn** 声明，其中包含具有以下编码值的 resource_params 参数：

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

此类请求传入时，本地联合身份验证服务必须使用 Windows 集成身份验证对用户进行身份验证。 身份验证成功后，联合身份验证服务必须颁发以下两个声明：

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

在 AD FS 中，必须添加一个用于传递身份验证方法的颁发转换规则。 若要添加此规则，请执行以下操作：

1. 在 AD FS 管理控制台中，转到“AD FS” > “信任关系” > “信赖方信任”。
2. 右键单击“Microsoft Office 365 标识平台”信赖方信任对象，并选择“编辑声明规则”。
3. 在“颁发转换规则”选项卡中，选择“添加规则”。
4. 在“声明规则”模板列表中选择“使用自定义规则发送声明”。
5. 选择“**下一步**”。
6. 在“声明规则名称”框中，输入“身份验证方法声明规则”。
7. 在“声明规则”框中，输入以下规则：

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. 在联合身份验证服务器上，输入以下 PowerShell 命令。 将 **\<RPObjectName\>** 替换为 Azure AD 信赖方信任对象的信赖方对象名称。 此对象通常命名为“Microsoft Office 365 标识平台”。
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>将 Azure AD 设备身份验证终结点添加到本地 Intranet 区域

为了避免已注册设备的用户在向 Azure AD 进行身份验证时出现证书提示，可将一个策略推送到已加入域的设备，以便在 Internet Explorer 中将以下 URL 添加到本地 Intranet 区域：

`https://device.login.microsoftonline.com`


### <a name="control-windows-down-level-devices"></a>控制 Windows 下层设备 

若要注册 Windows 下层设备，需要通过下载中心下载并安装 Windows Installer 包 (.msi)。 有关详细信息，请参阅[控制设备的混合 Azure AD 加入](hybrid-azuread-join-control.md#control-windows-down-level-devices)。 



## <a name="verify-joined-devices"></a>验证联接的设备

可以在 [Azure Active Directory PowerShell 模块](/powershell/azure/install-msonlinev1?view=azureadps-2.0)中运行 [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet，查看组织中是否有已成功加入的设备。

此 cmdlet 的输出显示向 Azure AD 进行注册并与之联接的设备。 若要获取所有设备，请使用 **-All** 参数，然后使用 **deviceTrustType** 属性筛选结果。 已加入域的设备的值为 Domain Joined。



## <a name="troubleshoot-your-implementation"></a>对实现进行故障排除

如果在完成已加入域的 Windows 设备的混合 Azure AD 加入方面遇到问题，请参阅：

- [对 Windows 当前设备的混合 Azure AD 联接进行故障排除](troubleshoot-hybrid-join-windows-current.md)
- [对 Windows 下层设备的混合 Azure AD 联接进行故障排除](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>后续步骤

* [Azure Active Directory 中的设备管理简介](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
