---
title: 在 Azure 实验室服务中将用户添加为实验室创建者
description: 本文演示如何将用户添加到 Azure 实验室服务中的实验室帐户的实验室创建者角色。 实验室创建者可以在此实验室帐户中创建实验室。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444766"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>将实验室创建者添加到 Azure 实验室服务中的实验室帐户
本文介绍如何将用户作为实验室创建者添加到 Azure 实验室服务中的实验室帐户。 这些用途可以在实验室帐户中创建教室实验室。 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>将 Microsoft 用户帐户添加到实验室创建者角色
若要在实验室帐户中设置课堂实验室，用户必须是实验室帐户中“实验室创建者”**** 角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 如果打算使用同一用户帐户创建课堂实验室，可以跳过此步骤。 若要使用其他用户帐户创建教室实验室，请执行以下步骤： 

为教师提供为课程创建实验室的权限，并将他们添加为**实验室创建者**角色：

1. 在“实验室帐户”页上，选择“访问控制(IAM)”，然后单击工具栏中的“+ 添加角色分配”************。 

    ![“访问控制”->“添加角色分配”按钮](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在“添加角色分配”页上，选择“实验室创建者”作为“角色”，选择想要添加到“实验室创建者”角色的用户，然后选择“保存”****************。 

    ![添加实验室创建者](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > 如果要将非 Microsoft 帐户用户添加为实验室创建者，请参阅[将非 Microsoft 帐户用户添加为实验室创建者](#add-a-non-microsoft-account-user-as-a-lab-creator)部分。 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>将非 Microsoft 帐户用户添加为实验室创建者
要将用户添加为实验室创建者，请使用其电子邮件帐户。 可以使用以下类型的电子邮件帐户：

- 由大学 Office 365 Azure 活动目录 （AAD） 提供的电子邮件帐户。 
- 微软电子邮件帐户`@outlook.com`，如 、`@hotmail.com``@msn.com`或 。 `@live.com`
- 非微软电子邮件帐户，如雅虎或谷歌提供的电子邮件帐户。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 此帐户必须与 Microsoft 帐户链接。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
实验室创建者/讲师可以使用非 Microsoft 电子邮件帐户注册并登录到教室实验室。  但是，登录到实验室服务门户要求教师首先创建链接到其非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多教师可能已经将 Microsoft 帐户链接到其非 Microsoft 电子邮件地址。 例如，如果教师将电子邮件地址用于 Microsoft 的其他产品或服务（如 Office、Skype、OneDrive 或 Windows），则他们已拥有 Microsoft 帐户。  

当教师登录到实验室服务门户时，系统会提示他们输入电子邮件地址和密码。 如果讲师尝试使用未链接 Microsoft 帐户的非 Microsoft 帐户登录，讲师将收到以下错误消息： 

![错误消息](../media/how-to-configure-student-usage/cant-find-account.png)

要注册 Microsoft 帐户，讲师应转到[http://signup.live.com](http://signup.live.com)。  


### <a name="using-a-github-account"></a>使用 GitHub 帐户
教师还可以使用现有的 GitHub 帐户注册并登录到教室实验室。 如果教师已经拥有链接到其 GitHub 帐户的 Microsoft 帐户，则可以登录并提供其密码，如上一节所示。 如果他们尚未将其 GitHub 帐户链接到 Microsoft 帐户，则应选择**登录选项**：

![登录选项链接](../media/how-to-configure-student-usage/signin-options.png)

在 **"登录选项**"页上，选择**使用 GitHub 登录**。

![使用 GitHub 链接登录](../media/how-to-configure-student-usage/signin-github.png)

最后，系统会提示他们创建链接到其 GitHub 帐户的 Microsoft 帐户。 当讲师选择 **"下一步**"时，它会自动发生。  然后，教师立即登录并连接到教室实验室。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
