---
title: 管理员接管 Azure Active Directory 中的非托管目录或阴影租户 | Microsoft Docs
description: 如何接管 Azure Active Directory 中非托管目录（阴影租户）中的 DNS 域名。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 04/06/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: fa299603a14ef94ab4cf3eac2b1123def1a0b6de
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594877"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>在 Azure Active Directory 中以管理员身份接管非托管目录
本文介绍了在 Azure Active Directory (Azure AD) 的非托管目录中接管 DNS 域名的两种方式。 当自助服务用户注册一个使用 Azure AD 的云服务时，会根据其电子邮件域将其添加到非托管 Azure AD 目录。 有关自助服务或“迅速传播的”服务注册的详细信息，请参阅[什么是 Azure Active Directory 的自助服务注册？](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-self-service-signup)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>决定非托管目录的接管方式
在管理员接管的过程中，可按[向 Azure AD 添加自定义域名](add-custom-domain.md)中所述，证明所有权。 以下部分更详细地介绍了管理员体验，不过本文只会提供摘要：

* 对非托管 Azure 目录执行[“内部”管理员接管](#internal-admin-takeover)时，会将你添加为该目录的全局管理员。 用户、域或服务计划不会迁移到由你管理的任何其他目录。

* 对非托管 Azure 目录执行[“外部”管理员接管](#external-admin-takeover)时，则是将该目录的 DNS 域名添加到托管 Azure 目录。 添加域名时，将在托管 Azure 目录中创建用户到资源的映射，以便用户可以继续访问服务而不会遇到中断。 

## <a name="internal-admin-takeover"></a>内部管理员接管

包含 SharePoint 和 OneDrive 的某些产品（如 Office 365）不支持外部接管。 如果是这种情况，或如果你是管理员且想要接管的非托管或“阴影”租户是由使用自助服务注册的用户创建的，则可通过内部管理员接管方式达到目的。

1. 在非托管租户中通过注册 Power BI 等创建用户上下文。 为方便举例，以下步骤假定采用该方式。

2. 打开 [Power BI 网站](https://powerbi.com)并选择“免费开始”。 输入使用组织域名的用户帐户，如 `admin@fourthcoffee.xyz`。 输入验证码后，请检查电子邮件，查看确认代码。

3. 在来自 Power BI 的确认电子邮件中，选择“是，是我”。

4. 使用 Power BI 用户帐户登录到 [Office 365 管理中心](https://portal.office.com/adminportal/Home)。 会收到一条消息，指示如何“成为管理员”，即成为已在非托管租户中经过验证的域名的管理员。 选择“是，我想成为管理员”。
  
  ![“成为管理员”的首个屏幕截图](./media/domains-admin-takeover/become-admin-first.png)
  
5. 添加 TXT 记录以证明在域名注册机构拥有域名 fourthcoffee.xyz。 在本示例中，此站点为 GoDaddy.com。
  
  ![为域名添加 txt 记录](./media/domains-admin-takeover/become-admin-txt-record.png)

在域名注册机构验证 DNS TXT 记录后，便可管理 Azure AD 租户。

完成上述步骤后，便获得 Office 365 中 Fourth Coffee 租户的全局管理员身份。 若要将域名与其他 Azure 服务集成，可将其从 Office 365 中删除并移动到 Azure 中的另一个托管租户。

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>将域名添加到 Azure AD 中的托管租户 

1. 打开 [Office 365 管理中心](https://portal.office.com/adminportal/Home)。
2. 选择“用户”选项卡，并使用 user@fourthcoffeexyz.onmicrosoft.com 等不使用自定义域名的名称新建用户帐户。 
3. 请确保新的用户帐户对 Azure AD 租户拥有全局管理员权限。
4. 打开 Office 365 管理中心中的“域”选项卡，选择域名，然后选择“删除”。 
  
  ![从 Office 365 删除域名](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. 如果在 Office 365 中存在任何引用了已删除的域名的用户或组，必须将其重命名到 .onmicrosoft.com 域。 如果强制删除域名，将自动重命名所有用户，在本例中重命名为 user@fourthcoffeexyz.onmicrosoft.com。
  
6. 使用 Azure AD 租户的全局管理员帐户登录到 [Azure AD 管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
  
7. 选择“自定义域名”，然后添加域名。 需要输入 DNS TXT 记录来验证该域名的所有权。 
  
  ![添加到 Azure AD 的域](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> 如果删除了域名，则在 Office 365 租户中分配了许可证的任何 Power BI 或 Azure Rights Management 服务的用户必须保存其仪表板。 他们必须使用类似于 user@fourthcoffeexyz.onmicrosoft.com 而不是 user@fourthcoffee.xyz 的用户名登录。

## <a name="external-admin-takeover"></a>外部管理员接管

如果已使用 Azure 服务或 Office 365 管理租户，且自定义域名已在另一个 Azure AD 租户中经过验证，则无法添加该域名。 但是，可通过外部管理员接管从 Azure AD 中的托管租户接管非托管租户。 常规过程遵循文章[将自定义域添加到 Azure AD](add-custom-domain.md) 中的步骤。

验证域名的所有权时，Azure AD 会从非托管租户中删除该域名并将其移动到现有租户。 非托管目录的外部管理员接管操作需要执行与内部管理员接管操作相同的 DNS TXT 验证过程。 不同之处在于，除域名外还会移动以下内容：

- 用户
- 订阅
- 许可证分配

### <a name="support-for-external-admin-takeover"></a>支持外部管理员接管
以下联机服务支持外部管理员接管：

- Power BI
- Azure Rights Management
- Exchange Online

支持的服务计划包括：

- Power BI 免费版
- Power BI Pro
- PowerApps 免费版
- PowerFlow 免费版
- 个人版 RMS
- Microsoft Stream
- Dynamics 365 免费试用版

设有含 SharePoint、OneDrive 或 Skype For Business 的服务计划的任何服务都不支持外部管理员接管；例如，使用 Office 免费订阅或 Office Basic SKU 时。 可以选择使用 [**ForceTakeover** 选项](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option)，从非托管租户中删除域名并在所需租户上对其进行验证。 此 ForceTakeover 选项不会移动用户，也不会保留对订阅的访问权限。 与之相反，此选项仅移动域名。 

#### <a name="more-information-about-rms-for-individuals"></a>有关个人版 RMS 的详细信息

对于[个人版 RMS](/information-protection/understand-explore/rms-for-individuals)，当非托管租户与你拥有的租户位于同一区域时，自动创建的 [Azure 信息保护租户密钥](/information-protection/plan-design/plan-implement-tenant-key)和[默认保护模板](/information-protection/deploy-use/configure-usage-rights#rights-included-in-the-default-templates)还会随域名一起移动。 

当非托管租户位于不同区域时，密钥和模板不随之移动。 例如，非托管租户位于欧洲，而你拥有的租户位于北美。 

虽然个人版 RMS 旨在支持通过 Azure AD 身份验证来打开受保护的内容，但它不会阻止用户对内容也进行保护。 如果用户使用个人版 RMS 订阅来保护内容，而密钥和模板未移动，则在进行域接管后，该内容将无法访问。    

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>用于“ForceTakeover”选项的 Azure AD PowerShell cmdlet
可以查看在 [PowerShell 示例](#powershell-example)中使用的这些 cmdlet。


cmdlet | 使用情况 
------- | -------
`connect-msolservice` | 出现提示时，请登录到托管租户。
`get-msoldomain` | 显示与当前租户关联的域名。
`new-msoldomain –name <domainname>` | 将域名以“未验证”方式添加到租户（尚未执行 DNS 验证）。
`get-msoldomain` | 该域名现已包含在与托管租户关联的域名列表中，但被列为“未验证”。
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | 提供信息以将其放入域 (MS=xxxxx) 的新 DNS TXT 记录中。 可能不会立即进行验证，因为 TXT 记录需要花费一些时间传播，所以请等待几分钟，然后再考虑使用“-ForceTakeover”选项。 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>若仍未验证域名，则可使用“-ForceTakeover”选项继续操作。 它验证已创建 TXT 记录并启动接管进程。<li>仅在强制实施外部管理员接管时将“-ForceTakeover”选项添加到 cmdlet，例如，当非托管租户使用 Office 365 服务阻止接管时。
`get-msoldomain` | 域列表现在将该域名显示为“已验证”。

### <a name="powershell-example"></a>PowerShell 示例

1. 使用用于响应自助服务产品/服务的凭据连接到 Azure AD：
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. 获取域的列表：
  
  ````
    Get-MsolDomain
  ````
3. 运行 Get-MsolDomainVerificationDns cmdlet 以创建质询：
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. 复制从此命令返回的值（质询）。 例如：
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. 在公共 DNS 命名空间中，创建包含在上一步中复制的值的 DNS txt 记录。 此记录的名称即是父域的名称，因此，如果要使用 Windows Server 中的 DNS 角色创建此资源记录，请将记录名称保留空白，而只在文本框中粘贴该值。
6. 运行 Confirm-MsolDomain cmdlet 以验证质询：
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  例如：
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

如果质询成功，将返回到提示符，且不会显示错误。

## <a name="next-steps"></a>后续步骤
* [向 Azure AD 添加自定义域名](add-custom-domain.md)
* [如何安装和配置 Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 参考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
