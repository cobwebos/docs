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
ms.date: 12/05/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f26ca6e30e3070b2d2ee1861b88c52013158ba03
ms.openlocfilehash: 45f52d4e02896d38726552adbe81261551151683


---
# <a name="how-to-configure-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册

若要使用 [Azure Active Directory 基于设备的条件性访问](active-directory-conditional-access.md)，必须向 Azure Active Directory (Azure AD) 注册计算机。 这篇文章提供有关如何在组织中将已加入域的 Windows 设备配置为自动注册到 Azure AD 的步骤。

> [!NOTE]
> Windows 10 11 月更新在 Azure AD 中提供一些增强的用户体验，但 Windows 10 周年更新完全支持基于设备的条件性访问。 有关条件性访问的详细信息，请参阅 [Azure Active Directory 基于设备的条件性访问](active-directory-conditional-access.md)。 若要深入了解工作区中的 Windows 10 设备以及用户如何向 Azure AD 注册 Windows 10 设备，请参阅[适用于企业的 Windows 10：使用设备进行工作](active-directory-azureadjoin-windows10-devices-overview.md)。
> 
> 

对于运行 Windows 的设备，可以注册 Windows 的某些早期版本，包括：

- Windows 8.1
- Windows 7

对于运行 Windows Server 的设备，可以注册以下平台：

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2



## <a name="prerequisites"></a>先决条件

使用 Azure AD 自动注册已加入域的设备的主要要求是具有 Azure Active Directory Connect (Azure AD Connect) 的最新版本。

以下先决条件可能已自动配置，具体取决于部署 Azure AD Connect 的方式，以及使用的是快速安装、自定义安装还是就地升级：

- **本地 Active Directory 中的服务连接点** -用于使注册到 Azure AD 的计算机发现 Azure AD 租户信息。
 
- **Active Directory 联合身份验证服务 (AD FS) 颁发转换规则** -用于计算机注册时的身份验证（适用于联合配置）。

如果组织中的某些设备不是已加入域的 Windows 10 设备，请执行以下步骤：

* 在 Azure AD 中设置策略，让用户能够注册设备
* 将集成 Windows 身份验证 (IWA) 设置为 AD FS 中多重身份验证的有效替代方法

## <a name="step-1-configure-service-connection-point"></a>步骤 1：配置服务连接点 

计算机域的配置命名上下文分区中必须存在服务连接点 (SCP) 对象。 服务连接点保存有关注册计算机的 Azure AD 租户的发现信息。 在多林 Active Directory 配置中，服务连接点必须存在于具有已加入域的计算机的所有林中。

SCP 位于：  

**CN=62a0ff2e-97b9-4513-943f-0d221bd30080，CN=设备注册配置，CN=服务，[你的配置命名上下文]**

对于具有 Active Directory 域名 *example.com* 的林，配置命名上下文是：  

**CN=配置，DC=示例，DC=com**

通过以下 Windows PowerShell 脚本，可以验证对象是否存在，并检索发现值： 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

**$scp.Keywords** 输出显示 Azure AD 租户信息，例如：

azureADName:microsoft.com  
azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

如果服务连接点不存在，请在 Azure AD Connect 服务器上运行以下 PowerShell 脚本进行创建：

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;



**备注：**

- 运行 **$aadAdminCred = Get-Credential** 时，需要键入用户名。 对于用户名称，使用以下格式：**user@example.com** 


- 运行 **Initialize-ADSyncDomainJoinedComputerSync** cmdlet 时，将 [*连接器帐户名*] 替换为 Active Directory 连接器帐户中使用的域帐户。
  
- 此 cmdlet 使用 Active Directory PowerShell 模块，依赖于域控制器中的 Active Directory Web 服务。 Windows Server 2008 R2 及更高版本中的域控制器支持 Active Directory Web 服务。 对于 Windows Server 2008 或更早版本中的域控制器，通过 PowerShell 使用 System.DirectoryServices API，以创建服务连接点，然后分配关键字值。
 
 



##<a name="step-2-register-your-devices"></a>步骤 2：注册设备

注册设备的正确步骤取决于组织是否联合。 


### <a name="device-registration-in-non-federated-organizations"></a>非联合组织中的设备注册

只有满足以下条件才支持非联合组织中的设备注册：

- 设备正在运行 Windows 10 或 Windows Server 2016
- 设备已加入域
- 已启用使用 Azure AD Connect 的密码同步

如果满足上述全部条件，无需执行任何操作即可注册设备。  


### <a name="device-registration-in-federated-organizations"></a>联合组织中的设备注册

在联合 Azure AD 配置中，设备依赖 AD FS（或本地联合服务器）向 Azure AD 进行身份验证。 它们针对 Azure Active Directory 设备注册服务进行注册。

对于 Windows 10 和 Windows Server 2016 计算机，Azure AD Connect 会将 Azure AD 中的设备对象与本地计算机帐户对象关联起来。 要完成注册并创建设备对象，Azure AD 设备注册服务的身份验证期间必须存在以下声明：

- **http://schemas.microsoft.com/ws/2012/01/accounttype** -包含 DJ 值，该值将主体身份验证器标识为已加入域的计算机。

- **http://schemas.microsoft.com/identity/claims/onpremobjectguid** -包含本地计算机帐户的“objectGUID”属性值。
 
- **http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid** -包含计算机的主要安全标识符 (SID)，它对应于本地计算机帐户的“objectSid”属性值。

- **http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid** -包含一个值，Azure AD 使用该值来信任颁发自 AD FS 或本地安全令牌服务 (STS) 的令牌。 如果在 Azure AD 中有多个已验证的域，这很重要。 对于 AD FS，使用 **http://\<*domain-name*\>/adfs/services/trust/**，其中 **\<domain-name\>** 是 Azure AD 中已验证的域名。

有关已验证的域名的详细信息，请参阅 [Add a custom domain name to Azure Active Directory](active-directory-add-domain.md)（向 Azure Active Directory 添加自定义域名）。  
若要获取已验证的公司域的列表，可以使用 [Get-MsolDomain](https://docs.microsoft.com/powershell/msonline/v1/get-msoldomain) cmdlet。 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)


已加入域的 Windows 10 和 Windows Server 2016 计算机使用 Windows 集成身份验证对 AD FS 托管的活动 WS-Trust 终结点进行验证身份。 确保已启用此终结点。 如果使用的是 Web 身份验证代理，还要确保已通过该代理发布了此终结点。 终结点是 **aadfs/services/trust/13/windowstransport**。 

在 AD FS 管理控制台的“服务”>“终结点”下，该终结点应为已启用状态。 如果没有作为本地联合服务器的 AD FS，请按照供应商说明进行操作，确保启用相应的终结点。 



> [!NOTE]
> 如果不对本地联合身份验证服务器使用 AD FS，请按照供应商说明创建发出这些声明的规则。
> 
> 




**若要手动创建规则，在 AD FS 中：**

- 选择以下某个 Windows PowerShell 脚本 
- 在与服务器连接的会话中运行 Windows PowerShell 脚本。 
- 在 Azure AD 中使用组织已验证的域名替换第一行。




#### <a name="setting-ad-fs-rules-in-a-single-domain-environment"></a>在单个域环境中设置 AD FS 规则

如果只有**一个已验证的域**，使用以下脚本添加 AD FS 规则：


    <#----------------------------------------------------------------------
    |   Modify the Azure AD Relying Party to include the claims needed
    |   for DomainJoin++. The rules include:
    |   -ObjectGuid
    |   -AccountType
    |   -ObjectSid
    +---------------------------------------------------------------------#>

    $rule1 = '@RuleName = "Issue object GUID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

    $rule2 = '@RuleName = "Issue account type for domain joined computers" 

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

    $rule3 = '@RuleName = "Pass through primary SID" 

    c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

    c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

    => issue(claim = c2);' 

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 


#### <a name="setting-ad-fs-rules-in-a-multi-domain-environment"></a>在多个域环境中设置 AD FS 规则

如果拥有多个经过验证的域，请执行以下步骤：

1. 删除由 Azure AD Connect 创建的现有“IssuerID”规则。  
此处是该规则的一个示例：c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 


2. 运行此脚本： 

        <#----------------------------------------------------------------------  
        |   Modify the Azure AD Relying Party to include the claims needed  
        |   for DomainJoin++. The rules include:
        |   -ObjectGuid
        |   -AccountType
        |   -ObjectSid
        +---------------------------------------------------------------------#>

        $VerifiedDomain = 'example.com'      # Replace example.com with one of your verified domains

        $rule1 = '@RuleName = "Issue object GUID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);' 

        $rule2 = '@RuleName = "Issue account type for domain joined computers" 

        c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");' 

        $rule3 = '@RuleName = "Pass through primary SID" 

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "-515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 

        => issue(claim = c2);' 

        $rule4 = '@RuleName = "Issue AccountType with the value User when its not a computer account" 

        NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]) 

        => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");' 

        $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID" 

        c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] && 

        c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));' 

        $rule6 = '@RuleName = "Update issuer for DJ computer auth" 

        c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"] 

        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$VerifiedDomain+'/adfs/services/trust/");' 

        $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

        $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4+ $rule5+  $rule6 

        $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

        Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 



## <a name="step-3-setup-ad-fs-for-authentication-of-device-registration"></a>步骤 3：设置 AD FS，以进行设备注册的身份验证

请确保将 WIA 设置为 AD FS 中设备注册的多重身份验证的有效替代方法。 若要执行此操作，需要具有通过身份验证方法传递的颁发转换规则。

1. 在 AD FS 管理控制台中，转到“AD FS” > “信任关系” > “信赖方信任”。
2. 右键单击“Microsoft Office 365 标识平台”信赖方信任对象，并选择“编辑声明规则”。
3. 在“颁发转换规则”选项卡中，选择“添加规则”。
4. 在“声明规则”模板列表中选择“使用自定义规则发送声明”。
5. 选择“**下一步**”。
6. 在“声明规则名称”框中键入“身份验证方法声明规则”。
7. 在“声明规则”框中，键入此规则：  
**c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);**
8. 在联合身份验证服务器上，键入此 PowerShell 命令：
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

**\<RPObjectName\>** 是 Azure AD 信赖方信任对象的信赖方对象名称。 此对象通常命名为“Microsoft Office 365 标识平台”。



##<a name="step-4-deployment-and-rollout"></a>步骤 4：部署和推出

当已加入域的计算机满足先决条件时，就可以向 Azure AD 注册了。

下次重启或当用户登录到 Windows 时，Windows 10 周年更新和 Windows Server 2016 已加入域的计算机会自动注册到 Azure AD。 执行加入域操作后重启设备，已加入到域的新计算机会注册到 Azure AD。

> [!NOTE]
> 仅当设置了推出组策略对象时，已加入域的 Windows 10 计算机才会自动注册到 Azure AD。
> 
> 

可以使用组策略对象来控制推出已加入域的 Windows 10 和 Windows Server 2016 计算机的自动注册。 

若要推出已加入域的非 Windows 10 计算机的自动注册，可以将 Windows Installer 包部署到所选计算机。

> [!NOTE]
> 用于控制推出的组策略还会触发已加入域的 Windows 8.1 计算机的注册。 可以将该策略用于注册已加入域的 Windows 8.1 计算机。 或者，如果拥有混合的 Windows 版本（包括 Windows 7 或 Windows Server 版本），可以使用 Windows Installer 包注册所有非 Windows 10 和 Windows Server 2016 计算机。
> 
> 

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>创建组策略对象以控制自动注册的推出

若要控制推出已加入域的计算机的 Azure AD 自动注册，可以将“将加入域的计算机注册为设备”组策略部署到想要注册的计算机。 例如，可以将策略部署到组织单位或安全组。

**设置策略：**

1. 打开“服务器管理器”，转到“工具” > “组策略管理”。
2. 请转到某个域节点，该节点对应要在其中激活 Windows 10 或 Windows Server 2016 计算机的自动注册的域。
3. 右键单击“组策略对象”并选择“新建”。
4. 键入组策略对象的名称。 例如，“自动注册到 Azure AD”。 选择“确定”。
5. 右键单击新建的组策略对象，然后选择“编辑”。
6. 转到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “设备注册”。 右键单击“将加入域的计算机注册为设备”，然后选择“编辑”。
   
   > [!NOTE]
   > 已从早期版本的组策略管理控制台对该组策略模板进行了重命名。 如果使用早期版本的控制台，请转到“计算机配置” > “策略” > “管理模板” > “Windows 组件” > “工作区加入” > “自动工作区加入客户端计算机”。
   > 
   > 
7. 选择“已启用”，然后选择“应用”。
8. 选择“确定”。
9. 将该组策略对象链接到所选位置。 例如，可以将其链接到特定的组织单位。 还可以将其链接到自动注册 Azure AD 的特定安全计算机组。 若要为组织中所有已加入域的 Windows 10 和 Windows Server 2016 计算机设置此策略，请将此组策略对象链接到相应域。

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>用于非 Windows 10 计算机的 Windows Installer 包
若要在联合环境中注册运行 Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 的已加入域的计算机，可以下载并安装这些 Windows Installer 包 (.msi) 文件：

* [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
* [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

使用软件分发系统（如 System Center Configuration Manager）部署此包。 此包使用 *quiet* 参数支持标准的无提示安装选项。 从早期版本开始，System Center Configuration Manager 2016 增添了额外功能，如可跟踪已完成的注册。 有关详细信息，请参阅 [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center)。

安装程序会在系统上创建一项计划任务，该任务将在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 通过 IWA 进行身份验证后，该任务以无提示方式使用用户凭据向 Azure AD 注册设备。 若要查看计划任务，请转到“Microsoft” > “工作区加入”，然后转到任务计划程序库。

## <a name="next-steps"></a>后续步骤
* [Azure Active Directory 条件访问](active-directory-conditional-access.md)




<!--HONumber=Nov16_HO4-->


