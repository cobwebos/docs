---
title: "标识同步和重复属性复原 | Microsoft Docs"
description: "介绍如何借助一种新的行为，在目录同步期间使用 Azure AD Connect 处理具有 UPN 或 ProxyAddress 冲突的对象。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: 1ceb2cbe728d2b3bf21f441a595b7ed8e91e3795
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>标识同步和重复属性复原
重复属性复原是 Azure Active Directory 的一项功能，可在运行 Microsoft 的同步工具之一时消除 **UserPrincipalName** 和 **ProxyAddress** 冲突所造成的不便。

在给定 Azure Active Directory 租户的所有“用户”、“组”或“联系人”对象中，这两个属性通常必须是唯一的。

> [!NOTE]
> 只有用户可以拥有 UPN。
> 
> 

此功能实现的新行为是同步管道的云部分，因此，此功能不区分客户端，而是与任何 Microsoft 同步产品（包括 Azure AD Connect、DirSync 和 MIM + 连接器）相关。 本文档中使用的概括术语“同步客户端”用于表示上述任一产品。

## <a name="current-behavior"></a>当前行为
如果尝试预配的新对象具有违反此唯一性约束的 UPN 或 ProxyAddress 值，则 Azure Active Directory 会阻止创建该对象。 同样地，如果以非唯一的 UPN 或 ProxyAddress 更新对象，则更新会失败。 同步客户端在每个导出周期重试预配尝试或更新，在冲突解决前继续失败。 每次尝试时都会生成错误报告电子邮件，并由同步客户端记录一个错误。

## <a name="behavior-with-duplicate-attribute-resiliency"></a>重复属性复原的行为
Azure Active Directory 并不是完全无法预配或更新具有重复属性的对象，而是“隔离”违反唯一性约束的重复属性。 如果预配时需要此属性（例如 UserPrincipalName），则服务将分配占位符值。 这些临时值的格式为  
“***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***”。  
如果不需要此属性（例如 **ProxyAddress**），则 Azure Active Directory 只隔离冲突属性并继续创建或更新对象。

隔离属性后，有关冲突的信息以旧行为中使用的相同错误报告电子邮件发送。 但是，此信息只出现在错误报告中一次，发生隔离时，将不继续记录在以后的电子邮件中。 此外，由于此对象已成功导出，因此同步客户端不会记录错误，并且不会在后续的同步周期中重试创建/更新操作。

为了支持此行为，已向 User、Group 和 Contact 对象类添加新属性：  
**DirSyncProvisioningErrors**

这是一个多值属性，用于存储正常添加时违反唯一性约束的冲突属性。 Azure Active Directory 中已启用后台计时器任务，该任务每小时运行以查找已解决的重复属性冲突，并自动从隔离区中删除有问题的属性。

### <a name="enabling-duplicate-attribute-resiliency"></a>启用重复属性复原
重复属性复原将是所有 Azure Active Directory 租户上的新默认行为。 对于在 2016 年 8 月 22 日或以后首次启用同步的所有租户，它将默认打开。 在此日期前启用同步的租户将分批启用此功能。 此部署将于 2016 年 9 月开始，将向每个租户的技术通知联系人发送电子邮件通知，其中包含将要启用此功能的具体日期。

> [!NOTE]
> 一旦打开重复属性复原，便无法禁用它。

若要检查是否已为租户启用该功能，可下载 Azure Active Directory PowerShell 模块的最新版本并运行：

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> 为租户启用“重复属性复原”功能之前，不能再使用 Set-MsolDirSyncFeature cmdlet 主动启用“重复属性复原”功能。 要能够测试该功能，将需要创建新的 Azure Active Directory 租户。

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>识别具有 DirSyncProvisioningErrors 的对象
目前有两种方法可识别因为重复属性冲突而发生错误的对象：Azure Active Directory PowerShell 和 Office 365 管理门户。 我们已计划将来扩展到其他基于门户的报告。

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
本主题中的 PowerShell cmdlet 具有以下特点：

* 以下所有 cmdlet 都区分大小写。
* 始终必须包含 **–ErrorCategory PropertyConflict**。 目前没有其他类型的 **ErrorCategory**，但将来可能会扩展。

首先，应运行 **Connect-MsolService** 并输入租户管理员的凭据。

然后，使用以下 cmdlet 和运算符以不同方式查看错误：

1. [查看全部](#see-all)
2. [按属性类型](#by-property-type)
3. [按冲突值](#by-conflicting-value)
4. [使用字符串搜索](#using-a-string-search)
5. [已排序](#sorted)
6. [以有限的数量或全部](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>查看全部
连接后，若要查看租户中属性预配错误的常规列表，请运行：

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

随后将生成如下所示的结果：  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>按属性类型
若要按属性类型查看错误，请添加带有 **UserPrincipalName** 或 **ProxyAddresses** 参数的 **-PropertyName** 标志：

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

或

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>按冲突值
若要查看与特定属性相关的错误，请添加 **-PropertyValue** 标志（添加此标志时也必须使用 **-PropertyName**）：

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>使用字符串搜索
若要进行广泛的字符串搜索，请使用 **-SearchString** 标志。 此标志可以独立于上述所有标志使用，但 **-ErrorCategory PropertyConflict** 除外（此标志始终是必需的）：

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>以有限的数量或全部
1. **MaxResults <Int>** 可用于将查询限制为特定数目的值。
2. **All** 可用于确保在有大量错误的情况下检索所有结果。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Office 365 管理门户
可以在 Office 365 管理中心查看目录同步错误。 Office 365 门户中的报告只显示存在这些错误的 **User** 对象。 它不显示有关 **Groups** 和 **Contacts** 之间的冲突的信息。

![活动用户](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "活动用户")

有关如何在 Office 365 管理中心查看目录同步错误的说明，请参阅[识别 Office 365 中的目录同步错误](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)。

### <a name="identity-synchronization-error-report"></a>标识同步错误报告
使用此新行为处理具有重复属性冲突的对象时，通知将包含在标准标识同步错误报告电子邮件中，而该电子邮件将发送给租户的技术通知联系人。 但是，此行为有一项重大变化。 在过去，有关重复属性冲突的信息包含在每个后续错误报告中，直到解决冲突为止。 使用此新行为，给定冲突的错误通知只出现一次 - 在冲突属性被隔离时。

ProxyAddress 冲突的电子邮件通知示例如下所示：  
    ![活动用户](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "活动用户")  

## <a name="resolving-conflicts"></a>解决冲突
针对这些错误的故障排除策略和解决技巧不应与过去处理重复属性错误的方式不同。 唯一的差别在于，计时器任务将扫描服务端的租户，以便在冲突解决后，自动将有问题的属性添加到适当的对象。

以下文章概述了各种故障排除和解决策略：[重复或无效的属性阻止 Office 365 中的目录同步](https://support.microsoft.com/kb/2647098)。

## <a name="known-issues"></a>已知问题
没有任何已知问题导致数据丢失或服务降级。 其中有些问题是外观问题，有些问题将导致引发标准的“*复原前*”重复属性错误，而不是隔离冲突属性，还有一些问题导致特定错误需要额外的手动修复。

**核心行为：**

1. 具有特定属性配置的对象继续收到导出错误，而不是重复属性被隔离。  
   例如：
   
    a. 新用户在 AD 中创建，其 UPN 为 **Joe@contoso.com**，ProxyAddress 为 **smtp:Joe@contoso.com**。
   
    b. 此对象的属性与现有组发生冲突，其中 ProxyAddress 为 **SMTP:Joe@contoso.com**。
   
    c. 导出时，将引发 **ProxyAddress 冲突**错误，而非隔离冲突属性。 此操作在每个后续的同步周期中重试，就如同在启用复原之前一样。
2. 如果在本地创建两个具有相同 SMTP 地址的组，则其中一个组在首次尝试预配时会失败并返回标准的重复 **ProxyAddress** 错误。 但是，重复值会在下一个同步周期被适当隔离。

**Office 门户报告**：

1. UPN 冲突集中两个对象的详细错误消息是相同的。 这意味着，它们的 UPN 都已更改/隔离，此时，实际上只有其中一个对象的数据发生更改。
2. UPN 冲突的详细错误消息对已更改/隔离其 UPN 的用户显示不正确的 displayName。 例如：
   
    a. **用户 A** 首先使用 **UPN = User@contoso.com** 同步。
   
    b. 然后，**用户 B** 尝试使用 **UPN = User@contoso.com** 同步。
   
    c. **用户 B** 的 UPN 已更改为 **User1234@contoso.onmicrosoft.com**，**User@contoso.com** 已添加到 **DirSyncProvisioningErrors**。
   
    d.单击“下一步”。 **用户 B** 的错误消息应指出**用户 A** 已有用作 UPN 的 **User@contoso.com**，但却显示**用户 B** 自己的 displayName。

**标识同步错误报告**：

*关于如何解决此问题的步骤*链接不正确：  
    ![活动用户](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "活动用户")  

它应指向 [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)。

## <a name="see-also"></a>另请参阅
* [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)
* [识别 Office 365 中的目录同步错误](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

