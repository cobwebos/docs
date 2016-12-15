---
title: "Azure AD 报告 API 入门 | Microsoft 文档"
description: "如何开始使用 Azure Active Directory 报告 API"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3a8e9c802c265dacd1b8c3688855ce6ec0d90bb1


---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory 报告 API 入门
*本主题是 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)的一部分。*

Azure Active Directory 可向你提供各种报告。 对于你的应用程序（如 SIEM 系统、审核）和商业智能工具，这些报告的数据可能非常有用。 Azure AD 报告 API 通过一组基于 REST 的 API，可提供对该数据的编程访问权限。 可从各种编程语言和工具中调用这些 API。

本文介绍了开始使用 Azure AD 报告 API 所需的信息。
在下一部分中，可以找到有关使用审核和登录 API 的更多详细信息。 对于所有其他 API，请参阅 [Azure AD 报告和事件（预览）](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview)一文。

有关疑问、问题或反馈，请联系 [AAD 报告帮助](mailto:aadreportinghelp@microsoft.com)。

## <a name="learning-map"></a>学习路线图
1. **准备** - 需要先完成[访问 Azure AD 报告 API 的先决条件](active-directory-reporting-api-prerequisites.md)，然后才能测试你的 API 示例。
2. **探索** - 获取报告 API 的第一印象：
   
   * [使用审核 API 的示例](active-directory-reporting-api-audit-samples.md) 
   * [使用登录活动报告 API 的示例](active-directory-reporting-api-sign-in-activity-samples.md)
3. **自定义** - 创建你自己的解决方案： 
   
   * [使用审核 API 参考](active-directory-reporting-api-audit-reference.md) 
   * [使用登录活动报告 API 参考](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>后续步骤
如果你想要查看所有可用的 Azure AD 图形 API 终结点，请导航到 [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta)。




<!--HONumber=Nov16_HO3-->


