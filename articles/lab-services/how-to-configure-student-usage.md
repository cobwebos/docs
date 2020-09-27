---
title: 在 Azure 实验室服务的教室实验室中配置使用设置
description: 了解如何配置实验室的学生数量，如何将学生注册到实验室，控制他们可使用 VM 的小时数，等等。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 742fcd2b504bce7ad2257edd559e4b66a9172d24
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399663"
---
# <a name="add-and-manage-lab-users"></a>添加和管理实验室用户

本文介绍如何将学生用户添加到实验室、向实验室注册这些用户、控制他们可使用虚拟机 (VM) 等其他小时数。 

## <a name="add-users-to-a-lab"></a>将用户添加到实验室

在本部分中，将学生手动添加到实验室或上传 CSV 文件。 执行以下操作：

1. 在左窗格中，选择“用户”****。 

    默认情况下，" **限制访问** " 选项处于打开状态，除非它们位于用户列表中，否则学生无法注册到实验室，即使他们具有注册链接。 只有列出的用户才能使用发送的注册链接注册到实验室。 在此过程中，请将用户添加到列表。 或者，你可以关闭 " **限制访问**"，它允许学生注册到实验室，前提是他们具有注册链接。 

1. 在 " **用户** " 窗格的顶部，选择 " **添加用户**"，然后选择 " **按电子邮件地址添加**"。 

    !["添加用户" 按钮](./media/how-to-configure-student-usage/add-users-button.png)

1. 在 " **添加用户** " 窗格中，在单独的行或以分号分隔的单个行上输入学生的电子邮件地址。 

    ![添加用户的电子邮件地址](./media/how-to-configure-student-usage/add-users-email-addresses.png)

1. 选择“保存”。 

    该列表显示当前用户的电子邮件地址和状态，无论它们是否已注册到实验室。 

    ![用户列表](./media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 学生注册到实验室后，列表将显示其名称。 列表中显示的名称是使用 Azure Active Directory 中学生的名字和姓氏来构造的。 

### <a name="add-users-by-uploading-a-csv-file"></a>通过上传 CSV 文件添加用户

你还可以通过上载包含其电子邮件地址的 CSV 文件来添加用户。

1. 在 Microsoft Excel 中，创建一个在一列中列出学生电子邮件地址的 CSV 文件。

    ![CSV 文件中的用户列表](./media/how-to-configure-student-usage/csv-file-with-users.png)

1. 在 " **用户** " 窗格的顶部，选择 " **添加用户**"，然后选择 " **上载 CSV**"。

    !["上传 CSV" 按钮](./media/how-to-configure-student-usage/upload-csv-button.png)

1. 选择包含学生电子邮件地址的 CSV 文件，然后选择 " **打开**"。

    " **添加用户** " 窗口将显示 CSV 文件中的电子邮件地址列表。 

    !["添加用户" 窗口，其中包含 CSV 文件中的电子邮件地址](./media/how-to-configure-student-usage/add-users-window.png)

1. 选择“保存”。 

1. 在 " **用户** " 窗格中，查看添加的学生列表。 

    !["用户" 窗格中已添加用户的列表](./media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>向用户发送邀请

若要向新用户发送注册链接，请使用以下方法之一。 

如果为实验室启用了 " **限制访问** " 选项，则只有列出的用户才能使用注册链接注册到实验室。 默认情况下会启用此选项。 

### <a name="invite-all-users"></a>邀请所有用户

此方法说明如何向所有列出的学生发送包含注册链接和可选消息的电子邮件。

1. 在 " **用户** " 窗格中，选择 " **全部邀请**"。 

    !["全部邀请" 按钮](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在 " **通过电子邮件发送邀请** " 窗口中，输入可选消息，然后选择 " **发送**"。 

    电子邮件自动包含注册链接。 若要单独获取并保存注册链接，请选择 "**用户**" 窗格顶部 (**...** ") ，然后选择"**注册链接**"。 

    !["通过电子邮件发送注册链接" 窗口](./media/tutorial-setup-classroom-lab/send-email.png)

    "**用户**" 列表的 "**邀请**" 列显示每个添加的用户的邀请状态。 状态应更改为 "**发送**"，然后更改为 **" \<date> 发送**到"。 

### <a name="invite-selected-users"></a>邀请所选用户

此方法说明了如何仅邀请特定学生，并获取可与其他人共享的注册链接。

1. 在 " **用户** " 窗格中，选择列表中的一个或多个学生。 

1. 在所选学生的行中，选择 **信封** 图标，或在工具栏上选择 " **邀请**"。 

    ![邀请所选用户](./media/how-to-configure-student-usage/invite-selected-users.png)

1. 在 " **通过电子邮件发送邀请** " 窗口中，输入可选 **消息**，然后选择 " **发送**"。 

    ![向选定用户发送电子邮件](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    " **用户** " 窗格在表的 " **邀请** " 列中显示此操作的状态。 邀请电子邮件包含学生可用于向实验室注册的注册链接。

## <a name="get-the-registration-link"></a>获取注册链接

在本部分中，可以从门户获取注册链接，并使用自己的电子邮件应用程序发送该链接。 

1. 在 " **用户** " 窗格中，选择 " **注册链接**"。

    ![学生注册链接](./media/how-to-configure-student-usage/registration-link-button.png)

1. 在 " **用户注册** " 窗口中，选择 " **复制**"，然后选择 " **完成**"。 

    !["用户注册" 窗口](./media/how-to-configure-student-usage/registration-link.png)

    将链接复制到剪贴板。 
    
1. 在电子邮件应用程序中，粘贴 "注册" 链接，然后将电子邮件发送给学生，以便学生可以注册班级。 

## <a name="view-registered-users"></a>查看已注册用户

1. 请参阅 [Azure 实验室服务](https://labs.azure.com) 网站。 
1. 选择 " **登录**"，然后输入你的凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。
1. 在 " **我的实验** " 页上，选择要跟踪其使用情况的实验室。 
1. 在左窗格中，选择 " **用户**"，或选择 " **用户** " 磁贴。 

    " **用户** " 窗格显示已向实验室注册的学生列表。  

    ![已注册用户的列表](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>为用户设置配额

通过执行以下操作，可以为每个学生设置小时配额： 

1. 在 " **用户** " 窗格中，选择工具栏上的 " **每个用户的配额： \<number> 小时 (s) ** 。 
1. 在 " **每用户配额** " 窗口中，指定要在计划的类时间之外为每个学生分配的小时数，然后选择 " **保存**"。

    !["每个用户的配额" 窗口](./media/how-to-configure-student-usage/quota-per-user.png)    

    更改后的值现在显示在工具栏上和 "用户" 列表中的 "**每个用户的 \<number of hours> 配额：** " 按钮上，如下所示：

    ![每个用户的配额小时](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Vm 的计划运行时间](how-to-create-schedules.md)不会计为分配给学生的配额。 配额是指学生在 Vm 上花费的时间以外的时间。 

## <a name="set-additional-quotas-for-specific-users"></a>为特定用户设置其他配额

对于之前部分中的所有用户设置的常见配额，你可以为某些学生指定配额。 例如，如果你作为指导员，将所有学生的配额设置为10小时，并为特定学生设置5小时的额外配额，则该学生获取 15 (10 + 5) 小时的配额。 如果稍后更改常见配额，例如15，则学生获取 20 (15 + 5) 小时的配额。 请记住，此整体配额超出计划时间。 学生在计划时间内使用实验室 VM 的时间不计为此配额。 

若要设置其他配额，请执行以下操作：

1. 在 " **用户** " 窗格中，从列表中选择一个学生，然后在工具栏上选择 " **调整配额** "。 

    !["调整配额" 按钮](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. 在 "**调整配额 \<selected user or users email address> **" 中，输入要向所选学生或学生授予的其他实验室小时数，然后选择 "**应用**"。 

    !["调整配额 ..."窗口](./media/how-to-configure-student-usage/additional-quota.png)

    " **使用情况** " 列显示所选学生的更新配额。 

    ![用户的新使用情况](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>学生帐户

若要将学生添加到课堂实验室，请使用其电子邮件帐户。 学生可以使用以下类型的电子邮件帐户：

- 由大学 Azure Active Directory 实例提供的学生电子邮件帐户。
- Microsoft 域电子邮件帐户，如 *outlook.com*、 *hotmail.com*、 *msn.com*或 *live.com*。
- 非 Microsoft 电子邮件帐户，如 Yahoo！提供的帐户 或 Google。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 此帐户必须与 Microsoft 帐户链接。

### <a name="use-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
学生可以使用非 Microsoft 电子邮件帐户来注册和登录到教室实验室。  但注册要求它们首先创建一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多学生可能已经有一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果学生已经将其电子邮件地址与其他 Microsoft 产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则学生已经有了一个 Microsoft 帐户。  

当学生使用注册链接登录到课堂时，系统将提示他们输入其电子邮件地址和密码。 尝试使用未链接到 Microsoft 帐户的非 Microsoft 帐户登录的学生将收到以下错误消息： 

![登录时出现错误消息](./media/how-to-configure-student-usage/cant-find-account.png)

下面是学生 [注册 Microsoft 帐户](http://signup.live.com)的链接。  

> [!IMPORTANT]
> 学生登录到教室实验室时，不会提供创建 Microsoft 帐户的选项。 出于此原因，我们建议你 http://signup.live.com 在向使用非 Microsoft 帐户的学生发送的课堂实验室注册电子邮件中包含此注册链接。

### <a name="use-a-github-account"></a>使用 GitHub 帐户
学生还可以使用现有的 GitHub 帐户注册和登录到教室实验室。 如果他们已有一个链接到其 GitHub 帐户的 Microsoft 帐户，则学生可以登录并提供其密码，如前一部分中所示。 

如果尚未将其 GitHub 帐户链接到 Microsoft 帐户，则可以执行以下操作：

1. 选择 " **登录选项** " 链接，如下所示：

    !["登录选项" 链接](./media/how-to-configure-student-usage/signin-options.png)

1. 在 " **登录选项** " 窗口中，选择 " **用 GitHub 登录**"。

    !["用 GitHub 登录" 链接](./media/how-to-configure-student-usage/signin-github.png)

    在出现提示时，学生会创建一个链接到其 GitHub 帐户的 Microsoft 帐户。 如果选择 " **下一步**"，则会自动执行链接。 然后，它们会立即登录并连接到教室实验室。

## <a name="export-a-list-of-users-to-a-csv-file"></a>将用户列表导出到 CSV 文件

1. 中转到 " **用户** " 窗格。
1. 在工具栏上，选择 "省略号 (**...** ") ，然后选择 " **导出 CSV**"。 

    !["导出 CSV" 按钮](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 对于管理员： [创建和管理实验室帐户](how-to-manage-lab-accounts.md)
- 对于实验室所有者： [创建和管理实验室](how-to-manage-classroom-labs.md) 并 [设置和发布模板](how-to-create-manage-template.md)
- 对于实验室用户： [访问教室实验室](how-to-use-classroom-lab.md)
