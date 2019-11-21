---
title: Update profile & account info from the My Apps portal - Azure AD
description: Learn how to update your profile and your work or school account information, including changing your password, turning on password reset, updating your security verification methods, viewing your organization's terms of use statement, and signing out of everywhere you've signed in using your work or school account.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ecdffae61fbadfd5d975af942861e24589c6fbf
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233171"
---
# <a name="update-your-profile-and-work-or-school-account-information-on-the-my-apps-portal"></a>Update your profile and work or school account information on the My Apps portal

可以在基于 Web 的“我的应用”门户中使用工作或学校帐户，以便查看并启动组织的许多基于云的应用、更新你的部分配置文件和帐户信息、查看**组**信息，以及为应用和组执行“访问评审”。 如果无法访问“我的应用”门户，则必须联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="view-your-organization-related-profile-information"></a>View your organization-related profile information

Depending on what your organization has chosen to include on your **Profile** page of the **My Apps** portal, you might see your specific work-related details, along with your devices and activity, and any additional organizations to which you belong.

### <a name="to-view-your-profile-information"></a>To view your profile information

1. Sign in to your work or school account, [go to the **My Apps** portal](my-apps-portal-end-user-access.md).

2. From the **Apps** page, select your profile picture from the upper-right corner, where it says your name and your organization, and then select **Profile**.

    The **Profile** page appears, showing your profile information.

    ![Profile page, showing all of the available information](media/my-apps-portal/my-apps-portal-profile-page.png)

3. From the **Profile** page, you can:

    - **Review your organization-related details.** View your photo, name, title, associated email addresses, and office information. This information is managed by your organization and you won't be able to change it. If you see an error, contact your Helpdesk.

    - **Review your Devices & activity**. Make sure each device is familiar and properly connected to your organization. If you don’t recognize a device, select **Disable device** to remove the association with your account. After you disable a device, it’s removed from this page.

    - **Review your Organizations.** Make sure that you still work with each of the specified organizations. If you no longer work with an organization, we highly recommend that you select **Sign in to leave organization**. After you leave the organization, it’s removed from this page.

## <a name="manage-your-work-or-school-account-information"></a>Manage your work or school account information

Update and manage your work or school account information from the **Profile** page of the **My Apps** portal. This page helps you to:

- Change your work or school account password.

- Turn on password reset (if your administrator has turned on this feature).

- Provide additional security verification information.

- Review your organization’s terms of use.

- Sign out of everywhere.

## <a name="change-your-password"></a>更改密码

If you want to change the password for your work or school account, you can select **Change password** from the **Manage account** area of the **Profile** page.

### <a name="to-change-your-password"></a>To change your password

1. From the **Manage account** area of the **Profile** page, select **Change password**.

2. On the **Change password** page, make sure your User ID is correct, and then type your old password and your new password into the boxes.

    ![Change password page](media/my-apps-portal/my-apps-portal-change-password-page.png)

3. 选择“提交”。

    Your password is changed. You’ll be required to sign-in to any apps you’ve previously signed in to using your work or school account.

## <a name="set-up-and-use-password-reset"></a>Set up and use password reset

If you forgot your password, never got one from your company support, or have been locked out of your account, you can reset your own password.

>[!Important]
>Your administrator must turn on this feature and you must register to participate. For details about how to register and how to reset your password, see [Register for self-service password reset](active-directory-passwords-reset-register.md) and [Reset your work or school password](active-directory-passwords-update-your-own-password.md).

## <a name="change-your-security-verification-information"></a>Change your security verification information

If your organization requires you to use two-factor verification, you can add, update, and delete your associated security information from the **Additional security verification** page.

Two-factor verification requires you to use two pieces of verification info, like a password and a PIN, before you can get to your account or to your organization’s information. For more information about two-factor verification, see [Set up my account for two-step verification](multi-factor-authentication-end-user-first-time.md).

### <a name="to-change-your-security-information"></a>To change your security information

1. From the **Manage account** area of the **Profile** page, select **Additional security verification**.

    ![Additional security verification page](media/my-apps-portal/my-apps-portal-additional-verification-page.png)

2. On the **Additional security verification** page, you can add, change, or delete the following information:

    - **Default verification option.** Select the default secondary method to use for two-factor verification. This method is automatically used any time two-factor verification is required after you enter your username and password.

    - **Add, update, or remove verification methods.** You can add new, update existing, or delete old information that no longer applies.

    - **Set up the Microsoft Authenticator app.** You can choose to set up the Microsoft Authenticator app to work as your verification method. For more information about the Microsoft Authenticator app, see [What is the Microsoft Authenticator app?](user-help-auth-app-overview.md)

3. 选择“保存”以保存更改。

## <a name="review-your-organizations-terms-of-use-statement"></a>Review your organization’s terms of use statement

You can choose to review your organization’s terms of use statement, if it’s available.

1. From the **Manage account** area of the **Profile** page, select **Review terms of use**.

2. Review your organization's terms of use and select **Accept** to confirm that you've read and understood the terms of use for your organization.

    ![Terms of use page from the My Apps portal](media/my-apps-portal/my-apps-portal-tou-page.png)

    If your organization doesn't have a terms of use, you can select **Done** to return to the **Profile** page.

## <a name="sign-out-of-everywhere"></a>Sign out of everywhere

You can choose to sign out of everywhere you’re currently signed in to using your work or school account. This includes all apps and devices.

### <a name="to-sign-out-of-everywhere"></a>To sign out of everywhere

1. From the **Manage account** area of the **Profile** page, select **Sign out everywhere**.

2. In the **Sign out everywhere** confirmation box, select **Yes** to confirm that you want to sign out of all your sessions and devices. Select **No** if you change your mind.

## <a name="next-steps"></a>后续步骤

After you finish your updates on the **Profile** page, you can:

- [Access and use apps on the My Apps portal](my-apps-portal-end-user-access.md).

- [View and update your groups-related information](my-apps-portal-end-user-groups.md).

- [Perform your own access reviews](my-apps-portal-end-user-access-reviews.md).
