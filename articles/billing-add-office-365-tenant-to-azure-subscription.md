<properties
	pageTitle="将 Office 365 租户与 Azure 订阅搭配使用 | Azure"
	description="了解如何将 Office 365 目录（租户）添加到 Azure 订阅来建立关联。"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="billing"
	ms.date="06/15/2016"
	ms.author="cjiang"/>

# 将 Office 365 租户与 Azure 订阅关联
场景：你之前分别获取了 Azure 和 Office 365 订阅，现在想要能够从 Azure 订阅访问 Office 365 租户。本文说明如何轻松实现此目的。

> [AZURE.NOTE] 本文不适用于企业协议 (EA) 客户。

## 快速指南
如果想要将 Office 365 租户与 Azure 订阅关联，请使用 Azure 帐户添加 Office 365 租户，然后将 Azure 订阅与 Office 365 租户关联。请参阅详细步骤。

## 详细步骤
在本场景中，Kelley Wall 是一位在帐户 kelly.wall@outlook.com 下拥有 Azure 订阅的用户。Kelley 在帐户 kelley.wall@contoso.onmicrosoft.com 下还拥有 Office 365 订阅。现在，Kelley 想通过 Azure 订阅访问 Office 365 租户。

![msa-aad-status](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![office-365-user](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

**先决条件：**

- 你需要 Azure 订阅的服务管理员凭据。共同管理员无法执行一部分步骤。
- 你需要 Office 365 租户的全局管理员凭据。
- 服务管理员的电子邮件地址不能包含在 Office 365 租户中。
- 服务管理员的电子邮件地址不能与 Office 365 租户的任何全局管理员的电子邮件地址匹配。
- 如果你当前使用的电子邮件地址既是 Microsoft 帐户又是组织帐户，建议你暂时更改 Azure 订阅的服务管理员，以使用另一个 Microsoft 帐户。这会限制该过程具有的已知限制。你可以在 [Microsoft 帐户注册页](https://signup.live.com/)上创建一个新的 Microsoft 帐户。

	若要更改服务管理员，请遵循以下步骤：

	1. 登录到[帐户管理门户](https://account.windowsazure.cn/subscriptions)。
	2. 选择要更改的订阅。
	3. 在右侧，单击“编辑订阅详细信息”。

		![azure-edit-subscription-details](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)

	4. 在“服务管理员”框中，输入新服务管理员的电子邮件地址。

		![change-subscription-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

若要将 Office 365 租户与 Azure 订阅关联，请遵循以下步骤：

1. 	使用服务管理员凭据登录[帐户管理门户](https://account.windowsazure.cn/subscriptions)。
2.	在左窗格中，单击“ACTIVE DIRECTORY”。

	![classic-portal-active-directory-entry](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

	> [AZURE.NOTE] 你应该不会看到 Office 365 租户。如果看到，请跳过下一步。

	![aad-tenant-default](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)

3. 将 Office 365 租户添加到 Azure 订阅。
	1. 单击“新建”>“目录”>“自定义创建”。

		![aad-custom-create](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)

	2. 在“添加目录”页上，选择“目录”下的“使用现有目录”，单击选择“我已准备好立即注销”，然后单击“完成”![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

		![add-directory-use-existing](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)

	3. 注销后，使用 Office 365 租户的全局管理员凭据登录。

		![sign-in-global-admin-office-365](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)

	4. 单击“继续”。

		![use-contoso-directory-azure-verify](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)

	5. 单击“立即注销”。

		![use-contoso-directory-azure-confirm-and-sign-out](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)

	6. 使用服务管理员凭据登录[帐户管理门户](https://account.windowsazure.cn/subscriptions)。

		![azure-sign-in-service-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

	7. 你应该会在仪表板中看到 Office 365 租户。

		![office-365-tenant-appear-in-azure](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

4. 更改与 Azure 订阅关联的目录。

	1. 单击“设置”。

		![azure-classic-portal-settings-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)

	2. 单击 Azure 订阅，然后单击“编辑目录”。

		![azure-subscription-edit-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)

	3. 单击“下一步”![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)。

		![azure-change-associated-directory](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)

		> [AZURE.WARNING] 你将收到一个警告，指出将删除所有共同管理员。

		![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)

		>[AZURE.WARNING] 此外，还将删除现有资源组中所有具有“已分配”访问权限的[基于角色的访问控制 (RBAC)](./active-directory/role-based-access-control-configure.md) 用户。但是，你收到的警告仅提到删除共同管理员。

		![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)

	4. 单击“完成” ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

5. 现在可以向 AAD 租户添加 Office 365 组织帐户作为共同管理员。

	1. 单击“管理员”选项卡，然后单击“添加”。

		![azure-classic-portal-settings-administrators](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)

	2. 输入 Office 365 租户的组织帐户，单击选择 Azure 订阅，然后单击“完成” ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)。

		![azure-add-co-administrator](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)

	3. 返回到“管理员”选项卡，你应该会看到显示为共同管理员的组织帐户。

		![azure-co-administrator-added](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)

6. 接下来，你可以测试共同管理员的访问权限。

	1. 注销帐户管理门户。
	2. 打开[帐户管理门户](https://account.windowsazure.cn/subscriptions)或 [Azure 门户](https://portal.azure.com/)。
	3. 如果 Azure 登录页有“使用组织帐户登录”链接，请单击该链接。否则，请跳过此步骤。

		![Azure-sign-in-with-orgid-link](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)

	4. 输入共同管理员的凭据，然后单击“登录”。

		![Azure-sign-in-with-co-admin](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## 后续步骤
相关场景：你已经有一个 Office 365 订阅，准备进行 Azure 订阅，但想要使用现有的 Office 365 用户帐户进行 Azure 订阅。或者，你是 Azure 订户，想要为现有 Azure Active Directory 中的用户获取 Office 365 订阅。若要了解如何完成这些任务，请参阅[对 Azure 订阅使用现有的 Office 365 帐户，或者相反](billing-use-existing-office-365-account-azure-subscription.md)。

<!---HONumber=Mooncake_0627_2016-->