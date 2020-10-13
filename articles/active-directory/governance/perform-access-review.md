---
title: 在访问评审中评审对组和应用程序的访问权限 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中评审组成员的访问权限或应用程序访问权限。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e03ba5e7a4a24ee3114946230f78a64ea3d42cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265675"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中评审对组和应用程序的访问权限

Azure Active Directory (Azure AD) 借助称为“Azure AD 访问评审”的功能，简化了企业对 Azure AD 及其他 Microsoft Online Services 中的组和应用程序访问权限的管理方式。 本文介绍指定的审阅者如何对有权访问应用程序的组成员或用户执行访问评审。 如果想要评审对包的访问权限，请阅读[在 Azure AD 权利管理中评审访问包的访问权限](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>使用“我的应用”执行访问评审

可以从通知电子邮件开始访问评审过程，也可以直接转到站点来开始。

- **电子邮件**：

>[!IMPORTANT]
> 接收电子邮件可能存在延迟，在某些情况下，可能需要长达 24 小时来接收。 将 azure-noreply@microsoft.com 添加到安全收件人列表以确保收到所有电子邮件。

1. 查找要求你执行访问评审的 Microsoft 电子邮件。 以下示例电子邮件要求评审对某个组的访问权限。

    ![显示 Microsoft 的示例电子邮件以查看对组的访问权限的屏幕截图。](./media/perform-access-review/access-review-email.png)

1. 单击“开始评审”链接打开访问评审。

- 如果未收到该电子邮件，可按照以下步骤找到待处理的访问评审。

    1. 登录到“我的应用”门户 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。

        ![“我的应用”门户，其中列出了你有权访问的应用](./media/perform-access-review/myapps-access-panel.png)

    1. 在页面右上角，单击你的名称和默认组织旁边的用户。 如果列出多个组织，请选择已请求访问评审的组织。

    1. 单击“访问评审”磁贴，查看待处理的访问评审列表。

        > [!NOTE]
        > 如果“访问评审”磁贴不可见，则表明该组织没有要执行的访问评审，此时不需要执行任何操作。

        ![显示应用和组的挂起访问评审列表的屏幕截图。](./media/perform-access-review/access-reviews-list.png)

    1. 单击你要执行的访问评审对应的“开始评审”链接。

打开访问评审后，你会看到需要访问评审的用户的名称。

如果请求是评审你自己的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审自己对组或应用程序的访问权限](review-your-access.md)。

![打开访问评审，其中会列出要评审的用户](./media/perform-access-review/perform-access-review.png)

可通过两种方式批准或拒绝访问权限：

- 可以针对每个用户请求选择适当的操作，通过这种方式“手动”批准或拒绝一个或多个用户的访问。
- 可以接受系统建议。

### <a name="approve-or-deny-access-for-one-or-more-users"></a>批准或拒绝一个或多个用户的访问权限

1. 评审用户列表并确定是批准还是拒绝其继续访问。

    - 若要批准或拒绝单个用户的访问权限，请单击相应的行打开一个窗口，以指定要执行的操作。 
    - 若要批准或拒绝多个用户的访问权限，请勾选这些用户，然后单击“评审 X 个用户”按钮打开一个窗口，以指定要执行的操作。

1. 单击“批准”或“拒绝”。  

    ![包括“批准”、“拒绝”和“不知道”选项的操作窗口](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > 如果不确定，可以单击“不知道”。 用户可以保留其访问权限，而你的选择将记录在审核日志中。

1. 访问评审的管理员可能会要求你在“原因”框中提供做出决定的理由。 即使不需要提供理由， 你也可以为决策提供理由，而你提供的信息将可供其他审阅者使用。

1. 指定要执行的操作后，单击“保存”。

    >[!NOTE]
    > 在访问评审结束之前，随时可以更改响应。 若要更改响应，请选择相应的行并更新响应。 例如，可以批准以前已拒绝的用户，或者拒绝以前已批准的用户。

    >[!IMPORTANT]
    > - 如果拒绝了某个用户的访问权限，不会立即删除该用户。 如果已启用[自动应用](complete-access-review.md#apply-the-changes)，则在评审期结束或管理员停止评审时，这些用户将被删除。
    > - 如果有多个评审者，将记录最后提交的响应。 假设有一个管理员指定两个审阅者（Alice 和 Bob）的示例。 Alice 首先打开访问评审并批准了用户的访问请求。 在评审期结束之前，Bob 打开访问评审，拒绝了 Alice 之前批准的同一请求中的访问权限。 最后决定（即拒绝访问）是系统记录的响应。

### <a name="approve-or-deny-access-based-on-recommendations"></a>根据建议批准或拒绝访问权限

为了让你更轻松、更快捷地评审访问权限，我们还会提供建议，单击一下鼠标就能接受这些建议。 建议是根据用户的登录活动生成的。

1. 在页面底部的蓝色栏中，单击“接受建议”。

    ![显示 "打开访问评审" 列表并选择 "接受建议" 按钮的屏幕截图。](./media/perform-access-review/accept-recommendations.png)

    你将看到建议操作的摘要。

    ![显示建议操作摘要的窗口](./media/perform-access-review/accept-recommendations-summary.png)

1. 单击“确定”接受建议。

## <a name="perform-access-review-using-my-access-new"></a>使用 Access (新建) 执行访问评审

可以通过以下几种不同的方式，通过 "我的 Access" 中更新的用户界面获得新的审阅者体验：

### <a name="my-apps-portal"></a>我的应用门户

1. 登录到 "我的应用" [https://myapps.microsoft.com](https://myapps.microsoft.com) 。

    ![“我的应用”门户，其中列出了你有权访问的应用](./media/perform-access-review/myapps-access-panel.png)

2. 单击“访问评审”磁贴，查看待处理的访问评审列表。

    > [!NOTE]
    > 如果“访问评审”磁贴不可见，则表明该组织没有要执行的访问评审，此时不需要执行任何操作。

![在预览期间显示有新的可用体验横幅的应用和组的挂起访问审阅列表](./media/perform-access-review/banner.png)

3. 单击 " **试用"！** 在页面顶部的横幅中。 这会将你转到新的 "我的访问" 体验。
  
### <a name="email"></a>电子邮件

  >[!IMPORTANT]
> 接收电子邮件可能存在延迟，在某些情况下，可能需要长达 24 小时来接收。 将 azure-noreply@microsoft.com 添加到安全收件人列表以确保收到所有电子邮件。

   1. 查找要求你执行访问评审的 Microsoft 电子邮件。 你可以在下面看到一个示例电子邮件：

   ![要求评审对某个组的访问权限的 Microsoft 示例电子邮件](./media/perform-access-review/access-review-email-preview.png)

   2. 单击“开始评审”链接打开访问评审。

>[!NOTE]
>如果单击 "开始审阅"，则会将你转到 **"我的应用** "，然后按照上面标题为 **"我的应用门户**" 部分列出

### <a name="navigate-to-my-access-directly"></a>直接导航到 "我的访问"

还可以通过使用浏览器打开 "我的访问" 来查看待定的访问评审。

1. 在上登录到 "我的访问" https://myaccess.microsoft.com/

2. 从左侧栏上的菜单中选择 " **访问评审** "，查看分配给你的待定访问评审的列表。

   ![菜单中的访问评审](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>批准或拒绝一个或多个用户的访问权限

在 "组和应用" 下打开 "访问" 后，可以看到：

- **名称** 访问评审的名称。
- **截止日期** 评审的截止日期。 在此日期后，可以从正在查看的组或应用中删除用户。
- **资源** 要查看的资源的名称。
- **进度** 在此访问评审的总用户数量中审阅的用户数。

单击 "访问评审" 的名称即可开始使用。

![应用和组的待处理访问评审列表](./media/perform-access-review/access-reviews-list-preview.png)

一旦打开，你将在访问评审范围内看到用户列表。 如果请求是评审你自己的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审自己对组或应用程序的访问权限](review-your-access.md)。

可通过两种方式批准或拒绝访问权限：

- 可以手动批准或拒绝一个或多个用户的访问权限。
- 可以接受系统建议。

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>手动批准或拒绝一个或多个用户的访问权限

1. 评审用户列表并确定是批准还是拒绝其继续访问。
2. 通过单击其名称旁边的圆圈选择一个或多个用户。
3. 在上面的栏中选择 " **批准** " 或 " **拒绝** "。
    - 如果不确定，可以单击“不知道”。 用户需要保持其访问权限，并且你的选择将记录在审核日志中。 请务必记住，你提供的任何信息都将提供给其他审阅者。 他们可以阅读你的注释，并在评审请求时将其考虑在内。

    ![打开访问评审列出了需要查看的用户](./media/perform-access-review/user-list-preview.png)

4. 访问评审的管理员可能会要求你在“原因”框中提供做出决定的理由。 即使不需要提供理由， 你仍可以为你的决策提供原因，而你包含的信息将可供其他审批者查看。

5. 单击“提交” 。
    - 在访问评审结束之前，随时可以更改响应。 若要更改响应，请选择相应的行并更新响应。 例如，可以批准以前已拒绝的用户，或者拒绝以前已批准的用户。

 >[!IMPORTANT]
 > - 如果拒绝了某个用户的访问权限，不会立即删除该用户。 当审核时间结束或管理员停止评审时，它们会被删除。 
 > - 如果有多个评审者，将记录最后提交的响应。 假设有一个管理员指定两个审阅者（Alice 和 Bob）的示例。 Alice 首先打开访问评审并批准了用户的访问请求。 在评审期结束之前，Bob 打开访问评审，拒绝了 Alice 之前批准的同一请求中的访问权限。 最后决定（即拒绝访问）是系统记录的响应。

#### <a name="approve-or-deny-access-based-on-recommendations"></a>根据建议批准或拒绝访问权限

为了让你更轻松、更快捷地评审访问权限，我们还会提供建议，单击一下鼠标就能接受这些建议。 建议是根据用户的登录活动生成的。

1. 选择一个或多个用户，然后单击 " **接受建议**"。

    ![打开访问评审列表，其中会显示“接受建议”按钮](./media/perform-access-review/accept-recommendations-preview.png)

1. 单击“提交”接受建议。

若要接受所有用户的建议，请确保未选择任何人，并单击顶部栏上的 " **接受建议** " 按钮。

>[!NOTE]
>接受建议后，将不会更改以前的决策。

## <a name="next-steps"></a>后续步骤

- [完成组或应用程序的访问评审](complete-access-review.md)
