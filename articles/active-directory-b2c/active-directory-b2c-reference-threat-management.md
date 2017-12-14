---
title: "Azure Active Directory B2C：威胁管理 | Microsoft Docs"
description: "了解 Azure Active Directory B2C 中用于拒绝服务攻击和密码攻击的检测和缓解技术。"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: mtillman
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2016
ms.author: 
ms.openlocfilehash: a88a04dcc83482813b8ada0e7dbff985e9f49aba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-threat-management"></a>Azure Active Directory B2C：威胁管理

威胁管理包括计划防止对系统和网络的攻击。 拒绝服务攻击可能会使目标用户无法使用资源。 密码攻击会导致未经授权的资源访问。 Azure Active Directory B2C (Azure AD B2C) 具有内置功能，可采用多种方式帮助保护数据免受这些威胁。

## <a name="denial-of-service-attacks"></a>拒绝服务攻击

Azure AD B2C 使用检测和缓解技术（如 SYN Cookie、速率和连接限制）保护基础资源免受拒绝服务攻击。

## <a name="password-attacks"></a>密码攻击

Azure AD B2C 还有适用于密码攻击的缓解技术。 缓解技术包括暴力破解密码攻击和字典密码攻击。 要求用户所设密码的复杂性合理。 Azure AD B2C 使用各种信号分析请求的完整性。 Azure AD B2C 旨在智能地将目标用户与黑客和僵尸网络区分开来。 Azure AD B2C 提供一种成熟的策略，根据输入的密码、受到攻击的可能性锁定帐户。

有关详细信息，请访问 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/security/threatmanagement)。
