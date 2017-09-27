---
title: "将 Office 365 租户与 Azure 订阅配合使用 | Microsoft Docs"
description: "了解如何将 Office 365 目录（租户）添加到 Azure 订阅。"
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: cjiang
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: b7b3e4539110f68e1e6ef4a75110924a27b40c49
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="link-an-office-365-tenant-to-an-azure-subscription"></a>将 Office 365 租户链接到 Azure 订阅
链接独立的 Azure 和 Office 365 订阅，以便可从 Azure 订阅访问 Office 365 租户。 若要链接订阅，请使用 Azure 服务管理员帐户登录 Azure，添加一个目录，然后将 Office 365 工作或学校帐户添加到 Azure Active Directory 租户。

**想要将现有 Azure 订阅移到 Office 365 工作或学校帐户？** 如果已使用个人 Microsoft 帐户注册了 Azure，并且想要通过 Office 365 帐户使用它或登录，我们强烈建议转让订阅。 请参阅[将 Azure 订阅所有权转让给其他帐户](billing-subscription-transfer.md)。 

**想要使用 Office 365 注册 Azure？** 请参阅[使用 Office 365 帐户注册 Azure](billing-use-existing-office-365-account-azure-subscription.md)。 

## <a name="before-you-begin"></a>开始之前
* 必须具有 Azure 订阅服务管理员凭据。 共同管理员帐户无法执行本文中的某些步骤。 若要更改服务管理员，请参阅[如何添加或更改 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription)。
* 必须具有 Office 365 租户的全局管理员凭据。
* 服务管理员的电子邮件地址不能在 Office 365 租户中。
* 服务管理员的电子邮件地址不能与 Office 365 租户的任何全局管理员的电子邮件地址匹配。
* 如果使用的电子邮件地址既是 Microsoft 帐户又是组织帐户，则可暂时更改 Azure 订阅的服务管理员，以使用另一 Microsoft 帐户。 可在 [Microsoft 帐户注册页](https://signup.live.com/)上创建一个新的 Microsoft 帐户。

## <a name="link-office-365-tenant-to-azure-subscription"></a>将 Office 365 租户链接到 Azure 订阅
要将 Office 365 租户与 Azure 订阅关联，请执行以下步骤：

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>步骤 1：将 Office 365 租户添加到 Azure 订阅

1. 使用服务管理员凭据登录到 [Azure 经典门户](https://manage.windowsazure.com/)。

    ![Azure 登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
2. 在左窗格中选择“ACTIVE DIRECTORY”。 应该不会看到 Office 365 租户。 如果看到该租户，请参阅[步骤 2：更改与 Azure 订阅关联的目录](#Step2)。
   
   ![Active Directory 条目的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
3. 选择“新建” > **“目录”** > **“自定义创建”**。
   
    ![Azure Active Directory 自定义创建的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
4. 在“添加目录”页的“目录”下，选择“使用现有目录”。 然后选择“我已准备好立即注销”，再选择“完成”![完成图标 ](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
   
    ![“使用现有目录”的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
5. 注销后，使用 Office 365 租户的全局管理员凭据登录。
   
    ![Office 365 全局管理员登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
6. 选择“继续”。
   
    ![验证的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
7. 选择“立即注销”。
   
    ![注销的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
8. 使用服务管理员凭据登录到 [Azure 经典门户](https://manage.windowsazure.com/)。
   
    ![Azure 登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
9. 应该会在仪表板中看到 Office 365 租户。
   
    ![仪表板的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>步骤 2：更改与 Azure 订阅关联的目录
   
1. 选择“设置”。
   
    ![Azure 经典门户设置图标的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
2. 选择 Azure 订阅，并选择“编辑目录”。

    ![Azure 订阅编辑目录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
3. 选择“下一步”![“下一步”图标](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)。
   
    ![“更改关联的目录”的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
4. 查看受影响的帐户。 将删除现有资源组中所有具有“已分配”访问权限的共同管理员和[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 用户。 收到的警告将仅提到删除共同管理员。
      
    ![显示要删除的共同管理员帐户的屏幕截图。](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![显示要删除的示例用户帐户的屏幕截图。](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
5. 选择“完成”![完成图标](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

### <a name="step-3-add-your-office-365-organizational-accounts-as-admins-to-the-azure-subscription"></a>步骤 3：将 Office 365 组织帐户添加为 Azure 订阅的管理员帐户
   
要向 Azure 订阅添加管理员，请参阅[添加或更改管理订阅或服务的 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。


