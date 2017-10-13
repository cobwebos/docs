---
title: "Azure AD 报告 API 入门 | Microsoft 文档"
description: "如何开始使用 Azure Active Directory 报告 API"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9944cbd2b1b7c4acb18d37da1394c0bbc170f77d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory 报告 API 入门

Azure Active Directory 可向你提供各种报告。 对于应用程序（如 SIEM 系统、审核）和商业智能工具，这些报告的数据可能非常有用。 Azure AD 报告 API 通过一组基于 REST 的 API，可提供对该数据的编程访问权限。 可从各种编程语言和工具中调用这些 API。

本文介绍了开始使用 Azure AD 报告 API 所需的信息。
在下一部分中，可以找到有关使用审核和登录 API 的更多详细信息。 

有关常见问题解答，请参阅我们的[常见问题解答](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq)。 有关问题，请[提交支持票证](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)

## <a name="learning-map"></a>学习路线图
1. **准备** - 需要先完成[访问 Azure AD 报告 API 的先决条件](active-directory-reporting-api-prerequisites-azure-portal.md)，才能测试 API 示例。
2. **探索** - 获取报告 API 的第一印象：
   
   * [使用审核 API 的示例](active-directory-reporting-api-audit-samples.md) 
   * [使用登录活动报告 API 的示例](active-directory-reporting-api-sign-in-activity-samples.md)
3. **自定义** - 创建自己的解决方案： 
   
   * [使用审核 API 参考](active-directory-reporting-api-audit-reference.md) 
   * [使用登录活动报告 API 参考](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>后续步骤
如果想要查看所有可用的 Azure AD 图形 API 终结点，请使用此链接：[https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta)。

