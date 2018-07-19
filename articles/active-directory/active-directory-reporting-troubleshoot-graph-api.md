---
title: 对 Azure Active Directory 报告 API 中的错误进行故障排除 | Microsoft Docs
description: 针对调用 Azure Active Directory 报告 API 时遇到的错误提供相应解决方案。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: a05cb2325dc193b9d96e5d454cb2d934aa926a02
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906203"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>对 Azure Active Directory 报告 API 中的错误进行故障排除

本文列出了使用 MS Graph API 访问活动报告时可能遇到的常见错误消息及其解决步骤。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>访问 Microsoft Graph V2 终结点时出现 500 HTTP 内部服务器错误

目前，我们不支持 Microsoft Graph v2 终结点，请务必使用 Microsoft Graph v1 终结点访问活动日志。

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>错误: 无法从 AD Graph 获取用户角色

尝试使用 Graph 浏览器访问登录时，可能会收到此错误消息。 确保使用 Graph 浏览器 UI 中的两个登录按钮登录帐户，如下图所示。 

![Graph 浏览器](./media/active-directory-reporting-troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>错误: 无法从 AD Graph 执行 Premium 许可证检查 

如果尝试使用 Graph 浏览器访问登录时收到此错误消息，请在左侧导航栏中选择帐户下方的“修改权限”，然后选择“Tasks.ReadWrite”和“Directory.Read.All”。 

![修改权限 UI](./media/active-directory-reporting-troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>错误: 租户既不是 B2C，也没有 Premium 许可证

访问登录报告需要 Azure Active Directory Premium 1 (P1) 许可证。 如果访问登录时看到此错误消息，请确保你的租户已获得 Azure AD P1 许可证。

### <a name="error-user-is-not-in-the-allowed-roles"></a>错误: 用户不是允许的角色 

如果在尝试使用 API 访问审核日志或登录时看到此错误消息，请确保帐户属于 Azure Active Directory 租户中的“安全读者”或“报表读者”角色。 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>错误: 应用程序缺少 AAD“读取目录数据”权限 

请按照[访问 Azure Active Directory 报告 API 的先决条件](active-directory-reporting-api-prerequisites-azure-portal.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>错误: 应用程序缺少 MSGraph API“读取所有审核日志数据”权限

请按照[访问 Azure Active Directory 报告 API 的先决条件](active-directory-reporting-api-prerequisites-azure-portal.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

## <a name="next-steps"></a>后续步骤

[使用审核 API 参考](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/directoryaudit)
[使用登录活动报告 API 参考](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/signin)