---
title: 适用于澳大利亚客户的标识数据存储-Azure AD
description: 了解 Azure Active Directory 为其澳大利亚客户存储标识相关数据的位置。
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dc659f3d580646c1e86e5e97940268311e5546f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460528"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory 中的澳大利亚和新西兰客户的标识数据存储

标识数据由 Azure AD 存储在地理位置，在订阅 Microsoft Online services （如 Office 365 和 Azure）时，该地址基于组织提供的地址。 若要了解标识客户数据的存储位置，可以使用 Microsoft 信任中心的 "[你的数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) " 部分。

> [!NOTE]
> 与 Azure AD 集成的服务和应用程序可以访问标识客户数据。 评估每个服务和应用程序，以确定特定服务和应用程序如何处理标识客户数据，以及这些数据是否符合公司的数据存储要求。 有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的数据存储在何处？部分。

对于在澳大利亚和新西兰提供地址并使用 Azure AD 免费版的客户，Azure AD 将 PII 数据保存在澳大利亚数据中心。 

所有其他 Azure AD 高级服务将客户数据存储在全球数据中心。 若要查找服务的数据中心，请参阅[Azure Active Directory –你的数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure 多重身份验证（MFA）

中的 MFA 服务 Azure AD 将标识客户数据存储在静态全局数据中心中。 若要详细了解基于云的 Azure MFA 和 Azure MFA 服务器收集和存储的用户信息，请参阅[Azure 多重身份验证用户数据收集](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)。 如果客户使用 MFA，则其数据将存储在澳大利亚以外的其他位置。 

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
