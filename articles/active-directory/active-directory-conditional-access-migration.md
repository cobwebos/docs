---
title: "在 Azure 门户中迁移经典策略 | Microsoft Docs"
description: "了解在 Azure 门户中迁移经典策略所要掌握的知识。"
services: active-directory
keywords: "对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 16628bd4fa41d2e7697e1c2501f2ccd31dbd0496
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>在 Azure 门户中迁移经典策略 


[条件访问](active-directory-conditional-access-azure-portal.md)是 Azure Active Directory (Azure AD) 中的一项功能，用于控制已获授权的用户如何访问云应用。 尽管此功能的用途仍未变化，但新版 Azure 门户在条件访问的工作原理方面引入了重大改进。

应考虑迁移未在 Azure 门户中创建的策略，因为：

- 现在可以解决以前无法处理的方案。

- 可以通过合并来减少需要管理的策略数。   

- 可以在一个中心位置管理所有条件访问策略。

- Azure 经典门户即将停用。   

本文介绍在将现有条件访问策略迁移到新框架时所要掌握的知识。
 
## <a name="classic-policies"></a>经典策略

在 [Azure 门户](https://portal.azure.com)中，“条件访问 - 策略”页是条件访问策略的入口点。[](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) 但是，你的环境也可能包含尚未使用此页创建的条件访问策略。 这些策略称为“经典策略”。 经典策略是在以下位置创建的条件访问策略：

- Azure 经典门户
- Intune 经典门户
- Intune 应用保护门户


在“条件访问”页上，可以通过单击“管理”部分的“[**经典策略(预览)**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)”来访问经典策略。 


![Azure Active Directory](./media/active-directory-conditional-access-migration/71.png)


“经典策略”视图提供了一个选项用于执行以下操作：

- 筛选经典策略。
 
    ![Azure Active Directory](./media/active-directory-conditional-access-migration/72.png)

- 禁用经典策略。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/73.png)
   
- 查看经典策略的设置（以及禁用经典策略）。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/74.png)


禁用经典策略后，再也无法还原此步骤。 因此，可以使用“详细信息”视图修改经典策略中的组成员身份。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/75.png)

通过更改选定的组或排除特定的组，可以在针对所有包含的用户和组禁用某个策略之前，针对少量测试用户来测试禁用该策略所产生的影响。 



## <a name="azure-ad-conditional-access-policies"></a>Azure AD 条件访问策略

对于 Azure 门户中的条件访问，可以在一个中心位置管理所有策略。 由于条件访问的实现方式已发生很大变化，在迁移经典策略之前，应熟悉基本的概念。

请参阅：

- [Azure Active Directory 中的条件访问](active-directory-conditional-access-azure-portal.md)，了解基本概念和术语。

- [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)，获取有关在组织中部署条件访问的一些指导。

- [Azure Active Directory 中的条件访问入门](active-directory-conditional-access-azure-portal-get-started.md)，熟悉 Azure 门户中的用户界面。


 
## <a name="migration-considerations"></a>迁移注意事项

在本文中，Azure AD 条件访问策略也称为“新策略”。
在禁用或删除经典策略之前，这些策略与新策略仍可同时运行。 

在策略整合的上下文中，必须注意以下几个方面：

- 尽管经典策略与特定的云应用相关联，但你可以根据需要在新策略中选择任意数量的云应用。

- 云应用的经典策略和新策略控制要求满足所有控制条件 (*AND*)。 


- 在新策略中，可以：
 
    - 根据方案的需要组合多个条件。 

    - 选择多个授予要求作为访问控制，并使用逻辑 *OR*（要求满足选定控制条件中的一个）或逻辑 *AND*（要求满足所有选定控制条件）来合并这些要求。

        ![Azure Active Directory](./media/active-directory-conditional-access-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

若要迁移 **Office 365 Exchange Online** 的经典策略，而这些策略包含用作客户端应用条件的 **Exchange Active Sync**，则可能无法将它们整合到一个新策略。 

例如，如果希望支持所有客户端应用类型，则无法进行这种整合。 在将 **Exchange Active Sync** 用作客户端应用条件的新策略中，无法选择其他客户端应用。

![Azure Active Directory](./media/active-directory-conditional-access-migration/64.png)

如果经典策略包含多个条件，则也无法整合到一个新策略。 配置为将 **Exchange Active Sync** 用作客户端应用条件的新策略不支持其他条件：   

![Azure Active Directory](./media/active-directory-conditional-access-migration/08.png)

如果某个新策略配置为将 **Exchange Active Sync** 用作客户端应用条件，需确保未配置其他所有条件。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/16.png)
 

Office 365 Exchange Online 的、将 **Exchange Active Sync** 包含为客户端应用条件的[基于应用](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)的经典策略允许**受支持**和**不受支持**的[设备平台](active-directory-conditional-access-technical-reference.md#device-platform-condition)。 尽管无法在相关的新策略中配置单个设备平台，但可以将支持状态限制为[受支持的设备平台](active-directory-conditional-access-technical-reference.md#device-platform-condition)。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/65.png)

可以整合多个将 **Exchange Active Sync** 包含为客户端应用条件的经典策略，前提是这些策略：

- 只将 **Exchange Active Sync** 用作条件 

- 针对授予访问权限配置了多项要求

一种常见方案是整合：

- Azure 经典门户中基于设备的经典策略 
- Intune 应用保护门户中基于应用的经典策略 
 
在这种情况下，可将经典策略整合到一个已选择上述两项要求的新策略。

![Azure Active Directory](./media/active-directory-conditional-access-migration/62.png)



### <a name="device-platforms"></a>设备平台

包含[基于应用的控制条件](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)的经典策略中已预先将 iOS 和 Android 配置为[设备平台条件](active-directory-conditional-access-technical-reference.md#device-platform-condition)。 

在新策略中，需要选择希望单独支持的[设备平台](active-directory-conditional-access-technical-reference.md#device-platform-condition)。

![Azure Active Directory](./media/active-directory-conditional-access-migration/41.png)



 
 


## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件性访问策略，请参阅 [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md)（Azure Active Directory 中的条件性访问入门）。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 
