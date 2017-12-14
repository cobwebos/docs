---
title: "Azure AD Connect：同步服务实例 | Microsoft Docs"
description: "本页记录了 Azure AD 实例的特殊注意事项。"
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: billmath
ms.openlocfilehash: 396a0ff94f380e194d4b1d45c3d8132a4265a131
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect：有关实例的特殊注意事项
Azure AD Connect 最常用于全球范围内的 Azure AD 和 Office 365 实例。 但也有其他实例，这些实例对 URL 具有不同的要求并且具有其他的特殊注意事项。

## <a name="microsoft-cloud-germany"></a>德国 Microsoft 云
[德国 Microsoft 云](http://www.microsoft.de/cloud-deutschland)是由德国数据信托运营的最高等级的云。

| 在代理服务器中打开的 URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + 证书吊销列表 |

登录到 Azure AD 租户时，必须使用 onmicrosoft.de 域中的帐户。

德国 Microsoft 云中当前不存在的功能：

* **Azure AD Connect Health** 不可用。
* **自动更新**不可用。
* 密码写回可用于使用 Azure AD Connect 1.1.570.0 及更高版本预览。
* 其他 Azure AD Premium 服务不可用。

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure 政府版云
[Microsoft Azure 政府版云](https://azure.microsoft.com/features/gov/)是用于美国政府的云。

DirSync 的早期版本支持该云。 从 Azure AD Connect 的 1.1.180 版本起，将支持下一代云。 这一代使用的是基于仅限美国的终结点，并具有不同的 URL 列表，可在代理服务器中打开。

| 在代理服务器中打开的 URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net（自动 Azure AD 政府租户检测所必需） |
| \*.gov.us.microsoftonline.com |
| + 证书吊销列表 |

> [!NOTE]
> 从 AAD Connect 版本 1.1.647.0 开始，如果已在代理服务器上打开 *.windows.net，将不再需要在注册表中设置 AzureInstance 值。

Microsoft Azure 政府版云中当前不存在的功能：

* **Azure AD Connect Health** 不可用。
* **自动更新**不可用。
* 密码写回可用于使用 Azure AD Connect 1.1.570.0 及更高版本预览。
* 其他 Azure AD Premium 服务不可用。

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
