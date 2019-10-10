---
title: 在 Azure AD 授权管理（预览版）中将访问管理委派给目录创建者-Azure Active Directory
description: 了解如何将 IT 管理员的访问管理委派给目录创建者和项目经理，使他们能够自行管理访问权限。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170758"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 授权管理（预览版）中将访问管理委派给目录创建者

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

若要委派给不是管理员的用户，以便他们可以创建自己的目录，你可以将这些用户添加到 Azure AD 的授权管理定义的目录创建者角色。 您可以添加单个用户，也可以添加一个组，其成员可以创建目录。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>作为 IT 管理员，委派给目录创建者

遵循以下步骤将用户分配到目录创建者角色。

**必备角色：** 全局管理员或用户管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在“权利管理”部分的左侧菜单中，单击“设置”。

1. 单击“编辑”。

    ![用于添加目录创建者的设置](./media/entitlement-management-delegate/settings-delegate.png)

1. 在 "**委派权利管理**" 部分中，单击 "**添加目录创建者**"，选择要将此权利管理角色委派到的用户或组。

1. 单击“选择”。

1. 单击“保存”。

## <a name="next-steps"></a>后续步骤

- [创建和管理资源的目录](entitlement-management-catalog-create.md)
- [委托访问控制以访问包管理器](entitlement-management-delegate-managers.md)

