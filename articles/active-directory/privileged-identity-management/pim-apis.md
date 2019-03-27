---
title: 适用于 PIM 的 Microsoft Graph API（预览版） | Microsoft Docs
description: 介绍如何使用适用于 Azure Active Directory Privileged Identity Management (PIM) 的 Microsoft Graph API（预览版）。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d853579335653987321fded154eb9fb0f8dbdb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000286"
---
# <a name="microsoft-graph-apis-for-pim-preview"></a>适用于 PIM 的 Microsoft Graph API（预览版）

大多数可以使用 Azure 门户在 Azure AD Privileged Identity Management (PIM) 中执行的任务也可使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/overview) 来执行。 本文介绍使用适用于 PIM 的 Microsoft Graph API 时涉及的一些重要概念。 有关 Microsoft Graph API 的详细信息，请查看 [Azure AD Privileged Identity Management API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)。

> [!IMPORTANT]
> Microsoft Graph 中 /beta 版本下的 API 为预览版，可能会进行更改。 不支持在生产应用程序中使用这些 API。

## <a name="required-permissions"></a>所需的权限

若要调用适用于 PIM 的 Microsoft Graph API，你必须拥有下述权限中的**一项或多项**：

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>设置权限

应用程序必须具有必要的权限才能调用适用于 PIM 的 Microsoft Graph API。 若要指定所需的权限，最容易的方式是使用 [Azure AD 许可框架](../develop/consent-framework.md)。

### <a name="set-permissions-in-graph-explorer"></a>在 Graph 浏览器中设置权限

如果使用 Graph 浏览器来测试调用，则可在工具中指定权限。

1. 以全局管理员身份登录到 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

1. 单击“修改权限”。

    ![Graph 浏览器 - 修改权限](./media/pim-apis/graph-explorer.png)

1. 在要包括的权限旁边添加复选标记。 `PrivilegedAccess.ReadWrite.AzureAD` 在 Graph 浏览器中尚不可用。

    ![Graph 浏览器 - 修改权限](./media/pim-apis/graph-explorer-modify-permissions.png)

1. 单击“修改权限”以应用权限更改。

## <a name="next-steps"></a>后续步骤

- [Azure AD Privileged Identity Management API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
