---
title: 适用于 PIM 的 Microsoft Graph API（预览版） - Azure Active Directory | Microsoft Docs
description: 介绍如何使用适用于 Azure AD Privileged Identity Management (PIM) 的 Microsoft Graph API（预览版）。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 11/13/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49d49f42e0d705981a5b4e41630b425fcb02e940
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756260"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>适用于 Privileged Identity Management 的 Microsoft Graph API（预览版）

可以使用 Azure Active Directory 的 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/overview) 执行 Privileged Identity Management 任务。 本文介绍使用适用于 Privileged Identity Management 的 Microsoft Graph API 的重要概念。

有关 Microsoft Graph API 的详细信息，请查看 [Azure AD Privileged Identity Management API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)。

> [!IMPORTANT]
> Microsoft Graph 中 /beta 版本下的 API 为预览版，可能会进行更改。 不支持在生产应用程序中使用这些 API。

## <a name="required-permissions"></a>所需的权限

若要调用适用于 Privileged Identity Management 的 Microsoft Graph API，必须拥有下述权限中的**一项或多项**：

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>设置权限

应用程序必须具有必要的权限才能调用适用于 Privileged Identity Management 的 Microsoft Graph API。 若要指定所需的权限，最容易的方式是使用 [Azure AD 许可框架](../develop/consent-framework.md)。

### <a name="set-permissions-in-graph-explorer"></a>在 Graph 浏览器中设置权限

如果使用 Graph 浏览器来测试调用，则可在工具中指定权限。

1. 以全局管理员身份登录到 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

1. 单击“修改权限”。 

    ![Graph 浏览器 - 修改权限](./media/pim-apis/graph-explorer.png)

1. 选中要包括的权限旁边的复选框。 `PrivilegedAccess.ReadWrite.AzureAD` 在 Graph 浏览器中尚不可用。

    ![Graph 浏览器 - 修改权限](./media/pim-apis/graph-explorer-modify-permissions.png)

1. 单击“修改权限”以应用权限更改。 

## <a name="next-steps"></a>后续步骤

- [Azure AD Privileged Identity Management API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/privilegedidentitymanagement_root)
