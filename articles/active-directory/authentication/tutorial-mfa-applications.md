---
title: 启用 Azure 多重身份验证试点
description: 在本教程中，你将为试点用户组启用 Azure AD 多重身份验证
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 098973e2ece3477ec87b154c0304c4ca7e0246d1
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163264"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>教程：完成 Azure 多重身份验证试验试点推广

本教程将引导你配置一个条件访问策略，以便在登录到 Azure 门户时启用 Azure 多重身份验证 (Azure MFA)。 此策略是针对特定的试点用户组进行部署和测试的。 与传统的强制方法相比，使用条件访问部署 Azure MFA 为组织和管理员提供了极大的灵活性。

> [!div class="checklist"]
> * 启用 Azure 多重身份验证
> * 测试 Azure 多重身份验证

## <a name="prerequisites"></a>先决条件

* 一个至少启用了试用版许可证的有效 Azure AD 租户。
* 一个具有全局管理员权限的帐户。
* 一个用于测试的你知道其密码的非管理员测试用户，如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../add-users-azure-active-directory.md)一文。
* 非管理员用户所属的用于测试的一个试点组，如果需要创建组，请参阅[在 Azure Active Directory 中创建组并添加成员](../active-directory-groups-create-azure-portal.md)一文。

## <a name="enable-azure-multi-factor-authentication"></a>启用 Azure 多重身份验证

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory”、“条件访问”。
1. 选择“新建策略”
1. 将策略命名为“MFA 试点”
1. 在“用户和组”下，选择“选择用户和组”单选按钮
    * 选择在本文的先决条件部分中创建的试点组
    * 单击“完成” 
1. 在“云应用”下，选择“选择应用”单选按钮
    * Azure 门户的云应用是 **Microsoft Azure 管理**
    * 单击“选择”
    * 单击“完成” 
1. 跳过“条件”部分
1. 在“授予”下，确保选择“授权访问”单选按钮
    * 选中“要求多重身份验证”复选框
    * 单击“选择”
1. 跳过“会话”部分
1. 将“启用策略”开关设置为“开”
1. 单击“创建” 

## <a name="test-azure-multi-factor-authentication"></a>测试 Azure 多重身份验证

为了证明条件访问策略正常工作，通过以下方式进行测试：登录到不要求 MFA 的某个资源，然后登录到要求 MFA 的 Azure 门户。

1. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)。
   * 使用在本文的先决条件部分中创建的测试用户进行登录，注意，应当不会要求你完成 MFA。
   * 关闭浏览器窗口。
2. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://portal.azure.com](https://portal.azure.com)。
   * 使用在本文的先决条件部分中创建的测试用户进行登录，注意，现在应当会要求你注册并使用 Azure 多重身份验证。
   * 关闭浏览器窗口。

## <a name="clean-up-resources"></a>清理资源

如果你决定不再使用作为本教程的一部分配置的功能，请进行以下更改。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory”、“条件访问”。
1. 选择你创建的条件访问策略。
1. 单击“删除” 。

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用了 Azure 多重身份验证。 请继续学习下一教程来了解如何将 Azure Identity Protection 集成到自助服务密码重置和多重身份验证体验。

> [!div class="nextstepaction"]
> [在登录时评估风险](tutorial-risk-based-sspr-mfa.md)