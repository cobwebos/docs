---
title: 删除 Azure AD 目录 - Azure Active Directory | Microsoft Docs
description: 介绍如何准备要删除的 Azure AD 目录，包括自助服务目录
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
ms.openlocfilehash: 47a60ed44ddf057ef983f8f76f23fd784bc3efd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961817"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>删除 Azure Active Directory 中的目录

删除 Azure AD 目录时，也会删除包含在该目录中的所有资源。 准备组织时，尽量减少关联的资源，然后才能将其删除。 只有 Azure Active Directory (Azure AD) 全局管理员可以从门户中删除 Azure AD 目录。

## <a name="prepare-the-directory"></a>准备目录

在删除 Azure AD 中的某个目录之前，必须对其进行多项检查。 这些检查可以降低删除 Azure AD 目录后对用户访问造成负面影响（例如，影响用户登录 Office 365 或访问 Azure 中的资源）的风险。 例如，如果与某个订阅关联的目录被无意删除，则用户无法访问该订阅的 Azure 资源。 需检查以下条件：

* 目录中不能有用户，只能有一个负责删除该目录的全局管理员。 只有在删除所有其他用户后，才能删除该目录。 如果用户是从本地同步的，则必须先关闭同步，并且必须使用 Azure 门户或 Azure PowerShell cmdlet 从云目录中删除这些用户。
* 该目录中不能有任何应用程序。 只有在删除所有应用程序后，才能删除目录。
* 不能有任何多重身份验证提供程序关联到该目录。
* 与该目录相关联的任何 Microsoft Online Services（例如 Microsoft Azure、Office 365 或 Azure AD Premium）不存在任何订阅。 例如，如果在 Azure 中创建了一个默认目录，并且 Azure 订阅仍然依赖于此目录进行身份验证，则你不能删除此目录。 类似地，如果其他用户已将订阅与某个目录相关联，则你无法删除该目录。

## <a name="delete-the-directory"></a>删除目录

1. 使用属于组织的全局管理员的帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择**Azure 活动目录**。

3. 切换到要删除的目录。
  
   ![在删除之前确认组织](./media/directory-delete-howto/delete-directory-command.png)

4. 选择“删除目录”****。
  
   ![选择用于删除组织的命令](./media/directory-delete-howto/delete-directory-list.png)

5. 如果目录没有通过一项或多项检查，则会出现一个详细说明如何通过的链接。 通过所有检查后，选择“删除”**** 以完成此过程。

## <a name="if-you-cant-delete-the-directory"></a>如果无法删除目录

配置 Azure AD 目录时，您可能还为组织激活了基于许可证的订阅，例如 Azure AD 高级 P2、Office 365 业务高级版或企业移动性 + 安全 E5。 为了避免意外数据丢失，在完全删除订阅之前，您无法删除目录。 订阅必须处于**已取消预配**状态，才能允许删除目录。 **过期**或**已取消**的订阅将移动到 **"已禁用"** 状态，而最后阶段是 **"已取消预配"** 状态。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 如需深入了解 Office 365 数据保留期和订阅生命周期，请参阅 [Office 365 商业版订阅结束后我的数据和访问会出现什么情况？](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)。 

订阅状态 | 数据 | 对数据的访问
----- | ----- | -----
活动（30 天试用） | 所有用户均可访问数据 | 用户可正常访问 Office 365 文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源 
已到期（30 天） | 所有用户均可访问数据| 用户可正常访问 Office 365 文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源
已禁用（30 天） | 仅管理员可访问数据 | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心，但不能向用户分配许可证或更新用户
已取消预配（禁用后 30 天） | 已删除数据（没有使用其他服务时自动删除） | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心以购买和管理其他订阅

## <a name="delete-a-subscription"></a>删除订阅

您可以使用 Microsoft 365 管理中心将订阅置于 **"已取消预配"** 状态，在三天内删除。

1. 使用组织中全局管理员的帐户登录到[Microsoft 365 管理中心](https://admin.microsoft.com)。 如果您尝试删除具有初始默认域contoso.onmicrosoft.com的"Contoso"目录，请使用 UPN（如admin@contoso.onmicrosoft.com） 登录。

2. 通过确保**启用"尝试新的管理中心**切换"来预览新的 Microsoft 365 管理中心。

   ![预览新的 M365 管理中心体验](./media/directory-delete-howto/preview-toggle.png)

3. 启用新管理中心后，您需要取消订阅，然后才能将其删除。 选择 **"计费****"并选择"产品&服务**，然后选择 **"取消**要取消订阅的订阅"。 您将被带到反馈页面。

   ![选择要取消的订阅](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 填写反馈表并选择 **"取消订阅**"以取消订阅。

   ![取消订阅预览中的命令](./media/directory-delete-howto/cancel-command.png)

5. 您现在可以删除订阅。 为要删除的订阅选择 **"删除**"。 如果在 **"产品&服务**"页中找不到订阅，请确保将**订阅状态**设置为 **"全部**"。

   ![删除用于删除订阅的链接](./media/directory-delete-howto/delete-command.png)

6. 选择 **"删除订阅**"以删除订阅并接受条款和条件。 所有数据将在三天内永久删除。 如果您改变主意，您可以在三天内[重新激活订阅](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)。
  
   ![仔细阅读条款和条件](./media/directory-delete-howto/delete-terms.png)

7. 现在订阅状态已更改，并且订阅标记为删除。 订阅在 72 小时后会进入“已取消预配”**** 状态。

8. 删除目录中的订阅且已 72 小时过后，可以再次登录到 Azure AD 管理中心，并且不应执行任何必需的操作，也没有阻止目录删除的订阅。 您应该能够成功删除 Azure AD 目录。
  
   ![在删除屏幕通过订阅检查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我有一个阻止删除的试用订阅

有[自助服务注册产品](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide)，如 Microsoft Power BI、权限管理服务、Microsoft 电源应用或 Dynamics 365，个人用户可以通过 Office 365 进行注册，Office 365 还会创建来宾用户，用于在 Azure AD 目录中进行身份验证。 这些自助服务产品阻止目录删除，直到它们从目录中完全删除，以避免数据丢失。 无论用户是单独注册还是已分配产品，Azure AD 管理员都只能删除它们。

在分配它们的方式中，有两种类型的自助服务注册产品： 

* 组织级分配：Azure AD 管理员将产品分配给整个组织，用户可以在此组织级别分配中主动使用服务，即使他们不是单独许可的。
* 用户级别分配：在自助服务注册期间，单个用户基本上在没有管理员的情况下将产品分配给自己。一旦组织由管理员管理（请参阅[管理员接管非托管目录](domains-admin-takeover.md)，则管理员可以直接将产品分配给没有自助服务注册的用户。  

当您开始删除自助服务注册产品时，该操作会永久删除数据并删除所有用户对服务的访问权限。 然后，单独或组织级别分配产品/服务的任何用户都被禁止登录或访问任何现有数据。 如果要使用自助服务注册产品（如[Microsoft Power BI 仪表板](https://docs.microsoft.com/power-bi/service-export-to-pbix)或[权限管理服务策略配置](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)）防止数据丢失，请确保将数据备份并保存在其他位置。

有关当前可用的自助服务注册产品和服务的详细信息，请参阅[可用自助服务程序](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 如需深入了解 Office 365 数据保留期和订阅生命周期，请参阅 [Office 365 商业版订阅结束后我的数据和访问会出现什么情况？](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)。

产品状态 | 数据 | 对数据的访问
------------- | ---- | --------------
活动（30 天试用） | 所有用户均可访问数据 | 用户可以正常访问自助服务注册产品、文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源
Deleted | 已删除数据 | 用户无法访问自助服务注册产品、文件或应用<br>管理员可以访问 Microsoft 365 管理中心以购买和管理其他订阅

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何在 Azure 门户中删除自助服务注册产品？

您可以将自助服务注册产品（如 Microsoft Power BI 或 Azure 权限管理服务）置于 **"删除"** 状态，以便立即在 Azure AD 门户中删除。

1. 使用组织中全局管理员的帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。 如果您尝试删除具有初始默认域contoso.onmicrosoft.com的"Contoso"目录，请使用 UPN（如admin@contoso.onmicrosoft.com）登录。

2. 选择**许可证**，然后选择**自助服务注册产品**。 您可以查看所有自助服务注册产品与基于座位的订阅分开。 选择要永久删除的产品。 下面是 Microsoft Power BI 中的示例：

    ![键入错误的用户名，或者找不到该用户名](./media/directory-delete-howto/licenses-page.png)

3. 选择 **"删除"** 可删除产品并接受立即删除且不可撤销的数据的条款。 此删除操作将删除所有用户并删除组织对产品的访问权限。 单击"是"以继续删除。  

    ![键入错误的用户名，或者找不到该用户名](./media/directory-delete-howto/delete-product.png)

4. 当您选择 **"是**"时，将启动自助产品的删除。 有一个通知将告诉您正在进行的删除。  

    ![键入错误的用户名，或者找不到该用户名](./media/directory-delete-howto/progress-message.png)

5. 现在，自助服务注册产品状态已更改为 **"已删除**"。 刷新页面时，应从**自助服务注册产品**页面中删除产品。  

    ![键入错误的用户名，或者找不到该用户名](./media/directory-delete-howto/product-deleted.png)

6. 删除所有产品后，可以再次登录到 Azure AD 管理中心，并且不应执行任何必需的操作，也不应阻止产品阻止目录删除。 您应该能够成功删除 Azure AD 目录。

    ![键入错误的用户名，或者找不到该用户名](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>后续步骤

[Azure 活动目录文档](https://docs.microsoft.com/azure/active-directory/)
