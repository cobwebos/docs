---
title: 对 Azure Active Directory 报告 API 中的错误进行故障排除 | Microsoft Docs
description: 针对调用 Azure Active Directory 报告 API 时遇到的错误提供相应解决方案。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399281"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>对 Azure Active Directory 报告 API 中的错误进行故障排除

本文列出了使用 Microsoft Graph API 访问活动报告时可能会遇到的常见错误消息，以及解决方法的步骤。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>访问 Microsoft Graph V2 终结点时出现 500 HTTP 内部服务器错误

目前，我们不支持 Microsoft Graph v2 终结点，请务必使用 Microsoft Graph v1 终结点访问活动日志。

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>错误: 租户既不是 B2C，也没有 Premium 许可证

访问登录报告需要 Azure Active Directory Premium 1 (P1) 许可证。 如果访问登录时看到此错误消息，请确保你的租户已获得 Azure AD P1 许可证。

### <a name="error-user-is-not-in-the-allowed-roles"></a>错误: 用户不是允许的角色 

如果在尝试使用 API 访问审核日志或登录时看到此错误消息，请确保帐户属于 Azure Active Directory 租户中的“安全读取者”或“报表读取者”角色。 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>错误: 应用程序缺少 AAD“读取目录数据”权限 

请按照[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>错误：应用程序缺少 Microsoft Graph API "读取所有审核日志数据" 权限

请按照[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

## <a name="next-steps"></a>后续步骤

[使用审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[使用登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
