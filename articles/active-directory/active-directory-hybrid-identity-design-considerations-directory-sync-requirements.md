---
title: "Azure Active Directory 混合标识设计注意事项 - 确定目录同步要求 | Microsoft 文档"
description: "确定在本地和云之间同步企业的所有用户所需的要求。"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 69f7b49fd82e4d34b1d54718cfbd2f4dedd2ae47
ms.openlocfilehash: e50a308a897878d0985f2a8f1baa044e5c82335f
ms.lasthandoff: 05/05/2017


---
# <a name="determine-directory-synchronization-requirements"></a>确定目录同步要求
同步就是基于用户的本地标识向用户提供一个云中的标识。 无论用户是否会将已同步的帐户用于身份验证或联合身份验证，用户仍需要具有一个云中的标识。  需要定期维护和更新此标识。  可以进行多种形式的更新（从标题更改到密码更改）。  

首先评估组织的本地标识解决方案和用户要求。 必须对定义如何在云中创建和维护用户标识的技术要求进行评估。  大多数组织的 Active Directory 位于本地，并且是用户进行同步的本地源目录；但在某些情况下，也会有所不同。  

务必回答以下问题：

* 你有一个 AD 林还是多个 AD 林？还是一个也没有？
  
  * 将同步到多个 Azure AD 目录？
    
    1. 是否在使用筛选？
    2. 是否计划使用多个 Azure AD Connect 服务器？
* 当前是否具有本地同步工具？
  
  * 如果是，用户有没有标识的虚拟目录/集成？
* 是否有其他本地目录（如 LDAP 目录、HR 数据库等）要同步？
  * 是否会执行任何 GALSync？
  * 当前，UPN 在组织中处于何种状态？ 
  * 是否具有要对用户进行身份验证的其他目录？
  * 贵公司是否使用 Microsoft Exchange？
    * 是否计划进行 Exchange 混合部署？

你已经了解了同步要求，现在该确定哪一种工具才能满足这些要求了。  Microsoft 提供了可完成目录集成和同步的多种工具。  有关详细信息，请参阅[混合标识目录集成工具比较表](active-directory-hybrid-identity-design-considerations-tools-comparison.md)。 

现在，你有了同步要求以及为你的公司实现此目的的工具，需要对使用这些目录服务的应用程序进行评估。 这一步非常重要，只有通过评估才能确定将这些应用程序集成到云的技术要求。 务必回答以下问题：

* 是否会将这些应用程序迁移到云以及它们是否会使用目录？
* 是否存在需要同步到云的特殊属性，以便这些应用程序可以成功地使用它们？
* 是否需要重新编写这些应用程序才可以充分利用云身份验证？
* 用户使用云标识访问这些应用程序时，这些应用程序是否仍将存在于本地？

还需要确定安全要求和约束目录同步。 必须对获取为创建和维护云中用户的标识所需的要求列表进行评估。 务必回答以下问题：

* 同步服务器将位于何处？
* 是否会加入域？
* 服务器是否将位于防火墙之后的受限网络（如 DMZ）上？
  * 是否能够打开支持同步所需的防火墙端口？
* 是否有用于同步服务器的灾难恢复计划？
* 是否具有要与其进行同步的所有林的正确权限的帐户？
  * 如果贵公司不知道该问题的答案，请查看[安装 Azure Active Directory 同步服务](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService)文章中的“密码同步的权限”一节，然后确定是否已具有这些权限的帐户，还是需要创建一个帐户。
* 如果具有多林同步，同步服务器是否能够访问每个林？

> [!NOTE]
> 务必记下每个答案并了解答案背后的依据。 [确定事件响应要求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)将检查可用的选项。 回答了这些问题之后，就会挑选出最适合你的业务需求的选项。
> 
> 

## <a name="next-steps"></a>后续步骤
[确定多重身份验证要求](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)


