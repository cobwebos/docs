---
title: 澳大利亚和新西兰客户的标识数据存储 - Azure AD
description: 了解 Azure 活动目录在何处存储其澳大利亚客户的身份相关数据。
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
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370293"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Azure 活动目录中为澳大利亚和新西兰客户的标识数据存储

标识数据由 Azure AD 存储在地理位置中，具体取决于组织在订阅 Microsoft 在线服务（如 Office 365 和 Azure）时提供的地址。 有关标识客户数据的存储位置的信息，您可以使用 Microsoft 信任中心的"[数据位于何处"](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

> [!NOTE]
> 与 Azure AD 集成的服务和应用程序可以访问标识客户数据。 评估您用于确定特定服务和应用程序如何处理身份客户数据的每个服务和应用程序，以及它们是否符合公司的数据存储要求。 有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的数据存储在何处？部分。

对于在澳大利亚或新西兰提供地址的客户，Azure AD 将这些服务的身份数据保存在澳大利亚数据中心内： 
- Azure AD 目录管理 
- 身份验证

所有其他 Azure AD 服务将客户数据存储在全球数据中心中。 要查找服务的数据中心，请参阅[Azure 活动目录 – 数据位于何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>微软 Azure 多重身份验证 （MFA）

MFA 将身份客户数据存储在全球数据中心中。 要了解有关基于云的 Azure MFA 和 Azure MFA 服务器收集和存储的用户信息的详细信息，请参阅[Azure 多重身份验证用户数据收集](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
