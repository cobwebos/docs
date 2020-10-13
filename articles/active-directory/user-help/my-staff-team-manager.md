---
title: 通过“我的员工”管理密码和电话号码（预览） - Azure AD | Microsoft Docs
description: 通过“我的员工”管理用户的密码和电话号码
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: end-user-help
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 56ee7df52abf97c920dfe2fa4ba5d1d858975f7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537084"
---
# <a name="delegate-user-management-with-my-staff-preview"></a>通过“我的员工”委托用户管理（预览）

组织可以使用“我的员工”将用户管理任务委托给权威人士（例如商店经理或团队负责人），以帮助其员工访问所需的应用程序。 如果团队成员因忘记密码而无法访问应用程序，就会丧失生产力。 这也会增加支持成本，并导致管理过程出现瓶颈。  借助“我的员工”，无法访问其帐户的团队成员无需管理员帮助，只需点击几下鼠标即可重新获得访问权限。

## <a name="manage-your-staff-in-my-staff"></a>在“我的员工”中管理员工

在“我的员工”中管理团队成员非常简单。 首先，[转到“我的员工”](https://aka.ms/mystaff)，选择一个团队或位置，然后选择一个用户。 位置和位置中的团队成员由 IT 管理员决定，无法更改。

如果管理多个位置，则当访问“我的员工”时，必须选择一个位置才能查看分配到该位置的团队成员。

如果尚无足够的权限来访问“我的员工”，则会看到以下消息：“糟糕，你目前似乎无权查看我的员工。 有关详细信息，请与管理员联系”。

### <a name="find-a-staff-member-in-my-staff"></a>在“我的员工”中查找员工

必须先打开员工的配置文件，然后才能开始对其进行管理。

1. [打开“我的员工”](https://aka.ms/mystaff)，根据需要选择一个位置。

    ![在“我的员工”中为团队成员选择一个位置](media/my-staff-team-manager/allaus.png)

1. 打开团队成员的配置文件。

    ![在“我的员工”中选择某个位置中的一个用户](media/my-staff-team-manager/aupage.png)

## <a name="reset-a-user-password"></a>重置用户密码

如果已获得组织授权，则可以为员工重置密码。

1. [打开“我的员工”](https://aka.ms/mystaff)。
1. 打开员工的配置文件。
1. 选择“重置密码”。

    ![在“我的员工”中重置用户密码](media/my-staff-team-manager/resetpassword1.png)

1. 生成或输入新密码。 系统可能会显示自动生成的临时密码，或者要求你为用户输入临时密码。

    ![在“我的员工”中进行重置后复制临时用户密码](media/my-staff-team-manager/resetpassword2.png)

重置用户密码后，将该临时密码提供给用户。 当用户使用其临时密码登录时，要求他们更改该密码。

## <a name="manage-a-users-phone-number"></a>管理用户的电话号码

如果已获得组织授权，则可以管理员工的电话号码。

### <a name="add-a-phone-number"></a>添加电话号码

1. [打开“我的员工”](https://aka.ms/mystaff)。
1. 打开员工的配置文件。
1. 选择“添加电话号码”。

    ![在“我的员工”中添加用户电话号码](media/my-staff-team-manager/addphone1.png)

1. 添加电话号码，并选择“保存”。

    ![在“我的员工”中保存添加的用户电话号码](media/my-staff-team-manager/addphone2.png)

注册用户的电话号码后，用户可以使用它来登录短信、执行双重验证或自行重置密码（具体取决于组织的设置）。

![通过“我的员工”注册的新电话号码](media/my-staff-team-manager/addphone3.png)

### <a name="edit-a-phone-number"></a>编辑电话号码

1. [打开“我的员工”](https://aka.ms/mystaff)。
1. 打开员工的配置文件。
1. 选择“编辑电话号码”。

    ![在“我的员工”的用户配置文件中选择“编辑”](media/my-staff-team-manager/editphone2.png)

1. 输入新电话号码，并选择“保存”。

    ![在“我的员工”中编辑员工电话号码](media/my-staff-team-manager/editphone1.png)

### <a name="enable-phone-number-sign-in-for-a-user"></a>为用户启用电话号码登录

如果组织中启用了使用电话号码作为用户名登录（短信登录），则可以向现有用户电话号码添加此身份验证。

1. [打开“我的员工”](https://aka.ms/mystaff)。
1. 打开员工的配置文件。
1. 如果屏幕底部显示一条消息，指出使用电话号码作为用户名登录可用，请选择“启用”开始该过程。 如果用户已启用使用其电话号码登录，则会显示此消息。

    ![在“我的员工”中某个位置支持手机登录时显示该消息](media/my-staff-team-manager/enableforms1.png)

1. 完成后，请选择“确定”。

    ![显示 "为登录启用电话号码" 的屏幕截图 选中 "确定" 按钮的窗口。](media/my-staff-team-manager/enableforms2.png)

### <a name="remove-a-phone-number"></a>删除电话号码

1. [打开“我的员工”](https://aka.ms/mystaff)。
1. 打开员工的配置文件。
1. 选择“删除电话号码”。
1. 完成后，选择“删除”。

    ![在“我的员工”中删除员工电话号码](media/my-staff-team-manager/deletephone1.png)
