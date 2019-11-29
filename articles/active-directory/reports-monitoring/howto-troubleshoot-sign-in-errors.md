---
title: 如何排查登录错误报告 |Microsoft Docs
description: 了解如何在 Azure 门户中使用 Azure Active Directory 报告对登录错误进行故障排除
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008063"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>操作方法：使用 Azure Active Directory 报告对登录错误进行故障排除

使用 Azure Active Directory (Azure AD) 中的[登录报告](concept-sign-ins.md)可找到有关管理组织中应用程序访问权限问题的答案，包括：

- 什么是用户的登录模式？
- 多少用户超过一周都有登录行为？
- 这些登录的状态怎样？


此外，登录报告还可帮助解决组织内用户登录失败的问题。 本指南介绍如何在登录报告中查找登录失败，并用它来了解失败的根本原因。

## <a name="prerequisites"></a>先决条件

需要：

* 一个使用高级 (P1/P2) 许可证的 Azure AD 租户。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。
* 用户，属于该租户的**全局管理员**、**安全管理员**、**安全读者**或**报表读者**角色。 此外，任何用户都可以访问自己的登录活动。 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>使用登录报告对登录错误进行故障排除

1. 导航到 [Azure 门户](https://portal.azure.com)，选择目录。
2. 在“监视”部分，选择 **Azure Active Directory**，然后选择“登录”。 
3. 使用提供的筛选条件，通过用户名或对象标识符、应用程序名或日期缩小登录失败的范围。 此外，从**状态**下拉选择 "**失败**" 以仅显示失败的登录。 

    ![筛选结果](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. 确定要调查的登录失败。 选择它以打开 "其他详细信息" 窗口，其中包含有关登录失败的详细信息。 记下“登录错误代码”和“失败原因”。 

    ![选择记录](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. 还可在详细信息窗口中的“疑难解答和支持”选项卡中找到此信息。

    ![故障排除和支持](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. 失败原因是对错误的描述。 例如，在上面的方案中，失败原因是**无效的用户名或密码或者本地用户名或密码无效**。 解决方法是使用正确的用户名和密码再次登录。

7. 在此示例中，可在**登录错误代码参考**中搜索错误代码 50126 获取其他信息，包括补救方法[](reference-sign-ins-error-codes.md)。 

8. 如果所有其他方法都失败，或者采取建议的操作步骤后仍然存在问题，请按照“疑难解答和支持”选项卡中的步骤[打开支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。 

## <a name="next-steps"></a>后续步骤

* [登录错误代码参考](reference-sign-ins-error-codes.md)
* [登录报告概述](concept-sign-ins.md)
