---
title: 允许或阻止向特定组织中的 B2B 用户发送邀请 - Azure Active Directory | Microsoft Docs
description: 向管理员介绍如何使用 Azure 门户或 PowerShell 设置访问或拒绝列表，以允许或阻止来自某些域的 B2B 用户。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/19/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 3b4b57dd2299c6278fe823f59a4f2c7d8721f712
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>允许或阻止向特定组织中的 B2B 用户发送邀请

可以使用允许列表或拒绝列表，来允许或阻止向特定组织中的 B2B 用户发送邀请。 例如，如果你要阻止个人电子邮件地址域，可以设置一个拒绝列表，并在其中包含类似于 Gmail.com 和 Outlook.com 的域。或者，如果你的企业与 Contoso.com、Fabrikam.com 和 Litware.com 等其他企业建立了合作关系，并且你希望将邀请限制为这些组织，则可以将 Contoso.com、Fabrikam.com 和 Litware.com 添加到允许列表。
  
## <a name="important-considerations"></a>重要注意事项

- 可以创建允许列表或拒绝列表。 不能同时设置这两种类型的列表。 默认情况下，不在允许列表中的任何域都会包含在拒绝列表中，反之亦然。 
- 对于每个组织，只能创建一个策略。 可以更新策略以包含更多的域，或者删除策略以创建新策略。 
- 此列表独立于 OneDrive for Business 和 SharePoint Online 允许/阻止列表。 若要在 SharePoint Online 中限制单个文件的共享，需要为 OneDrive for Business 和 SharePoint Online 设置允许或拒绝列表。 有关详细信息，请参阅 [SharePoint Online 和 OneDrive for Business 中受限制的域共享](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9)。
- 此列表不适用于已兑换邀请的外部用户。 设置列表后，将强制实施该列表。 如果用户邀请处于挂起状态，而你设置了一个阻止该用户的域的策略，则该用户在尝试兑换邀请时将会失败。

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>在门户中设置允许或拒绝列表策略

“允许将邀请发送到任何域(最大范围)”默认已启用。 在这种情况下，可以邀请任何组织中的 B2B 用户。

### <a name="add-a-deny-list"></a>添加拒绝列表

这是一个最典型的场景：你的组织希望能够与绝大多数组织合作，但同时想要避免邀请某些域的用户作为 B2B 用户。

添加拒绝列表：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “用户” > “用户设置”。
3. 在“外部用户”下，选择“管理外部协作设置”。
4. 在“协作限制”下，选择“拒绝向指定的域发送邀请”。
5. 在“目标域”下，输入要阻止的某个域的名称。 若要阻止多个域，请分行输入每个域。 例如：

   ![显示针对添加的域的拒绝选项](./media/allow-deny-list/DenyListSettings.png)
 
6. 完成后，单击“保存”。

设置策略后，如果尝试邀请被阻止域中的用户，将会收到一条消息，指出该用户的域当前已被邀请策略阻止。
 
### <a name="add-an-allow-list"></a>添加允许列表

这是限制性更强的配置。使用此配置可在允许列表中设置特定的域，并将邀请限制为列表中未提到的其他任何组织或域。 

若要使用允许列表，请确保花些时间来全面评估业务需求。 如果此策略的限制性过于严格，则用户可能会选择通过电子邮件发送文档，或者寻求 IT 部门未批准的其他协作方式。


若要添加允许列表，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “用户” > “用户设置”。
3. 在“外部用户”下，选择“管理外部协作设置”。
4. 在“协作限制”下，选择“只允许向指定的域发送邀请(限制性最强)”。
5. 在“目标域”下，输入要允许的某个域的名称。 若要阻止多个域，请分行输入每个域。 例如：

   ![显示已添加域的允许选项](./media/allow-deny-list/AllowListSettings.png)
 
6. 完成后，单击“保存”。

设置策略后，如果尝试邀请的用户来自不在允许列表中的域，则会收到一条消息，指出该用户的域当前已被邀请策略阻止。

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>从允许列表切换到拒绝列表或反之 

如果从一种策略切换到另一种策略，则会丢弃现有的策略配置。 在执行这种切换之前，请务必备份配置详细信息。 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>使用 PowerShell 设置允许或拒绝列表策略

### <a name="prerequisite"></a>先决条件

若要使用 PowerShell 设置允许或拒绝列表，必须安装适用于 Windows PowerShell 的 Azure Active Directory 模块预览版。 具体而言，请安装 AzureADPreview 模块 2.0.0.98 或更高版本。

检查模块版本（及查看是否已安装）：
 
1. 以权限提升的用户身份（以管理员身份运行）打开 Windows PowerShell。 
2. 运行以下命令，查看计算机上是否已安装任何版本的适用于 Windows PowerShell 的 Azure Active Directory 模块：

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

如果未安装该模块或者未安装所需的版本，请执行以下操作之一：

- 如果未返回任何结果，请运行以下命令安装最新版本的 AzureADPreview 模块：
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- 如果结果中只显示了 AzureAD 模块，请运行以下命令安装 AzureADPreview 模块： 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- 如果结果中只显示了 AzureADPreview 模块，但版本低于 2.0.0.98，请运行以下命令更新该模块： 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- 如果结果中同时显示了 AzureAD 和 AzureADPreview 模块，但 AzureADPreview 模块的版本低于 2.0.0.98，请运行以下命令更新该模块： 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>使用 AzureADPolicy cmdlet 配置策略

若要创建允许或拒绝列表，请使用 [New-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) cmdlet。 以下示例演示如何设置阻止“live.com”域的拒绝列表。

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

下面是一个类似的示例，其中包含内联的策略定义。

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

若要设置允许或拒绝列表策略，请使用 [Set-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) cmdlet。 例如：

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

若要获取策略，请使用 [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) cmdlet。 例如：

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

若要删除策略，请使用 [Remove-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) cmdlet。 例如：

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>后续步骤

- 有关 Azure AD B2B 的概述，请参阅[什么是 Azure AD B2B 协作？](what-is-b2b.md)
- 有关条件访问和 B2B 协作的信息，请参阅 [B2B 协作用户的条件访问](conditional-access.md)。



