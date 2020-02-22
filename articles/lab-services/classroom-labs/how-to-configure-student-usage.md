---
title: 在 Azure 实验室服务的教室实验室中配置使用设置
description: 了解如何配置实验室的用户数，将用户注册到实验室，控制用户可以使用 VM 的小时数，以及其他内容。
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 9fda1295bcdcd44b8a92c101c2aa445197ce6d4a
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539133"
---
# <a name="add-and-manage-lab-users"></a>添加和管理实验室用户
本文介绍了如何向实验室添加用户，将用户注册到实验室，控制用户可以使用 VM 的小时数，以及其他内容。 


## <a name="add-users-to-the-lab"></a>将用户添加到实验室

1. 选择左侧菜单上的“用户”。 默认情况下，“限制访问”选项处于启用状态。 当此设置处于启用状态时，用户即使有注册链接也不能注册到实验室，除非该用户在用户列表中。 只有列表中的用户可以使用你发送的注册链接注册到实验室。 在此过程中，请将用户添加到列表。 也可关闭“限制访问”，这样一来，用户只要有注册链接就可以注册到实验室。 
2. 选择工具栏中的“添加用户”，然后选择“通过电子邮件地址添加”。 

    ![“添加用户”按钮](../media/how-to-configure-student-usage/add-users-button.png)
1. 在“添加用户”页上，在多个不同的行中输入电子邮件地址，或者在一行中输入以分号分隔的电子邮件地址。 

    ![添加用户电子邮件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 选择“保存”。 可以在列表中看到用户的电子邮件地址及其状态（已注册或未注册）。 

    ![用户列表](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 将用户注册到实验室后，列表中会显示其姓名。 列表中显示的名称是通过在 Azure Active Directory 中使用用户的名字和姓氏来构造的。 

### <a name="add-users-by-uploading-a-csv-file"></a>通过上传 CSV 文件添加用户
还可通过上传包含用户电子邮件地址的 CSV 文件来添加用户。

1. 创建一个 CSV 文件，在其中将用户的电子邮件地址放在一个列中。

    ![带有用户的 CSV 文件](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 在实验室的 "**用户**" 页上，选择工具栏上的 "**添加用户**"，然后选择 "**上载 CSV**"。

    ![“上传 CSV”按钮](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 选择包含用户电子邮件地址的 CSV 文件。 在选择 CSV 文件后选择“打开”时，可以看到以下“添加用户”窗口。 电子邮件地址列表中将填充来自 CSV 文件的电子邮件地址。 

    ![填充有来自 CSV 文件的电子邮件地址的“添加用户”窗口](../media/how-to-configure-student-usage/add-users-window.png)
4. 在“添加用户”窗口中选择“保存”。 
5. 确认你可以在用户列表中看到用户。 

    ![添加的用户的列表](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>向用户发送邀请
若要向学生发送注册链接，请使用以下方法之一。 第一种方法显示了如何使用注册链接和可选消息向学生发送电子邮件。 第二种方法向您演示如何获取注册链接，你可以根据需要以任何方式与他人共享。 

如果为实验室启用了“限制访问”，则只有用户列表中的用户可以使用注册链接注册到实验室。 默认情况下该选项处于启用状态。 

### <a name="invite-all-users"></a>邀请所有用户
1. 切换到“用户”视图（如果尚未转到该页），然后在工具栏上选择“全部邀请”。 

    ![选择学生](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在“通过电子邮件发送邀请”页上，输入可选的消息，然后选择“发送”。 电子邮件自动包含注册链接。 若要获取此注册链接，可以选择工具栏上的“... (省略号)”，然后选择“注册链接”。 

    ![通过电子邮件发送注册链接](../media/tutorial-setup-classroom-lab/send-email.png)
4. 可以在“用户”列表中查看**邀请**状态。 在 **\<日期 > 上**，状态应更改为 "**发送**" 和 "发送"。 

    若要详细了解如何将学生添加到课堂并管理其对实验室的使用，请参阅[如何配置学生对实验室的使用](how-to-configure-student-usage.md)。

### <a name="invite-selected-users"></a>邀请所选用户

1. 在列表中选择一个或多个用户。 
2. 然后，选择工具栏上的 "选择的行" 中显示的**信封**图标（或）选择 "**邀请**"。 

    ![邀请所选用户](../media/how-to-configure-student-usage/invite-selected-users.png)
3. 在 "**通过电子邮件发送邀请**" 窗口中，输入可选**消息**，然后选择 "**发送**"。 

    ![向选定用户发送电子邮件](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    **用户**列表的 "**邀请**" 列中会显示此操作的状态。 邀请电子邮件包含用户可用于向实验室注册的注册链接。

1. 切换到 "**用户**" 视图（如果尚未在页面上）。 

## <a name="get-registration-link"></a>获取注册链接
你还可以从门户获取注册链接，并使用你自己的电子邮件客户端应用程序发送该链接。 

1. 在左侧菜单中选择“用户”，切换到“用户”视图。 
2. 选择 **.。。（省略号）** 在工具栏上，选择 "**注册链接**"。

    ![学生注册链接](../media/how-to-configure-student-usage/registration-link-button.png)
1. 在 "**用户注册**" 对话框中，选择 "**复制**" 按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 

    ![学生注册链接](../media/how-to-configure-student-usage/registration-link.png)
2. 在 "**用户注册**" 对话框中，选择 "**完成**"。 
4. 向学生发送**注册链接**，以便学生可以注册该类。 

## <a name="view-registered-users"></a>查看注册用户

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 
2. 选择“登录”并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。
3. 在“我的实验室”页上，选择要跟踪其使用情况的实验室。 
4. 选择左侧菜单上的“用户”，或选择“用户”磁贴。 此时会看到注册到实验室的学生。  

    ![注册的用户](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>为用户设置配额
可以使用以下步骤来设置每个用户的配额： 

1. 如果页面尚未处于活动状态，请在左侧菜单中选择“用户”。 
2. 在工具栏上选择 "**每用户配额： \<数 > 小时**"。 
3. 在 "**每个用户的配额**" 页上，指定你想要在计划的类时间之外为每个用户（学生）指定的小时数，然后选择 "**保存**"。

    ![每个用户的配额](../media/how-to-configure-student-usage/quota-per-user.png)    
5. 现在，工具栏上已更改的值： "**每个用户的配额： \<小时数 >** 和" 用户 "列表中。

    ![每个用户的配额-之后](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM 的计划运行时间](how-to-create-schedules.md)不计入分配给用户的配额。 配额是指学生在 VM 上花费的计划外时间。 

## <a name="set-additional-quota-for-a-specific-user"></a>为特定用户设置附加配额
可以指定用户的额外配额。 除了之前部分中的所有用户所设置的通用配额外，此配额也是如此。 例如，如果您（作为指导员）将所有用户的配额设置为10小时，并为特定用户设置5小时的额外配额，则该用户将获得15（10 + 5）小时的配额。 如果稍后将常见配额更改为，例如15，则用户将获得20（15 + 5）小时的配额。 请记住，此整体配额在计划时间之外。 学生在计划时间内使用实验室 VM 的时间不计为此配额。 

为此，请执行下列步骤：

1. 从 "**用户**" 页上的用户列表中选择一个用户（student）。
2. 然后，从工具栏中选择 "**调整配额**"。 

    ![调整配额按钮](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. 输入所选用户或用户的**额外小时**数，然后选择 "**应用**"。 

    ![用户的额外配额](../media/how-to-configure-student-usage/additional-quota.png)
4. 你将在 "**使用情况**" 列中看到用户的更新使用情况。 

    ![用户的新使用情况](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>学生帐户
若要将学生添加到课堂实验室，请使用其电子邮件帐户。 可以使用以下类型的电子邮件帐户：

- 大学的 Office 365 Azure Active Directory （AAD）提供的学生电子邮件帐户。 
- Microsoft 电子邮件帐户，如 `@outlook.com`、`@hotmail.com`、`@msn.com`或 `@live.com`。
- 非 Microsoft 电子邮件帐户，如 Yahoo 或 Google 提供的帐户。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 此帐户必须与 Microsoft 帐户链接。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
学生可以使用非 Microsoft 电子邮件帐户来注册和登录到教室实验室。  但注册要求学生首先创建一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多学生可能已经有一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果学生已经将其电子邮件地址与 Microsoft 的其他产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则学生已经有了一个 Microsoft 帐户。  

当学生单击注册 URL 登录到课堂时，系统将提示他们输入其电子邮件地址和密码。 如果学生尝试使用未链接 Microsoft 帐户的非 Microsoft 帐户进行登录，则该学生会收到以下错误消息： 

![错误消息](../media/how-to-configure-student-usage/cant-find-account.png)

若要注册 Microsoft 帐户，学员应[http://signup.live.com](http://signup.live.com)。  

> [!IMPORTANT]
> 学生登录到教室实验室时，不会为他们提供创建 Microsoft 帐户的选项。 因此，我们建议在向使用非 Microsoft 帐户的学生发送的课堂实验室注册电子邮件中包含此注册链接。

### <a name="using-a-github-account"></a>使用 GitHub 帐户
学生还可以使用现有的 GitHub 帐户注册和登录到教室实验室。 如果学生已经有一个链接到其 GitHub 帐户的 Microsoft 帐户，则他们可以登录并提供其密码，如前一部分中所示。 如果尚未将其 GitHub 帐户链接到 Microsoft 帐户，则应选择 "**登录" 选项**：

![登录选项链接](../media/how-to-configure-student-usage/signin-options.png)

在 "**登录选项**" 页面上，选择 "**用 GitHub 登录**"。

![用 GitHub 链接登录](../media/how-to-configure-student-usage/signin-github.png)

最后，系统将提示他们创建链接到其 GitHub 帐户的 Microsoft 帐户。 当学生选择**下一个**时，会自动发生此情况。  然后，该学生会立即登录并连接到教室实验室。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以管理员身份创建并管理实验室帐户](how-to-manage-lab-accounts.md)
- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
