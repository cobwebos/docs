---
title: 如何使用 Azure Active Directory 报告对登录错误进行故障排除 | Microsoft Docs
description: 了解如何在 Azure 门户中使用 Azure Active Directory 报告对登录错误进行故障排除
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22126114f2d4fcb865485d9cebc69f0e35f70201
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198467"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>如何：使用 Azure Active Directory 报告对登录错误进行故障排除

使用 Azure Active Directory (Azure AD) 中的[登录报告](concept-sign-ins.md)可找到有关管理组织中应用程序访问权限问题的答案，包括：

- 什么是用户的登录模式？
- 多少用户超过一周都有登录行为？
- 这些登录的状态怎样？


此外，登录报告还可帮助解决组织内用户登录失败的问题。 本指南介绍如何在登录报告中查找登录失败，并用它来了解失败的根本原因。

## <a name="prerequisites"></a>先决条件

需要：

* 一个使用高级 (P1/P2) 许可证的 Azure AD 租户。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。
* 一位属于该租户的全局管理员、安全管理员、安全读者或报表读者角色的用户。 此外，任何用户都可以访问自己的登录活动。 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>使用登录报告对登录错误进行故障排除

1. 导航到 [Azure 门户](https://portal.azure.com)，选择目录。
2. 在“监视”部分，选择 **Azure Active Directory**，然后选择“登录”。 
3. 使用提供的筛选条件，通过用户名或对象标识符、应用程序名或日期缩小登录失败的范围。 此外可选择“状态”下拉框中的“失败”，仅显示失败的登录。 

    ![筛选结果](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. 确定要调查的失败登录并选择。 此操作将打开其他详细信息窗口，其中包含有关失败登录的详细信息。 记下“登录错误代码”和“失败原因”。 

    ![选择记录](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. 还可在详细信息窗口中的“疑难解答和支持”选项卡中找到此信息。

    ![故障排除和支持](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. 失败原因是对错误的描述。 例如，在上面的方案中，失败原因是“无效的用户名或密码，或无效的本地用户名或密码”。 这意味着用户输入了错误的用户名或密码来登录 Azure 门户。 解决方法是使用正确的用户名和密码再次登录。

7. 在此示例中，可在[登录错误代码参考](reference-sign-ins-error-codes.md)中搜索错误代码 50126 获取其他信息，包括补救方法。 

8. 如果所有其他方法都失败，或者采取建议的操作步骤后仍然存在问题，请按照“疑难解答和支持”选项卡中的步骤[打开支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。 

## <a name="next-steps"></a>后续步骤

* [登录错误代码参考](reference-sign-ins-error-codes.md)
* [登录报告概述](concept-sign-ins.md)
