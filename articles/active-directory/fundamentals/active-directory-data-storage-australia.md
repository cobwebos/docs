---
title: 澳大利亚和新西兰客户的标识数据存储-Azure AD
description: 了解 Azure Active Directory 为澳大利亚和新西兰的客户存储标识相关数据的位置。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7ed1d4c49c46673f1a1c2b5cb08b2467490acae
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565110"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure Active Directory 中的澳大利亚和新西兰客户的标识数据存储

标识数据由 Azure AD 存储在地理位置，这是在订阅 Microsoft Online 服务（例如 Microsoft 365 和 Azure）时由你的组织提供的地址。 若要了解标识客户数据的存储位置，可以使用 Microsoft 信任中心的 " [你的数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) " 部分。

> [!NOTE]
> 与 Azure AD 集成的服务和应用程序可以访问标识客户数据。 评估每个服务和应用程序，以确定特定服务和应用程序如何处理标识客户数据，以及这些数据是否符合公司的数据存储要求。 有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的数据存储在何处？部分。

对于在澳大利亚或新西兰提供地址的客户，Azure AD 在澳大利亚数据中心内保留这些服务的标识数据： 
- Azure AD 目录管理 
- 身份验证

所有其他 Azure AD 服务在全球数据中心存储客户数据。 若要查找服务的数据中心，请参阅 [Azure Active Directory –你的数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure 多因素身份验证 (MFA) 

MFA 将标识客户数据存储在全球数据中心。 若要详细了解基于云的 Azure MFA 和 Azure MFA 服务器收集和存储的用户信息，请参阅 [Azure 多重身份验证用户数据收集](../authentication/concept-mfa-data-residency.md)。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](../authentication/concept-mfa-howitworks.md)