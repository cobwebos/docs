---
title: 在 Azure 实验室服务中添加用户作为实验室创建者
description: 本文介绍如何将用户添加到 Azure 实验室服务中的实验室帐户的实验室创建者角色。 实验室创建者可以在此实验室帐户中创建实验室。
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444766"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>向 Azure 实验室服务中的实验室帐户添加实验室创建者
本文介绍如何将用户作为实验室创建者添加到 Azure 实验室服务中的实验室帐户。 然后，这些使用可以在实验室帐户中创建教室实验室。 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>将 Microsoft 用户帐户添加到 Lab Creator 角色
若要在实验室帐户中设置课堂实验室，用户必须是实验室帐户中“实验室创建者”角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 如果打算使用同一用户帐户创建课堂实验室，可以跳过此步骤。 若要使用其他用户帐户创建教室实验室，请执行以下步骤： 

为教师提供为课程创建实验室的权限，并将他们添加为**实验室创建者**角色：

1. 在“实验室帐户”页上，选择“访问控制(IAM)”，然后单击工具栏中的“+ 添加角色分配”。 

    ![“访问控制”->“添加角色分配”按钮](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在“添加角色分配”页上，选择“实验室创建者”作为“角色”，选择想要添加到“实验室创建者”角色的用户，然后选择“保存”。 

    ![添加实验室创建者](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > 如果要添加非 Microsoft 帐户用户作为实验室创建者，请参阅[将非 Microsoft 帐户用户添加为实验室创建者](#add-a-non-microsoft-account-user-as-a-lab-creator)部分。 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>添加非 Microsoft 帐户用户作为实验室创建者
若要将用户添加为实验室创建者，请使用其电子邮件帐户。 可以使用以下类型的电子邮件帐户：

- 大学办公室 365 Azure Active Directory （AAD）提供的电子邮件帐户。 
- Microsoft 电子邮件帐户，如 `@outlook.com`、`@hotmail.com`、`@msn.com`或 `@live.com`。
- 非 Microsoft 电子邮件帐户，如 Yahoo 或 Google 提供的帐户。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 此帐户必须与 Microsoft 帐户链接。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
实验室创建者/讲师可以使用非 Microsoft 电子邮件帐户来注册和登录到教室实验室。  但是，登录到实验室服务门户要求讲师首先创建一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多教师可能已经有一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果讲师已经将其电子邮件地址与 Microsoft 的其他产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则该 Microsoft 帐户。  

当教师登录到实验室服务门户时，会提示他们输入其电子邮件地址和密码。 如果讲师尝试使用未链接 Microsoft 帐户的非 Microsoft 帐户进行登录，则讲师将收到以下错误消息： 

![错误消息](../media/how-to-configure-student-usage/cant-find-account.png)

若要注册 Microsoft 帐户，讲师应使用[http://signup.live.com](http://signup.live.com)。  


### <a name="using-a-github-account"></a>使用 GitHub 帐户
讲师还可以使用现有的 GitHub 帐户注册和登录到教室实验室。 如果讲师已经有一个链接到其 GitHub 帐户的 Microsoft 帐户，则他们可以登录并提供其密码，如前一部分中所示。 如果尚未将其 GitHub 帐户链接到 Microsoft 帐户，则应选择 "**登录" 选项**：

![登录选项链接](../media/how-to-configure-student-usage/signin-options.png)

在 "**登录选项**" 页面上，选择 "**用 GitHub 登录**"。

![用 GitHub 链接登录](../media/how-to-configure-student-usage/signin-github.png)

最后，系统将提示他们创建链接到其 GitHub 帐户的 Microsoft 帐户。 当讲师选择**下一个**时，会自动发生这种情况。  然后，教师会立即登录并连接到教室实验室。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
