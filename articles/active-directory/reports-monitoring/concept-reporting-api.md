---
title: Azure AD 报告 API 入门 | Microsoft Docs
description: 如何开始使用 Azure Active Directory 报告 API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7de7c87e5cf1747f4899f33d9e6b9cbf0bb430e1
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2018
ms.locfileid: "42139822"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory 报告 API 入门

Azure Active Directory 向你提供了各种[报告](overview-reports.md)。 对于应用程序（如 SIEM 系统、审核）和商业智能工具，这些报告的数据可能非常有用。 

使用 Azure AD 报告 API，可通过一组基于 REST 的 API 以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。

本文为你提供了使用相关 API 访问报告数据的路线图。

如果遇到问题，请参阅[如何获取对 Azure Active Directory 的支持](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)。


## <a name="prerequisites"></a>先决条件

若要访问报告 API，即使你打算使用脚本访问该 API，也需要：

1. 分配角色（安全读者、安全管理员、全局管理员）
2. 注册应用程序
3. 授予权限
4. 收集配置设置

有关详细说明，请参阅[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)。

## <a name="apis-with-graph-explorer"></a>Graph 浏览器的 API

可以使用 [MSGraph 浏览器](https://developer.microsoft.com/graph/graph-explorer)来验证登录和审核 API 数据。 确保使用 Graph 浏览器 UI 中的两个登录按钮登录帐户，并为租户设置 **AuditLog.Read.All** 和 **Directory.Read.All** 权限，如下所示。   

![Graph 浏览器](./media/concept-reporting-api/graph-explorer.png)

![修改权限 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>使用证书访问 Azure AD 报告 API 

如果计划在无用户干预的情况下检索报告数据，请使用具有证书的 Azure AD 报告 API。

有关详细说明，请参阅[使用具有证书的 Azure AD 报告 API 获取数据](tutorial-access-api-with-certificates.md)。


## <a name="next-steps"></a>后续步骤

 * [审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [排查 Azure AD 报告 API 中的错误](troubleshoot-graph-api.md)


