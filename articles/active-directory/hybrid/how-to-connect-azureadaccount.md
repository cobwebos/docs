---
title: 更改 Azure AD 连接器帐户密码 |Microsoft Docs
description: 本主题介绍如何还原 Azure AD 连接器帐户。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d68c190b51b9bbb5faf21e8ea75b07d1a82005e5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571414"
---
# <a name="change-the-azure-ad-connector-account-password"></a>更改 Azure AD 连接器帐户密码
Azure AD 连接器帐户应该是免费的服务。 但如果需要重置其凭据，则可以参阅本主题。 例如，如果全局管理员错误地重置上使用 PowerShell 的帐户的密码。

## <a name="reset-the-credentials"></a>重置凭据
如果 Azure AD 连接器帐户无法联系 Azure AD 由于身份验证问题，则可以重置密码。

1. 登录到 Azure AD Connect 同步服务器并启动 PowerShell。
2. 运行 `Add-ADSyncAADServiceAccount`。  
   ![PowerShell cmdlet addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. 提供 Azure AD 全局管理员凭据。

此 cmdlet 重置服务帐户的密码，并在 Azure AD 和同步引擎中更新该密码。

## <a name="known-issues-these-steps-can-solve"></a>这些步骤可以解决的已知问题
本部分是由 Azure AD 连接器帐户上重置凭据已解决的客户所报告的错误的列表。

- - -
事件 6900  
服务器在处理密码更改通知时遇到意外的错误：  
AADSTS70002：验证凭据时出错。 AADSTS50054：使用了旧密码进行身份验证。

- - -
事件 659  
检索密码策略同步配置时出错。 Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException：  
AADSTS70002：验证凭据时出错。 AADSTS50054：使用了旧密码进行身份验证。

## <a name="next-steps"></a>后续步骤
**概述主题**

* [Azure AD Connect 同步：了解和自定义同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)

