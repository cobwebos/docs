---
title: 删除 Azure AD 目录-Azure Active Directory |Microsoft Docs
description: 说明如何准备要删除的 Azure AD 目录，包括自助服务目录
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7993810343f6bd925afd54cc38a8302420d6aec
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72439352"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>删除 Azure Active Directory 中的目录

删除 Azure AD 目录后，还会删除该目录中包含的所有资源。 删除之前，通过最小化其关联资源来准备你的组织。 只有 Azure Active Directory （Azure AD）全局管理员才能从门户中删除 Azure AD 目录。

## <a name="prepare-the-directory"></a>准备目录

如果某个目录通过了多个检查，则无法删除该 Azure AD 目录。 这些检查降低了删除 Azure AD 目录对用户访问权限产生负面影响的风险，例如，能够登录到 Office 365 或访问 Azure 中的资源。 例如，如果无意中删除了与某个订阅关联的目录，则用户无法访问该订阅的 Azure 资源。 需检查以下条件：

* 该目录中不能有用户，只需要一个全局管理员即可删除该目录。 只有在删除所有其他用户后，才能删除该目录。 如果用户是从本地同步的，则必须先关闭同步，并且必须使用 Azure 门户或 Azure PowerShell cmdlet 在云目录中删除用户。
* 该目录中不能有任何应用程序。 必须先删除任何应用程序，然后才能删除该目录。
* 不能有任何多重身份验证提供程序链接到该目录。
* 与该目录相关联的任何 Microsoft Online Services（例如 Microsoft Azure、Office 365 或 Azure AD Premium）不存在任何订阅。 例如，如果在 Azure 中创建了一个默认目录，并且 Azure 订阅仍然依赖于此目录进行身份验证，则你不能删除此目录。 类似地，如果其他用户已将订阅与某个目录相关联，则你无法删除该目录。

## <a name="delete-the-directory"></a>删除目录

1. 使用组织的全局管理员帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择“Azure Active Directory”。

3. 切换到要删除的目录。
  
   ![删除之前确认组织](./media/directory-delete-howto/delete-directory-command.png)

4. 选择“删除目录”。
  
   ![选择要删除组织的命令](./media/directory-delete-howto/delete-directory-list.png)

5. 如果你的目录未通过一项或多项检查，则会提供一个链接，指向有关如何传递的详细信息。 通过所有检查后，选择“删除”以完成此过程。

## <a name="if-you-cant-delete-the-directory"></a>如果无法删除该目录

配置 Azure AD 目录时，可能还会为组织激活基于许可证的订阅，如 Azure AD Premium P2、Office 365 商业高级版或企业移动性 + 安全性 E5。 若要避免意外的数据丢失，则在订阅被完全删除之前，将无法删除该目录。 订阅必须处于**取消预配**状态才能允许删除目录。 已**过期**或**已取消**的订阅将移动到**禁用**状态，最后阶段是**取消预配**状态。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 如需深入了解 Office 365 数据保留期和订阅生命周期，请参阅 [Office 365 商业版订阅结束后我的数据和访问会出现什么情况？](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)。 

订阅状态 | 数据 | 对数据的访问
----- | ----- | -----
活动（30 天试用） | 所有用户均可访问数据 | 用户可正常访问 Office 365 文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源 
已到期（30 天） | 所有用户均可访问数据| 用户可正常访问 Office 365 文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源
已禁用（30 天） | 仅管理员可访问数据 | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心，但无法将许可证分配给或更新用户
已取消预配（禁用后 30 天） | 已删除数据（没有使用其他服务时自动删除） | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心，以购买和管理其他订阅

## <a name="delete-a-subscription"></a>删除订阅

你可以使用 Microsoft 365 管理中心将订阅置于**取消预配**状态，以便在三天内删除。

1. 使用组织中的全局管理员帐户登录到[Microsoft 365 管理中心](https://admin.microsoft.com)。 如果尝试删除包含初始默认域 contoso.onmicrosoft.com 的 "Contoso" 目录，请使用 UPN （如 admin@contoso.onmicrosoft.com）登录。

2. 通过确保启用 "**试用新管理中心**" 切换功能，预览新 Microsoft 365 管理中心。

   ![预览全新的 M365 管理中心体验](./media/directory-delete-howto/preview-toggle.png)

3. 启用新的管理中心后，你需要取消订阅，然后才能将其删除。 选择 "**计费**" 并选择 "**产品" & 服务**"，然后选择要取消的订阅的"**取消订阅**"。 你将转到反馈页。

   ![选择要取消的订阅](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 完成反馈表，然后选择 "**取消订阅**" 来取消订阅。

   ![订阅预览中的取消命令](./media/directory-delete-howto/cancel-command.png)

5. 你现在可以删除订阅。 选择要删除的订阅的 "**删除**"。 如果在 "**产品 & 服务**" 页中找不到该订阅，请确保将 "**订阅状态**" 设置为 "**所有**"。

   ![删除用于删除订阅的链接](./media/directory-delete-howto/delete-command.png)

6. 选择 "**删除订阅**" 可删除订阅并接受条款和条件。 所有数据将在三天内永久删除。 如果改变主意，可以在三天内[重新激活订阅](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)。
  
   ![仔细阅读条款和条件](./media/directory-delete-howto/delete-terms.png)

7. 现在，订阅状态已更改，订阅被标记为删除。 订阅在 72 小时后会进入“已取消预配”状态。

8. 在目录中删除了某个订阅并且已超过了72小时后，就可以再次登录到 Azure AD 管理中心，而且不需要执行任何操作，也不会阻止目录删除。 应能成功删除 Azure AD 目录。
  
   ![在删除屏幕通过订阅检查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我有一个阻止删除的试用订阅

有[自助注册产品](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide)，如 Microsoft Power BI、Rights Management 服务、Microsoft Power Apps 或 Dynamics 365，单独的用户可通过 Office 365 注册，这也会在你的 Azure AD 目录中创建用于身份验证的来宾用户. 这些自助服务产品会阻止目录删除，直到它们完全从目录中删除，以避免数据丢失。 它们只能由 Azure AD 管理员删除，无论用户是单独注册还是分配了产品。

提供两种类型的自助服务注册产品的方式： 

* 组织级别分配： Azure AD 管理员将产品分配到整个组织，用户可以使用该服务进行此组织级别的分配，即使它们未单独获得许可也是如此。
* 用户级别分配：在自助注册过程中，个人用户实际上不需要管理员就将产品分配给自己。组织被管理员管理后（请参阅[管理员接管非托管目录](domains-admin-takeover.md)，然后，管理员可以直接向用户分配产品，无需自助注册。  

当你开始删除自助服务注册产品时，该操作将永久删除数据并删除对服务的所有用户访问权限。 然后，将阻止任何已单独分配了产品/服务或组织级别的用户登录或访问任何现有数据。 如果你想要通过自助注册产品（如[Microsoft Power BI 仪表板](https://docs.microsoft.com/power-bi/service-export-to-pbix)或[Rights Management Services 策略配置](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)）防止数据丢失，请确保将数据备份并保存到其他位置。

有关当前可用的自助服务注册产品和服务的详细信息，请参阅[可用的自助服务程序](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 有关 Office 365 数据保留和订阅生命周期的详细信息，请参阅 [我的 office 365 for business 订阅结束后，我的数据和访问发生了什么情况？](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)。

产品状态 | 数据 | 对数据的访问
------------- | ---- | --------------
活动（30 天试用） | 所有用户均可访问数据 | 用户可以正常访问自助注册产品、文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源
已删除 | 删除的数据 | 用户无法访问自助注册产品、文件或应用<br>管理员可以访问 Microsoft 365 管理中心，以购买和管理其他订阅

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何在 Azure 门户中删除自助服务注册产品？

你可以将自助服务注册产品（如 Microsoft Power BI 或 Azure Rights Management 服务）置于 Azure AD 门户中立即删除的**删除**状态。

1. 登录到 [Azure AD 管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with 组织中的全局管理员帐户。 如果尝试删除包含初始默认域 contoso.onmicrosoft.com 的 "Contoso" 目录，请使用 UPN （如 admin@contoso.onmicrosoft.com）登录。

2. 选择 "**许可证**"，然后选择 " **自助注册产品**"。 你可以从基于座位的订阅中单独查看所有自助服务注册产品。 选择要永久删除的产品。 下面是 Microsoft Power BI 的示例：

    ![用户名键入错误或未找到](./media/directory-delete-howto/licenses-page.png)

3. 选择 " **删除**" @no__t 1to "删除产品" 并接受 "立即删除数据" 和 "不可撤销" 这一术语。 此删除操作将删除所有用户并删除对该产品的组织访问权限。 单击 "是" 以继续删除删除操作。  

    ![用户名键入错误或未找到](./media/directory-delete-howto/delete-product.png)

4. 选择 **"是**" 时，将启动对自助服务产品的删除。 通知会告诉您正在进行的删除。  

    ![用户名键入错误或未找到](./media/directory-delete-howto/progress-message.png)

5. 现在，自助注册产品状态已更改为 "**已删除**"。 刷新页面后，应从**自助注册产品**页中删除该产品。  

    ![用户名键入错误或未找到](./media/directory-delete-howto/product-deleted.png)

6. 删除所有产品后，你可以再次登录到 Azure AD 管理中心，并且应不需要执行任何操作，也不会阻止删除目录。 应能成功删除 Azure AD 目录。

    ![用户名键入错误或未找到](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>后续步骤

[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory/)
