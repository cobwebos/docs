---
title: 更改 Office 365 信赖方信任的签名哈希算法-Azure
description: 本页提供有关更改适用于 Office 365 的联合身份验证信任的 SHA 算法的指导原则
keywords: SHA1，SHA256，O365，联合，aadconnect，adfs，ad fs，更改 sha，联合身份验证信任，信赖方信任
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897348"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>更改 Office 365 信赖方信任的签名哈希算法
## <a name="overview"></a>概述
Active Directory 联合身份验证服务（AD FS）将其令牌 Microsoft Azure Active Directory，以确保它们不会被篡改。 此签名可以基于 SHA1 或 SHA256。 Azure Active Directory 现在支持使用 SHA256 算法签名的令牌，我们建议将令牌签名算法设置为 SHA256 以获得最高安全级别。 本文介绍将令牌签名算法设置为更安全的 SHA256 级别所需的步骤。

>[!NOTE]
>Microsoft 建议使用 SHA256 作为令牌签名算法，因为它比 SHA1 更安全，但 SHA1 仍是受支持的选项。

## <a name="change-the-token-signing-algorithm"></a>更改令牌签名算法
使用以下两个过程之一设置签名算法后，AD FS 使用 SHA256 对 Office 365 信赖方信任的令牌进行签名。 无需进行任何额外的配置更改，并且此更改不会影响你访问 Office 365 或其他 Azure AD 应用程序的能力。

### <a name="ad-fs-management-console"></a>AD FS 管理控制台
1. 打开主 AD FS 服务器上的 AD FS 管理控制台。
2. 展开 "AD FS" 节点，然后单击 "**信赖方信任**"。
3. 右键单击 Office 365/Azure 信赖方信任，然后选择 "**属性**"。
4. 选择 "**高级**" 选项卡，然后选择安全哈希算法 SHA256。
5. 单击“确定”。

![SHA256 签名算法--MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell cmdlet
1. 在任何 AD FS 服务器上，以管理员权限打开 PowerShell。
2. 使用**set-adfsrelyingpartytrust** cmdlet 设置安全哈希算法。
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>另请参阅
* [修复 Office 365 信任与 Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

