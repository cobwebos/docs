---
title: Azure AD Connect 同步：目录扩展 | Microsoft Docs
description: 本主题介绍 Azure AD Connect 中的目录扩展功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: dda35e63c209951547a667c46639dc0f37c87b43
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593626"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步：目录扩展
通过目录扩展，可以使用本地 Active Directory 中的属性扩展 Azure AD 中的架构。 此功能允许使用继续在本地管理的属性来构建 LOB 应用。 可以通过 [Azure AD Graph API 目录扩展](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或 [Microsoft Graph](https://graph.microsoft.io/) 使用这些属性。 可以使用 [Azure AD Graph 资源管理器](https://graphexplorer.azurewebsites.net/)和 [Microsoft Graph 资源管理器](https://developer.microsoft.com/en-us/graph/graph-explorer)查看可用属性。

目前，没有任何 Office 365 工作负荷使用这些属性。

在安装向导的自定义设置路径中配置要同步的其他属性。

![架构扩展向导](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  

安装显示以下属性，它们是有效的候选项：

* “用户”和“组”对象类型
* 单值属性：String、Boolean、Integer、Binary
* 多值属性：String、Binary


>[!NOTE]
> Azure AD Connect 支持将多值 Active Directory 属性作为多值目录扩展同步到 Azure AD。 但是，Azure AD 中的任何功能当前都不支持使用多值目录扩展。

属性列表是从安装 Azure AD Connect 期间创建的架构缓存中读取的。 如果已使用附加属性扩展了 Active Directory 架构，则必须[刷新架构](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema)，然后这些新属性才可见。

Azure AD 中的对象最多可以有 100 个目录扩展属性。 最大长度为 250 个字符。 如果属性值更长，则同步引擎会将其截断。

在安装 Azure AD Connect 期间，会注册可以使用这些属性的应用程序。 可以在 Azure 门户中看到此应用程序。

![架构扩展应用](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

这些属性带有 extension \_{AppClientId}\_ 前缀。 对于 Azure AD 租户中的所有属性，AppClientId 具有相同的值。

现在可以通过 Azure AD Graph API 使用这些属性。 可以使用 [Azure AD Graph 资源管理器](https://graphexplorer.azurewebsites.net/)查询它们。

![Azure AD Graph 资源管理器](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

另外，也可以使用 [Microsoft Graph 资源管理器](https://developer.microsoft.com/en-us/graph/graph-explorer#)通过 Microsoft Graph API 查询属性。

>[!NOTE]
> 你需要指定要返回的属性。 如下所示显式选择属性：https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division。 
>
> 有关详细信息，请参阅 [Microsoft Graph: Use query parameters](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter)（Microsoft Graph：使用查询参数）。

## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)配置的详细信息。

了解有关 [将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md)的详细信息。
