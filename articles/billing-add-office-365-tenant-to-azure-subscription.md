---
title: "将 Office 365 租户与 Azure 订阅配合使用 | Microsoft Docs"
description: "了解如何将 Office 365 目录（租户）添加到 Azure 订阅来建立关联。"
services: 
documentationcenter: 
author: JiangChen79
manager: mbaldwin
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 36cd9eac7be2d99971d8d2e227cd4b761df82d08


---
# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>将 Office 365 租户与 Azure 订阅关联
如果之前已分别获得了 Azure 和 Office 365 订阅，现在想要能够从 Azure 订阅访问 Office 365 租户，则可轻松实现这一点。 本文介绍相关实现方法。

> [!NOTE]
> 本文不适用于企业协议 (EA) 客户。
> 
> 

## <a name="quick-guidance"></a>快速指南
若要将 Office 365 租户与 Azure 订阅关联，请使用 Azure 帐户添加 Office 365 租户，然后将 Azure 订阅与 Office 365 租户关联。

## <a name="detailed-steps"></a>详细步骤
在此方案中，Kelley Wall 是一位在帐户 kelley.wall@outlook.com. 下拥有 Azure 订阅的用户。Kelley 在帐户 kelley.wall@contoso.onmicrosoft.com. 下也有 Office 365 订阅。现在，Kelley 想通过 Azure 订阅访问 Office 365 租户。

![Azure Active Directory 状态的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Office 365 管理中心活动用户的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>先决条件
若要使关联生效，必须满足以下先决条件：

* 需要 Azure 订阅的服务管理员凭据。 共同管理员无法执行一部分步骤。
* 需要 Office 365 租户的全局管理员凭据。
* 服务管理员的电子邮件地址不能包含在 Office 365 租户中。
* 服务管理员的电子邮件地址不能与 Office 365 租户的任何全局管理员的电子邮件地址匹配。
* 如果当前使用的电子邮件地址既是 Microsoft 帐户又是组织帐户，则可暂时更改 Azure 订阅的服务管理员，以使用另一 Microsoft 帐户。 可以在 [Microsoft 帐户注册页](https://signup.live.com/)上创建一个新的 Microsoft 帐户。

若要更改服务管理员，请遵循以下步骤：

1. 登录到[帐户管理门户](https://account.windowsazure.com/subscriptions)。
2. 选择想要更改的订阅。
3. 选择“编辑订阅详细信息”。
   
    ![Azure 订阅信息的屏幕截图，突出显示了“编辑订阅详细信息”](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)
4. 在“服务管理员”框中，输入新服务管理员的电子邮件地址。
   
    !["编辑订阅"对话框的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>将 Office 365 租户与 Azure 订阅关联
若要将 Office 365 租户与 Azure 订阅关联，请执行以下步骤：

1. 使用服务管理员凭据登录到[帐户管理门户](https://account.windowsazure.com/subscriptions)。
2. 在左窗格中选择“ACTIVE DIRECTORY”。
   
   ![Active Directory 条目的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
   
   > [!NOTE]
   > 你应该不会看到 Office 365 租户。 如果看到，请跳过下一步。
   > 
   > 
   
   ![Azure Active Directory 默认目录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)
3. 将 Office 365 租户添加到 Azure 订阅。
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“新建” > **“目录”** > **“自定义创建”**。
   
    ![Azure Active Directory 自定义创建的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
    b. 在“添加目录”页的“目录”下，选择“使用现有目录”。 然后选择“我已准备好立即注销”，再选择“完成”![完成图标](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
   
    ![“使用现有目录”的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
    c. 注销后，使用 Office 365 租户的全局管理员凭据登录。
   
    ![Office 365 全局管理员登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
    d.单击“下一步”。 选择“继续”。
   
    ![验证的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择“立即注销”。
   
    ![注销的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
    f. 使用服务管理员凭据登录到[帐户管理门户](https://account.windowsazure.com/subscriptions)。
   
    ![Azure 登录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
    g. 你应该会在仪表板中看到 Office 365 租户。
   
    ![仪表板的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)
4. 更改与 Azure 订阅关联的目录。
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“设置”。
   
    ![Azure 经典门户设置图标的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
    b. 选择 Azure 订阅，然后选择“编辑目录”。
    ![Azure 订阅编辑目录的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
    c. 选择“下一步”![下一步图标](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)。
   
    ![“更改关联的目录”的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
   > [!WARNING]
   > 你将收到一个警告，指出将删除所有共同管理员。
   > 
   > 
   
    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
   > [!WARNING]
   > 此外，还将删除现有资源组中所有具有“已分配”访问权限的[基于角色的访问控制 (RBAC)](active-directory/role-based-access-control-configure.md) 用户。 但是，你收到的警告仅提到删除共同管理员。
   > 
   > 
   
    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
    d.单击“下一步”。 选择“完成”![完成图标](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
5. 现在可以向 Azure Active Directory 租户添加 Office 365 组织帐户作为共同管理员。
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“管理员”选项卡，然后选择“添加”。
   
    ![Azure 经典门户设置管理员选项卡的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
    b. 输入 Office 365 租户的组织帐户，选择 Azure 订阅，然后选择“完成”![完成图标](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。
   
    ![Azure 添加共同管理员对话框的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
    c. 返回到“管理员”选项卡。 此时会看到显示为共同管理员的组织帐户。
   
    ![管理员选项卡的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
6. 接下来，你可以测试共同管理员的访问权限。
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 注销帐户管理门户。
   
    b. 打开[帐户管理门户](https://account.windowsazure.com/subscriptions)或 [Azure 门户](https://portal.azure.com/)。
   
    c. 如果 Azure 登录页有“使用组织帐户登录”链接，请选择该链接。 否则，请跳过此步骤。
   
    ![Azure 登录页的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)
   
    d.单击“下一步”。 输入共同管理员的凭据，然后选择“登录”。
   
    ![Azure 登录页的屏幕截图](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>后续步骤
相关方案包括：

* 用户已经有了一个 Office 365 订阅，准备进行 Azure 订阅，但想要使用现有的 Office 365 用户帐户进行 Azure 订阅。
* 用户是 Azure 订户，想要为现有 Azure Active Directory 实例中的用户获取 Office 365 订阅。

若要了解如何完成这些任务，请参阅 [Use your existing Office 365 account with your Azure subscription, or vice versa](billing-use-existing-office-365-account-azure-subscription.md)（对 Azure 订阅使用现有的 Office 365 帐户，或者相反）。




<!--HONumber=Nov16_HO3-->


