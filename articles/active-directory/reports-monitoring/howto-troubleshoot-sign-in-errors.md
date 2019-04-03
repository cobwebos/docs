---
title: 如何使用 Azure Active Directory 报告对登录错误进行故障排除 | Microsoft Docs
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
ms.openlocfilehash: db68ad2a29dcaa53d219b679b9e0f24a50a6f576
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877051"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>如何：使用 Azure Active Directory 报告对登录错误进行故障排除

使用 Azure Active Directory (Azure AD) 中的[登录报告](concept-sign-ins.md)可找到有关管理组织中应用程序访问权限问题的答案，包括：

- 什么是用户的登录模式？
- 多少用户超过一周都有登录行为？
- 这些登录的状态怎样？


此外，登录报告还可帮助解决组织内用户登录失败的问题。 本指南介绍如何在登录报告中查找登录失败，并用它来了解失败的根本原因。

## <a name="prerequisites"></a>必备组件

需要：

* 一个使用高级 (P1/P2) 许可证的 Azure AD 租户。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。
* 中的用户**全局管理员**，**安全管理员**，**安全读取者**，或者**报告读取器**租户角色。 此外，任何用户都可以访问自己的登录活动。 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>使用登录报告对登录错误进行故障排除

1. 导航到 [Azure 门户](https://portal.azure.com)，选择目录。
2. 在“监视”部分，选择 **Azure Active Directory**，然后选择“登录”。 
3. 使用提供的筛选条件，通过用户名或对象标识符、应用程序名或日期缩小登录失败的范围。 此外，选择**失败**从**状态**下拉列表以显示仅失败的登录。 

    ![筛选结果](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. 识别失败登录想要调查。 选择它以打开更多详细信息窗口有关失败登录的详细信息。 记下“登录错误代码”和“失败原因”。 

    ![选择记录](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. 还可在详细信息窗口中的“疑难解答和支持”选项卡中找到此信息。

    ![故障排除和支持](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. 失败原因是对错误的描述。 例如，在上述情况中，失败原因是**用户名或密码或无效的本地用户名或密码无效**。 解决方法是使用正确的用户名和密码再次登录。

7. 在此示例中，可在[登录错误代码参考](reference-sign-ins-error-codes.md)中搜索错误代码 50126 获取其他信息，包括补救方法。 

8. 如果所有其他方法都失败，或者采取建议的操作步骤后仍然存在问题，请按照“疑难解答和支持”选项卡中的步骤[打开支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)。 

## <a name="next-steps"></a>后续步骤

* [登录错误代码引用](reference-sign-ins-error-codes.md)
* [登录报告概述](concept-sign-ins.md)
