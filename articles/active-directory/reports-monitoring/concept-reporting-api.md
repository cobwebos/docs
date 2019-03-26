---
title: Azure AD 报告 API 入门 | Microsoft Docs
description: 如何开始使用 Azure Active Directory 报告 API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ff3e530dae3a6db4b7c84292a25e83c11000baf
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437758"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory 报告 API 入门

Azure Active Directory 提供了各种[报告](overview-reports.md)，其中包含有关 SIEM 系统、审核和商业智能工具等应用程序的有用信息。 

使用 Microsoft Graph API for Azure AD 报告，可通过一组基于 REST 的 API 以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。

本文提供了报告 API 的概述，包括访问它的方法。

如果遇到问题，请参阅[如何获取对 Azure Active Directory 的支持](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)。

## <a name="prerequisites"></a>必备组件

若要访问报告 API，无论是否需要用户干预，都需要：

1. 分配角色（安全读者、安全管理员、全局管理员）
2. 注册应用程序
3. 授予权限
4. 收集配置设置

有关详细说明，请参阅[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)。 

## <a name="api-endpoints"></a>API 终结点 

用于审核日志的 Microsoft Graph API 终结点为 `https://graph.microsoft.com/beta/auditLogs/directoryAudits`，用于登录的 Microsoft Graph API 终结点为 `https://graph.microsoft.com/beta/auditLogs/signIns`。 有关详细信息，请参阅[审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)和[登录 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn)。

此外，还可以使用 [Identity Protection 风险事件 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) 通过 Microsoft Graph 以编程方式访问安全检测。 有关详细信息，请参阅 [Azure Active Directory Identity Protection 和 Microsoft Graph 入门](../identity-protection/graph-get-started.md)。 

> [!NOTE]
>  **https:\/\/graph.windows.net\/\<tenant-name\>\/reports\/** 终结点已被弃用。 请使用上述新的 API 终结点以编程方式访问活动和安全报告。
  
## <a name="apis-with-graph-explorer"></a>Graph 浏览器的 API

可以使用 [MSGraph 浏览器](https://developer.microsoft.com/graph/graph-explorer)来验证登录和审核 API 数据。 确保使用 Graph 浏览器 UI 中的两个登录按钮登录帐户，并为租户设置 **AuditLog.Read.All** 和 **Directory.Read.All** 权限，如下所示。   

![Graph 浏览器](./media/concept-reporting-api/graph-explorer.png)

![修改权限 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>使用证书访问 Azure AD 报告 API 

如果计划在无用户干预的情况下检索报告数据，请使用具有证书的 Azure AD 报告 API。

有关详细说明，请参阅[使用具有证书的 Azure AD 报告 API 获取数据](tutorial-access-api-with-certificates.md)。

## <a name="next-steps"></a>后续步骤

 * [访问报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md) 
 * [使用证书通过 Azure AD 报告 API 获取数据](tutorial-access-api-with-certificates.md)
 * [排查 Azure AD 报告 API 中的错误](troubleshoot-graph-api.md)


