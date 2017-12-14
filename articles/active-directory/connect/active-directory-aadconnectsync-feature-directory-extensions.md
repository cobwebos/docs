---
title: "Azure AD Connect 同步：目录扩展 | Microsoft Docs"
description: "本主题介绍 Azure AD Connect 中的目录扩展功能。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3ab8b02ad30315de23e5d8e7370cc385a53ecf3a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步：目录扩展
通过目录扩展，可以使用本地 Active Directory 中的属性在 Azure AD 中扩展架构。 此功能可以构建 LOB 应用来使用要在本地持续管理的属性。 可以通过 [Azure AD Graph 目录扩展](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或 [Microsoft Graph](https://graph.microsoft.io/) 使用这些属性。 使用 [Azure AD Graph 资源管理器](https://graphexplorer.azurewebsites.net/)和 [Microsoft Graph 资源管理器](https://developer.microsoft.com/en-us/graph/graph-explorer)分别可以查看可用属性。

目前没有任何 Office 365 工作负荷使用这些属性。

在安装向导的自定义设置路径中配置要同步的其他属性。
![架构扩展向导](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
安装显示以下属性，它们是有效的候选项：

* “用户”和“组”对象类型
* 单值属性：String、Boolean、Integer、Binary
* 多值属性：String、Binary


>[!NOTE]
> 虽然 Azure AD Connect 支持将多值 AD 属性同步到 Azure AD 作为多值目录扩展，但是目前 Azure AD 中没有功能支持使用多值目录扩展。

属性列表是在安装 Azure AD Connect 过程中从架构缓存读取的。 如果已使用附加属性扩展 Active Directory 架构，则只有在[刷新架构](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema)后，这些新属性才可见。

Azure AD 中的对象最多可以有 100 个目录扩展属性。 最大长度为 250 个字符。 如果属性值更长，则会被同步引擎截断。

在安装 Azure AD Connect 期间，会注册可以使用这些属性的应用程序。 可以在 Azure 门户中看到此应用程序。  
![架构扩展应用](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

现在可以通过 Graph 使用这些属性：  
![图形](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

这些属性的前面带有 extension\_{AppClientId}\_ 前缀。 Azure AD 租户中的所有属性具有相同的 AppClientId 值。

## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)配置的详细信息。

了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
