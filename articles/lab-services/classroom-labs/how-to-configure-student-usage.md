---
title: 在 Azure 实验室服务的教室实验室中配置使用设置
description: 了解如何配置实验室的学生人数、让他们在实验室注册、控制他们可以使用 VM 的小时数等。
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
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159449"
---
# <a name="add-and-manage-lab-users"></a>添加和管理实验室用户

本文介绍如何将学生用户添加到实验室、向实验室注册、控制他们可以使用虚拟机 （VM） 的额外小时数等。 

## <a name="add-users-to-a-lab"></a>将用户添加到实验室

在本节中，您可以手动将学生添加到实验室或通过上传 CSV 文件。 请执行以下操作：

1. 在左窗格中，选择“用户”****。 

    默认情况下，"**限制访问**"选项处于打开状态，除非他们在用户列表中，否则即使学生有注册链接，也不能在实验室注册。 只有列出的用户可以使用您发送的注册链接向实验室注册。 在此过程中，请将用户添加到列表。 或者，您可以关闭 **"限制访问**"，允许学生注册实验室，只要他们有注册链接。 

1. 在 **"用户"** 窗格的顶部，选择 **"添加用户**"，然后选择"**按电子邮件地址添加**"。 

    !["添加用户"按钮](../media/how-to-configure-student-usage/add-users-button.png)

1. 在"**添加用户"** 窗格中，在单独的行或由分号分隔的一行上输入学生的电子邮件地址。 

    ![添加用户的电子邮件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. 选择“保存”。**** 

    该列表显示当前用户的电子邮件地址和状态，无论他们是否在实验室注册。 

    ![用户列表](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 学生注册到实验室后，列表将显示他们的姓名。 列表中显示的名称是使用 Azure 活动目录中学生的名字和姓氏构造的。 

### <a name="add-users-by-uploading-a-csv-file"></a>通过上传 CSV 文件添加用户

您还可以通过上传包含其电子邮件地址的 CSV 文件来添加用户。

1. 在 Microsoft Excel 中，创建一个 CSV 文件，该文件将学生的电子邮件地址列在一列中。

    ![CSV 文件中的用户列表](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. 在 **"用户"** 窗格的顶部，选择 **"添加用户**"，然后选择"**上载 CSV**"。

    !["上传 CSV"按钮](../media/how-to-configure-student-usage/upload-csv-button.png)

1. 选择包含学生电子邮件地址的 CSV 文件，然后选择 **"打开**"。

    "**添加用户"** 窗口显示来自 CSV 文件的电子邮件地址列表。 

    ![包含 CSV 文件的电子邮件地址的"添加用户"窗口](../media/how-to-configure-student-usage/add-users-window.png)

1. 选择“保存”。**** 

1. 在"**用户"** 窗格中，查看添加的学生列表。 

    !["用户"窗格中添加的用户列表](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>向用户发送邀请

要向新用户发送注册链接，请使用以下方法之一。 

如果为实验室启用了 **"限制访问**"选项，则只有列出的用户可以使用注册链接注册到实验室。 默认情况下会启用此选项。 

### <a name="invite-all-users"></a>邀请所有用户

此方法演示如何向所有列出的学生发送包含注册链接和可选消息的电子邮件。

1. 在"**用户"** 窗格中，选择 **"邀请所有**"。 

    !["全部邀请"按钮](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在"**通过电子邮件发送邀请"** 窗口中，输入可选邮件，然后选择"**发送**"。 

    电子邮件自动包含注册链接。 要单独获取和保存注册链接，请选择 **"用户"** 窗格顶部的省略号 **（.），** 然后选择 **"注册链接**"。 

    !["通过电子邮件发送注册链接"窗口](../media/tutorial-setup-classroom-lab/send-email.png)

    **"用户"** 列表**的"邀请"** 列显示每个添加用户的邀请状态。 状态应更改为 **"发送"，** 然后更改为 **"发送\<日期>。 ** 

### <a name="invite-selected-users"></a>邀请所选用户

此方法演示如何仅邀请某些学生并获得注册链接，您可以与其他人共享。

1. 在"**用户"** 窗格中，在列表中选择一个学生或多个学生。 

1. 在所选学生的行中，选择**信封**图标，或在工具栏上选择 **"邀请**"。 

    ![邀请所选用户](../media/how-to-configure-student-usage/invite-selected-users.png)

1. 在"**通过电子邮件发送邀请"** 窗口中，输入可选**邮件**，然后选择"**发送**"。 

    ![向所选用户发送电子邮件](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    "**用户"** 窗格在表的"**邀请"** 列中显示此操作的状态。 邀请电子邮件包括学生可用于在实验室注册的注册链接。

## <a name="get-the-registration-link"></a>获取注册链接

在本节中，您可以从门户获取注册链接，并使用您自己的电子邮件应用程序发送该链接。 

1. 在 **"用户"** 窗格中，选择 **"注册"链接**。

    ![学生注册链接](../media/how-to-configure-student-usage/registration-link-button.png)

1. 在 **"用户注册**"窗口中，选择 **"复制**"，然后选择 **"完成**"。 

    !["用户注册"窗口](../media/how-to-configure-student-usage/registration-link.png)

    将链接复制到剪贴板。 
    
1. 在电子邮件应用程序中，粘贴注册链接，然后将电子邮件发送给学生，以便学生可以注册课程。 

## <a name="view-registered-users"></a>查看已注册用户

1. 转到[Azure 实验室服务](https://labs.azure.com)网站。 
1. 选择 **"登录**"，然后输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。
1. 在 **"我的实验室"** 页面上，选择要跟踪其使用情况的实验室。 
1. 在左侧窗格中，选择 **"用户**"或"**选择"用户**"磁贴。 

    "**用户"** 窗格显示在实验室注册的学生列表。  

    ![注册用户列表](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>为用户设置配额

您可以通过执行以下操作为每个学生设置一小时配额： 

1. 在 **"用户"** 窗格中，选择**工具栏上的\<"每个用户配额：>小时数**"。 
1. 在 **"每个用户配额"** 窗口中，指定要在计划上课时间之外给每个学生的小时数，然后选择 **"保存**"。

    !["每个用户的配额"窗口](../media/how-to-configure-student-usage/quota-per-user.png)    

    更改的值现在显示在 **"每个用户的\<配额：工具栏>** 按钮和用户列表中的小时数，如下所示：

    ![每个用户的配额小时数](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM 的计划运行时间](how-to-create-schedules.md)不计入分配给学生的配额。 配额是学生花费在 VM 上的计划时间以外的时间。 

## <a name="set-additional-quotas-for-specific-users"></a>为特定用户设置其他配额

您可以为某些学生指定配额，超出上一节中为所有用户设置的通用配额。 例如，如果您作为教师，将所有学生的配额设置为 10 小时，并为特定学生设置 5 小时的额外配额，则该学生将获得 15 （10 + 5） 小时的配额。 如果您稍后将公共配额更改为 15，则学生将获得 20 小时（15 + 5） 小时的配额。 请记住，此总配额超出计划时间。 学生在计划时间内在实验室 VM 上花费的时间不计入此配额。 

要设置其他配额，可以执行以下操作：

1. 在"**用户"** 窗格中，从列表中选择一个学生，然后在工具栏上选择 **"调整配额**"。 

    !["调整配额"按钮](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. 在**\<"调整所选用户或用户电子邮件地址>的"配额"中**，输入要授予所选学生或学生的额外实验室小时数，然后选择"**应用**"。 

    !["调整配额..."窗口](../media/how-to-configure-student-usage/additional-quota.png)

    "**使用情况"** 列显示所选学生的更新配额。 

    ![用户的新用法](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>学生帐户

要将学生添加到教室实验室，请使用他们的电子邮件帐户。 学生可能具有以下类型的电子邮件帐户：

- 由大学为 Office 365 提供的 Azure 活动目录实例提供的学生电子邮件帐户。 
- 微软域电子邮件帐户，如*outlook.com*outlook.com、hotmail.com、msn.com*hotmail.com*或*live.com*。 *msn.com*
- 非微软电子邮件帐户，如雅虎提供的电子邮件帐户 或谷歌。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 此帐户必须与 Microsoft 帐户链接。

### <a name="use-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
学生可以使用非 Microsoft 电子邮件帐户注册并登录到教室实验室。  但是，注册要求他们首先创建链接到其非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多学生可能已经拥有链接到其非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果学生将电子邮件地址与其他 Microsoft 产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则他们已拥有 Microsoft 帐户。  

当学生使用注册链接登录到教室时，系统会提示他们输入电子邮件地址和密码。 尝试使用未链接到 Microsoft 帐户的非 Microsoft 帐户登录的学生将收到以下错误消息： 

![登录时出现错误消息](../media/how-to-configure-student-usage/cant-find-account.png)

下面是一个链接，供学生[注册一个微软帐户](http://signup.live.com)。  

> [!IMPORTANT]
> 当学生登录到教室实验室时，他们无法选择创建 Microsoft 帐户。 因此，我们建议您在教室实验室注册电子邮件中包括此注册链接http://signup.live.com，以及您发送给使用非 Microsoft 帐户的学生的教室实验室注册电子邮件中。

### <a name="use-a-github-account"></a>使用 GitHub 帐户
学生还可以使用现有的 GitHub 帐户注册并登录到教室实验室。 如果他们已经有一个 Microsoft 帐户链接到其 GitHub 帐户，学生可以登录并提供其密码，如上一节所示。 

如果他们尚未将其 GitHub 帐户链接到 Microsoft 帐户，则可以执行以下操作：

1. 选择**登录选项**链接，如下所示：

    !["登录选项"链接](../media/how-to-configure-student-usage/signin-options.png)

1. 在 **"登录"选项**窗口中，选择**使用 GitHub 登录**。

    !["使用 GitHub 登录"链接](../media/how-to-configure-student-usage/signin-github.png)

    提示后，学生将创建链接到其 GitHub 帐户的 Microsoft 帐户。 当他们选择 **"下一步**"时，链接会自动发生。 然后，他们立即登录并连接到教室实验室。

## <a name="export-a-list-of-users-to-a-csv-file"></a>将用户列表导出到 CSV 文件

1. 转到 **"用户"** 窗格。
1. 在工具栏上，选择省略号 **（...），** 然后选择 **"导出 CSV**"。 

    !["导出 CSV"按钮](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- 对于管理员：[创建和管理实验室帐户](how-to-manage-lab-accounts.md)
- 对于实验室所有者：[创建和管理实验室](how-to-manage-classroom-labs.md)以及[设置和发布模板](how-to-create-manage-template.md)
- 对于实验室用户：[访问教室实验室](how-to-use-classroom-lab.md)
