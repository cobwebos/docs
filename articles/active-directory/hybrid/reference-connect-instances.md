---
title: Azure AD Connect：同步服务实例 | Microsoft Docs
description: 本页记录了 Azure AD 实例的特殊注意事项。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "66298821"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect：有关实例的特殊注意事项
Azure AD Connect 最常用于全球范围内的 Azure AD 和 Office 365 实例。 但也有其他实例，这些实例对 URL 具有不同的要求并且具有其他的特殊注意事项。

## <a name="microsoft-cloud-germany"></a>德国 Microsoft 云
[德国 Microsoft 云](https://www.microsoft.de/cloud-deutschland)是由德国数据信托运营的最高等级的云。

| 将在代理服务器中打开的 URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| + 证书吊销列表 |

登录到 Azure AD 租户时，必须使用 onmicrosoft.de 域中的帐户。

德国 Microsoft 云中当前不存在的功能：

* **密码写回**可用于预览 Azure AD Connect 版本 1.1.570.0 及之后。
* 其他 Azure AD Premium 服务不可用。

## <a name="microsoft-azure-government"></a>Microsoft Azure 政府
[Microsoft Azure 政府版云](https://azure.microsoft.com/features/gov/)是用于美国政府的云。

DirSync 的早期版本支持该云。 从 Azure AD Connect 的 1.1.180 版本起，将支持下一代云。 这一代使用的是基于仅限美国的终结点，并具有不同的 URL 列表，可在代理服务器中打开。

| 将在代理服务器中打开的 URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net（自动 Azure 政府租户检测需要） |
| \*.gov.us.microsoftonline.com |
| + 证书吊销列表 |

> [!NOTE]
> 从 Azure AD Connect 版本 1.1.647.0 开始，如果 *.windows.net在代理服务器上打开，则不再需要在注册表中设置 Azure 实例值。 但是，对于不允许从 Azure AD Connect 服务器进行 Internet 连接的客户，可以使用以下手动配置。

### <a name="manual-configuration"></a>手动配置

以下手动配置步骤用于确保 Azure AD 连接使用 Azure 政府同步终结点。

1. 开始 Azure AD Connect 安装。
2. 出现第一页时（应在其中接受 EULA），请不要继续，而是让安装向导保持运行。
3. 启动 regedit 并将注册表项 `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` 更改为值 `4`。
4. 返回 Azure AD Connect 安装向导，接受 EULA，并继续。 在安装过程中，请确保使用**自定义配置**安装路径（而不是快速安装），然后照常继续安装。

## <a name="next-steps"></a>后续步骤
详细了解[将本地标识与 Azure 活动目录集成](whatis-hybrid-identity.md)。
