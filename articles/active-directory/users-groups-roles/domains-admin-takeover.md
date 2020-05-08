---
title: 非托管目录的管理员接管-Azure AD |Microsoft Docs
description: 如何在非托管的 Azure AD 组织（影子租户）中接管 DNS 域名。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36c7bb426a329a54f333b76e028b884204543014
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582980"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>在 Azure Active Directory 中以管理员身份接管非托管目录

本文介绍了在 Azure Active Directory (Azure AD) 的非托管目录中接管 DNS 域名的两种方式。 当自助服务用户注册一个使用 Azure AD 的云服务时，系统会根据其电子邮件域将其添加到非托管 Azure AD 目录。 有关自助服务或 "病毒" 注册服务的详细信息，请参阅[什么是 Azure Active Directory 的自助注册？](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>决定非托管目录的接管方式
在管理员接管的过程中，可按[向 Azure AD 添加自定义域名](../fundamentals/add-custom-domain.md)中所述，证明所有权。 以下部分更详细地介绍了管理员体验，不过本文只会提供摘要：

* 对非托管 Azure 目录执行[“内部”管理员接管](#internal-admin-takeover)时，会将你添加为该目录的全局管理员。 用户、域或服务计划不会迁移到由你管理的任何其他目录。

* 对非托管 Azure 目录执行[“外部”管理员接管](#external-admin-takeover)时，则是将该目录的 DNS 域名添加到托管 Azure 目录。 添加域名时，将在托管 Azure 目录中创建用户到资源的映射，以便用户可以继续访问服务而不会遇到中断。 

## <a name="internal-admin-takeover"></a>内部管理员接管

包含 SharePoint 和 OneDrive 的某些产品（如 Office 365）不支持外部接管。 如果是你的方案，或者如果你是管理员，并且想要接管使用自助注册的用户创建的非托管或 "影子" Azure AD 组织，则可以使用内部管理员接管来执行此操作。

1. 通过注册 Power BI 在非托管组织中创建用户上下文。 为方便举例，以下步骤假定采用该方式。

2. 打开 [Power BI 网站](https://powerbi.com)并选择“免费开始”****。 输入使用组织域名的用户帐户，如 `admin@fourthcoffee.xyz`。 输入验证码后，请检查电子邮件，查看确认代码。

3. 在来自 Power BI 的确认电子邮件中，选择“是，是我”****。

4. 用 Power BI 用户帐户登录到[Microsoft 365 管理中心](https://portal.office.com/admintakeover)。 您将收到一条消息，指示您成为在非托管组织中已验证的域名的**管理员**。 选择“是，我想成为管理员”****。
  
   ![“成为管理员”的首个屏幕截图](./media/domains-admin-takeover/become-admin-first.png)
  
5. 添加 TXT 记录以证明在域名注册机构拥有域名 fourthcoffee.xyz****。 在本示例中，此站点为 GoDaddy.com。
  
   ![为域名添加 txt 记录](./media/domains-admin-takeover/become-admin-txt-record.png)

在域名注册机构验证 DNS TXT 记录后，可以管理 Azure AD 组织。

完成上述步骤后，你现在就是 Office 365 中第四个咖啡店的全局管理员。 若要将域名与其他 Azure 服务集成，你可以将其从 Office 365 中删除，并将其添加到 Azure 中的其他托管组织。

### <a name="adding-the-domain-name-to-a-managed-organization-in-azure-ad"></a>将域名添加到 Azure AD 中的托管组织

1. 打开[Microsoft 365 管理中心](https://admin.microsoft.com)。
2. 选择 "**用户**" 选项卡，并使用不使用自定义域名的名称（例如*用户\@fourthcoffeexyz.onmicrosoft.com* ）创建新的用户帐户。 
3. 确保新的用户帐户具有 Azure AD 组织的全局管理员权限。
4. 在 Microsoft 365 管理中心中打开 "**域**" 选项卡，选择域名，然后选择 "**删除**"。 
  
   ![从 Office 365 删除域名](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. 如果在 Office 365 中存在任何引用了已删除的域名的用户或组，必须将其重命名到 .onmicrosoft.com 域。 如果你强制删除域名，则所有用户都将自动重命名，在此示例中为*user\@fourthcoffeexyz.onmicrosoft.com*。
  
6. 使用作为 Azure AD 组织的全局管理员的帐户登录到[Azure AD 管理中心](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
  
7. 选择“自定义域名”，然后添加域名****。 需要输入 DNS TXT 记录来验证该域名的所有权。 
  
   ![域已验证为已添加到 Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> 如果删除了域名，则在 Office 365 组织中分配了许可证的 Power BI 或 Azure Rights Management 服务的任何用户必须保存他们的仪表板。 他们必须使用用户名*\@fourthcoffeexyz.onmicrosoft.com* （而不是*用户\@fourthcoffee*）登录用户名。

## <a name="external-admin-takeover"></a>外部管理员接管

如果已使用 Azure 服务或 Office 365 管理组织，则如果已在另一 Azure AD 组织中验证了自定义域名，则无法添加该域名。 但是，在 Azure AD 中，你可以将非托管组织作为外部管理员接管。 常规过程遵循文章[将自定义域添加到 Azure AD](../fundamentals/add-custom-domain.md) 中的步骤。

验证域名的所有权时，Azure AD 会从非托管组织中删除域名，并将其移到现有组织中。 非托管目录的外部管理员接管操作需要执行与内部管理员接管操作相同的 DNS TXT 验证过程。 不同之处在于，除域名外还会移动以下内容：

- 用户
- 订阅
- 许可证分配

### <a name="support-for-external-admin-takeover"></a>支持外部管理员接管
以下联机服务支持外部管理员接管：

- Azure Rights Management
- Exchange Online

支持的服务计划包括：

- PowerApps 免费版
- PowerFlow 免费版
- 个人版 RMS
- Microsoft Stream
- Dynamics 365 免费试用版

对于包含 SharePoint、OneDrive 或 Skype For Business 的服务计划的任何服务，都不支持外部管理员接管;例如，通过 Office 免费订阅。 

您可以选择使用[ **ForceTakeover**选项](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option)从非托管组织中删除域名，并在所需的组织中对其进行验证。 

#### <a name="more-information-about-rms-for-individuals"></a>有关个人版 RMS 的详细信息

对于[个人 RMS](/azure/information-protection/rms-for-individuals)，如果非托管组织与你拥有的组织位于同一区域，则自动创建的[Azure 信息保护组织密钥](/azure/information-protection/plan-implement-tenant-key)和[默认保护模板](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates)将另外随域名一起移动。

当非托管组织在不同的区域时，密钥和模板不会移动。 例如，如果非托管组织在欧洲，并且你拥有的组织处于北美中。

虽然个人版 RMS 旨在支持通过 Azure AD 身份验证来打开受保护的内容，但它不会阻止用户对内容也进行保护。 如果用户已通过个人 RMS 订阅保护内容，并且未移动密钥和模板，则在域接管后，不能访问该内容。

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>用于“ForceTakeover”选项的 Azure AD PowerShell cmdlet
可以查看在 [PowerShell 示例](#powershell-example)中使用的这些 cmdlet。

cmdlet | 用法
------- | -------
`connect-msolservice` | 出现提示时，请登录到托管组织。
`get-msoldomain` | 显示与当前组织关联的域名。
`new-msoldomain –name <domainname>` | 将域名作为 "未验证" 添加到组织（尚未执行任何 DNS 验证）。
`get-msoldomain` | 域名现在包含在与托管组织关联的域名列表中，但列为 "未**验证**"。
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | 提供信息以将其放入域 (MS=xxxxx) 的新 DNS TXT 记录中。 可能不会立即进行验证，因为 TXT 记录需要花费一些时间传播，所以请等待几分钟，然后再考虑使用“-ForceTakeover”选项****。 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>若仍未验证域名，则可使用“-ForceTakeover”选项继续操作****。 它验证已创建 TXT 记录并启动接管进程。<li>仅当强制外部管理员接管时，才应将 **-ForceTakeover**选项添加到 cmdlet，例如，当非托管组织有 Office 365 服务阻止接管时。
`get-msoldomain` | 域列表现在将该域名显示为“已验证”****。

> [!NOTE]
> 执行外部接管强制选项后，会在10天内删除非托管 Azure AD 组织。

### <a name="powershell-example"></a>PowerShell 示例

1. 使用凭据连接到 Azure AD，这些凭据曾用于响应自助服务产品：
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. 获取域的列表：
  
   ```powershell
   Get-MsolDomain
   ```
3. 运行 Get-MsolDomainVerificationDns cmdlet 以创建质询：
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    例如：
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. 复制从此命令返回的值（质询）。 例如：
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. 在公共 DNS 命名空间中，创建包含在上一步中复制的值的 DNS txt 记录。 此记录的名称即是父域的名称，因此，如果要使用 Windows Server 中的 DNS 角色创建此资源记录，请将记录名称保留空白，而只在文本框中粘贴该值。
6. 运行 Confirm-MsolDomain cmdlet 以验证质询：
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   例如：
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

如果质询成功，将返回到提示符，且不会显示错误。

## <a name="next-steps"></a>后续步骤

* [向 Azure AD 添加自定义域名](../fundamentals/add-custom-domain.md)
* [如何安装和配置 Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 参考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
