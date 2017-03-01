---
title: "将 Office 365 租户与 Azure 订阅配合使用 | Microsoft Docs"
description: "了解如何将 Office 365 目录（租户）添加到 Azure 订阅。"
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: a8e6423020fc0b669aad2bc48198c1df84db4dd2
ms.openlocfilehash: 18de4326edef50953c972e17cc9e75ea85a1fb82
ms.lasthandoff: 02/17/2017


---
# <a name="associate-an-office-365-tenant-to-an-azure-subscription"></a>将 Office 365 租户与 Azure 订阅关联
链接独立的 Azure 和 Office 365 订阅，以便可从 Azure 订阅访问 Office 365 租户。 若要链接订阅，请使用 Azure 服务管理员帐户登录 Azure，添加一个目录，然后将 Office 365 组织帐户添加到 Azure Active Directory 租户。

如果希望 Azure Active Directory 实例中有面向用户的 Office 365 订阅或已安装 Office 365 帐户但未安装 Azure 帐户，请参阅[使用 Office 365 帐户注册 Azure](billing-use-existing-office-365-account-azure-subscription.md)。 

## <a name="before-you-begin"></a>开始之前
* 必须具有 Azure 订阅服务管理员凭据。 共同管理员帐户无法执行本文中的某些步骤。 若要更改服务管理员，请参阅[如何添加或更改 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md#how-to-change-service-administrator-for-a-subscription)。
* 必须具有 Office 365 租户的全局管理员凭据。
* 服务管理员的电子邮件地址不能在 Office 365 租户中。
* 服务管理员的电子邮件地址不能与 Office 365 租户的任何全局管理员的电子邮件地址匹配。
* 如果使用的电子邮件地址既是 Microsoft 帐户又是组织帐户，则可暂时更改 Azure 订阅的服务管理员，以使用另一 Microsoft 帐户。 可在 [Microsoft 帐户注册页](https://signup.live.com/)上创建一个新的 Microsoft 帐户。

## <a name="link-office-365-tenant-to-azure-subscription"></a>将 Office 365 租户链接到 Azure 订阅
若要将 Office 365 租户与 Azure 订阅关联，请执行以下步骤：

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>步骤 1：将 Office 365 租户添加到 Azure 订阅

1. 使用服务管理员凭据登录到 [Azure 经典门户](https://manage.windowsazure.com/)。

    ![Azure 登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

2. 在左窗格中选择“ACTIVE DIRECTORY”。 你应该不会看到 Office 365 租户。 如果看到该租户，请参阅[步骤 2：更改与 Azure 订阅关联的目录](#Step2)。
   
   ![Active Directory 条目的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

3. 选择“新建” > **“目录”** > **“自定义创建”**。
   
    ![Azure Active Directory 自定义创建的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
4. 在“添加目录”页的“目录”下，选择“使用现有目录”。 然后选择“我已准备好立即注销”，再选择“完成”![完成图标](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
   
    ![“使用现有目录”的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
5. 注销后，使用 Office 365 租户的全局管理员凭据登录。
   
    ![Office 365 全局管理员登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
6. 选择“继续”。
   
    ![验证的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
7. 选择“立即注销”。
   
    ![注销的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
8. 使用服务管理员凭据登录到 [Azure 经典门户](https://manage.windowsazure.com/)。
   
    ![Azure 登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
9. 你应该会在仪表板中看到 Office 365 租户。
   
    ![仪表板的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="a-namestep2astep-2-change-the-directory-associated-with-the-azure-subscription"></a><a name="Step2"></a>步骤 2：更改与 Azure 订阅关联的目录
   
1. 选择“设置”。
   
    ![Azure 经典门户设置图标的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
2. 选择 Azure 订阅，然后选择“编辑目录”。

    ![Azure 订阅编辑目录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
3. 选择“下一步”![“下一步”图标](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)。
   
    ![“更改关联的目录”的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
4. 查看受影响的帐户。 将删除现有资源组中所有具有“已分配”访问权限的共同管理员和[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 用户。 收到的警告将仅提到删除共同管理员。
      
    ![显示要删除的共同管理员帐户的屏幕截图。](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![显示要删除的示例用户帐户的屏幕截图。](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
5. 选择“完成”![完成图标](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

### <a name="step-3-add-your-office-365-organizational-accounts-as-co-administrators-to-the-azure-active-directory-tenant"></a>步骤 3：向 Azure Active Directory 租户添加 Office 365 组织帐户作为共同管理员
   
1. 选择“管理员”选项卡，然后选择“添加”。
   
    ![Azure 经典门户设置管理员选项卡的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
2. 输入 Office 365 租户的组织帐户，选择 Azure 订阅，然后选择“完成”![完成图标](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
   
    ![Azure 添加共同管理员对话框的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
3. 返回到“管理员”选项卡。 此时会看到显示为共同管理员的组织帐户。
   
    ![管理员选项卡的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
4.  使用共同管理员帐户测试对 Azure 的访问权限。
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 注销 Azure 经典门户。
   
    b.保留“数据库类型”设置，即设置为“共享”。 打开 [Azure 门户](https://portal.azure.com/)。
   
    c. 输入共同管理员的凭据，然后选择“登录”。
   
    ![Azure 登录页的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。



