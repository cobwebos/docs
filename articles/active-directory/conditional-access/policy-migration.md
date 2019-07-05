---
title: 什么是 Azure Active Directory 条件访问中的策略迁移？ | Microsoft Docs
description: 了解在 Azure 门户中迁移经典策略所要掌握的知识。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7464546a78e1b54cdea3bd6dd66656f5b189bc02
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67506800"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>什么是 Azure Active Directory 条件访问中的策略迁移？ 

[条件性访问](../active-directory-conditional-access-azure-portal.md)是一项功能的 Azure Active directory (Azure AD) 可以控制如何授权用户访问你的云应用。 虽然目的仍然相同，新的 Azure 门户的版本引入了重大改进条件性访问的工作原理。

考虑迁移未在 Azure 门户中创建的策略，因为：

- 现在可以解决以前无法处理的方案。
- 可以通过合并来减少需要管理的策略数。   
- 你可以管理在一个中心位置的所有条件性访问策略。
- Azure 经典门户即将停用。   

此文章介绍了您需要知道要将现有的条件性访问策略迁移到新的框架。
 
## <a name="classic-policies"></a>经典策略

在中[Azure 门户](https://portal.azure.com)，则[条件访问-策略](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)页是条件性访问策略的入口点。 但是，在环境中，您还可能不具有创建使用此页的条件性访问策略。 这些策略称为“经典策略”。  经典策略是条件性访问策略，在已创建：

- Azure 经典门户
- Intune 经典门户
- Intune 应用保护门户

上**条件访问**页上，您可以通过单击访问经典策略[**经典策略 （预览）** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)中**管理**部分。 

![Azure Active Directory](./media/policy-migration/71.png)

“经典策略”视图提供了一个选项用于执行以下操作： 

- 筛选经典策略。
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- 禁用经典策略。

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- 查看经典策略 （以及以禁用它） 的设置。

   ![Azure Active Directory](./media/policy-migration/74.png)

禁用经典策略后，再也无法还原此步骤。 因此，可以使用“详细信息”视图修改经典策略中的组成员身份。  

![Azure Active Directory](./media/policy-migration/75.png)

通过更改选定的组或排除特定的组，可以在针对所有包含的用户和组禁用某个策略之前，针对少量测试用户来测试禁用该策略所产生的影响。 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD 条件访问策略

使用 Azure 门户中的条件性访问，可以管理在一个中心位置中的所有策略。 由于条件性访问已更改的实现方式，您应熟悉的基本概念迁移经典策略之前。

请参阅：

- [什么是 Azure Active Directory 中条件性访问](../active-directory-conditional-access-azure-portal.md)若要了解基本概念和术语。
- [Azure Active Directory 中条件性访问的最佳实践](best-practices.md)以获取有关在组织中部署条件性访问的一些指导。
- [使用 Azure Active Directory 条件性访问需要针对特定应用的 MFA](app-based-mfa.md) ，熟悉 Azure 门户中的用户界面。
 
## <a name="migration-considerations"></a>迁移注意事项

在本文中，Azure AD 条件性访问策略也称为*新的策略*。
在禁用或删除经典策略之前，这些策略与新策略仍可同时运行。 

在策略整合的上下文中，必须注意以下几个方面：

- 尽管经典策略与特定的云应用相关联，但你可以根据需要在新策略中选择任意数量的云应用。
- 云应用的经典策略和新策略控制要求满足所有控制条件 (*AND*)。 
- 在新策略中，可以：
   - 根据方案的需要组合多个条件。 
   - 选择多个授予要求作为访问控制，并使用逻辑 *OR*（要求满足选定控制条件中的一个）或逻辑 *AND*（要求满足所有选定控制条件）来合并这些要求。

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

若要迁移 **Office 365 Exchange Online** 的经典策略，而这些策略包含用作客户端应用条件的 **Exchange Active Sync**，则可能无法将它们整合到一个新策略。 

例如，如果希望支持所有客户端应用类型，则无法进行这种整合。 在将 **Exchange Active Sync** 用作客户端应用条件的新策略中，无法选择其他客户端应用。

![Azure Active Directory](./media/policy-migration/64.png)

如果经典策略包含多个条件，则也无法整合到一个新策略。 配置为将 **Exchange Active Sync** 用作客户端应用条件的新策略不支持其他条件：   

![Azure Active Directory](./media/policy-migration/08.png)

如果某个新策略配置为将 **Exchange Active Sync** 用作客户端应用条件，需确保未配置其他所有条件。 

![Azure Active Directory](./media/policy-migration/16.png)
 
Office 365 Exchange Online 的、将 **Exchange Active Sync** 包含为客户端应用条件的[基于应用](technical-reference.md#approved-client-app-requirement)的经典策略允许**受支持**和**不受支持**的[设备平台](technical-reference.md#device-platform-condition)。 尽管无法在相关的新策略中配置单个设备平台，但可以将支持状态限制为[受支持的设备平台](technical-reference.md#device-platform-condition)。 

![Azure Active Directory](./media/policy-migration/65.png)

可以整合多个将 **Exchange Active Sync** 包含为客户端应用条件的经典策略，前提是这些策略：

- 只将 **Exchange Active Sync** 用作条件 
- 针对授予访问权限配置了多项要求

一种常见方案是整合：

- Azure 经典门户中基于设备的经典策略 
- Intune 应用保护门户中基于应用的经典策略 
 
在这种情况下，可将经典策略整合到一个已选择上述两项要求的新策略。

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>设备平台

包含[基于应用的控制条件](technical-reference.md#approved-client-app-requirement)的经典策略中已预先将 iOS 和 Android 配置为[设备平台条件](technical-reference.md#device-platform-condition)。 

在新策略中，需要选择希望单独支持的[设备平台](technical-reference.md#device-platform-condition)。

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>后续步骤

- 如果你想要了解如何配置条件性访问策略，请参阅[需要 MFA 的特定应用的 Azure Active Directory 条件性访问](app-based-mfa.md)。
- 如果你已准备好配置你的环境的条件性访问策略，请参阅[的 Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。 
