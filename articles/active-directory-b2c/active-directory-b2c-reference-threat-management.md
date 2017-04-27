---
title: "Azure B2C 的威胁管理 | Microsoft Docs"
description: "Azure B2C 中的 DOS 攻击及密码攻击检测和缓解技术。"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: Ajith Alexander
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 5cca5fcd445e70b4eab9197258bb783e20016f76
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad-b2c-threat-management"></a>Azure AD B2C：威胁管理
威胁管理包括防止对系统和网络的攻击。 拒绝服务 (DOS) 可能会影响可用性，并使该资源对预期用户不可用。 密码攻击会导致未经授权的资源访问。 Microsoft Azure Active Directory B2C 具有内置功能，可采用多种方式保护你的数据免受这些威胁。 

## <a name="denial-of-service-attack"></a>拒绝服务攻击

Azure AD B2C 使用检测和缓解技术（如 SYN cookie、速率和连接限制）保护基础资源免受这些攻击。  

## <a name="password-attacks"></a>密码攻击

Azure AD B2C 也已有适用于密码攻击的缓解技术。  此技术包括暴力破解密码攻击和字典密码攻击。  要求用户设置的密码的复杂性合理。  Azure AD B2C 使用各种信号分析请求的完整性以智能地将预期用户与黑客和僵尸网络区分开来。 B2C 提供一种复杂的策略，基于输入的密码、根据受到攻击的可能性锁定帐户。

[关于 Microsoft 的威胁管理的详细信息](https://www.microsoft.com/trustcenter/security/threatmanagement)
