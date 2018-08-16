---
title: 操作指南 - 针对来自不受信任网络的访问尝试配置 Azure Active Directory 条件访问策略 | Microsoft Docs
description: 了解如何在 Azure Active Directory 中针对来自不受信任网络的访问尝试配置条件访问策略。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: protection
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2dea5686add93d93f35e82445f411035a2451e33
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525971"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>操作指南：针对来自不受信任网络的访问尝试配置条件访问策略   

在移动优先、云优先的世界中，使用 Azure Active Directory (Azure AD) 可以实现从任意位置单一登录到设备、应用和服务。 因此，用户不但可以从组织的网络访问云应用，而且可以从任何不受信任的 Internet 位置访问云应用。 使用 [Azure Active Directory (Azure AD) 条件访问](active-directory-conditional-access-azure-portal.md)，可以控制授权用户访问云应用的方式。 在此上下文中，一个常见的要求是控制从不受信任的网络发起的访问尝试。 本文提供了配置用来处理此要求的条件访问策略所需的信息。 

## <a name="prerequisites"></a>先决条件

本文假定你熟悉以下内容： 

- Azure AD 条件访问的基本概念 
- 在 Azure 门户中配置条件访问策略

请参阅：

- [什么是 Azure Active Directory 中的条件访问](active-directory-conditional-access-azure-portal.md) - 获取有关条件访问的概述 

- [快速入门：通过 Azure Active Directory 条件访问要求特定应用进行多重身份验证](conditional-access/app-based-mfa.md) - 体验一下如何配置条件访问策略。 


## <a name="scenario-description"></a>方案描述

要掌控安全性与工作效率之间的平衡，对于你来说，可能只需要要求用户使用密码进行身份验证就足够了。 但是，当访问尝试来自不受信任的网络位置时，登录不是由合法用户执行的这一风险会增大。 要解决此顾虑，可以阻止来自不受信任网络的访问尝试。 另外，还可以要求进行多重身份验证 (MFA) 来获得额外的保证，以确保访问尝试是由该帐户的合法所有者执行的。 

使用 Azure AD 条件访问，可以通过进行授权的以下单个策略来解决此要求： 

- 授予对所选云应用的访问权限

- 为所选用户和组授予权限  

- 要求进行多重身份验证 

- 当访问尝试来自以下位置时： 

    - 不受信任的位置


## <a name="considerations"></a>注意事项

此场景的挑战在于将“在从不受信任的位置尝试访问时”转换为条件访问条件。 在条件访问策略中，可以配置[位置条件](conditional-access/location-condition.md)来应对与网络位置相关的场景。 位置条件使你可以选择命名位置，这些位置表示 IP 地址范围、国家和地区的逻辑分组。  

通常，你的组织拥有一个或多个地址范围，例如 199.30.16.0 - 199.30.16.24。
可以通过以下方式配置命名位置：

- 指定此范围 (199.30.16.0/24) 

- 分配一个描述性名称，例如**公司网络** 


可以选择以下选项，而不是尝试定义不受信任的所有位置：

- 包括 

    ![条件性访问](./media/active-directory-conditional-access-untrusted-networks/02.png)

- 排除所有受信任的位置 

    ![条件性访问](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>实现

使用本文中概述的方法，你现在可以针对不受信任位置配置条件访问策略。 在将策略推广到生产环境中之前，始终应当对策略进行测试以确保它按预期工作。 理想情况下，如果可能，应当在测试租户中执行初始测试。 有关详细信息，请参阅[应当如何部署新策略](conditional-access/best-practices.md#how-should-you-deploy-a-new-policy)。 



## <a name="next-steps"></a>后续步骤

若要了解有关条件访问的详细信息，请参阅[什么是 Azure Active Directory 中的条件访问？](active-directory-conditional-access-azure-portal.md)