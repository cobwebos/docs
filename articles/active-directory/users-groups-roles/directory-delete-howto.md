---
title: 删除 Azure AD 组织（租户）- Azure Active Directory | Microsoft Docs
description: 说明如何使 Azure AD 组织（租户）为删除做好准备，包括自助服务组织
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 05/21/2020
ms.author: curtand
ms.reviewer: addimitu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c0b203647bc57c7c7eb48e321895cf3b3fa7d44
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795416"
---
# <a name="delete-a-tenant-in-azure-active-directory"></a>在 Azure Active Directory 中删除一个租户

删除 Azure AD 组织（租户）时，也会删除包含在该组织中的所有资源。 在删除前要最大程度地减少与组织关联的资源，使组织为删除做好准备。 只有 Azure Active Directory (Azure AD) 全局管理员可从门户中删除 Azure AD 组织。

## <a name="prepare-the-organization"></a>准备组织

除非 Azure AD 中的组织通过了若干项检查，否则无法将其删除。 这些检查可以降低删除 Azure AD 组织后对用户访问造成负面影响（例如，影响登录 Office 365 或访问 Azure 中的资源）的风险。 例如，如果意外删除了与订阅关联的组织，则用户将无法访问该订阅的 Azure 资源。 需检查以下情况：

* 除了一位要删除组织的全局管理员外，Azure AD 组织（租户）中不得有任何用户。 在删除组织之前，必须先删除任何其他用户。 如果用户是从本地同步的，则必须先关闭同步，并且必须使用 Azure 门户或 Azure PowerShell cmdlet 从云组织中删除这些用户。
* 该组织中不能有任何应用程序。 在删除组织之前，必须先删除任何应用程序。
* 不能有任何多重身份验证提供程序链接到该组织。
* 与该组织相关联的任何 Microsoft Online Services（例如 Microsoft Azure、Office 365 或 Azure AD Premium）不存在任何订阅。 例如，如果在 Azure 中创建了一个默认 Azure AD 组织，并且 Azure 订阅仍然依赖于此组织进行身份验证，则不能删除此组织。 类似地，如果其他用户已将订阅与某个组织相关联，则无法删除该组织。

## <a name="delete-the-organization"></a>删除组织

1. 使用组织的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择“Azure Active Directory” 。

3. 切换到要删除的组织。
  
   ![删除前确认组织](./media/directory-delete-howto/delete-directory-command.png)

4. 选择“删除租户”。
  
   ![选择命令以删除组织](./media/directory-delete-howto/delete-directory-list.png)

5. 如果组织未通过一个或多个检查，系统会提供相关链接，介绍如何通过检查的详细信息。 通过所有检查后，请选择“删除”，此过程结束。

## <a name="if-you-cant-delete-the-organization"></a>如果无法删除组织

如果配置了 Azure AD 组织，你可能已为组织激活了基于许可证的订阅，例如 Azure AD Premium P2、Office 365 商业高级版或企业移动性 + 安全性 E5。 若要避免意外的数据丢失，在订阅被完全删除之前，无法删除组织。 订阅必须处于“已取消预配”状态才能删除组织。 “已过期”或“已取消”订阅移动到“已禁用”状态后，最后一个阶段就是“已取消预配”状态   。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 如需深入了解 Office 365 数据保留期和订阅生命周期，请参阅 [Office 365 商业版订阅结束后我的数据和访问会出现什么情况？](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)。 

订阅状态 | 数据 | 对数据的访问
----- | ----- | -----
活动（30 天试用） | 所有用户均可访问数据 | 用户可正常访问 Office 365 文件或应用<br>管理员可正常访问 Microsoft 365 管理中心和资源 
已到期（30 天） | 所有用户均可访问数据| 用户可正常访问 Office 365 文件或应用<br>管理员可正常访问 Microsoft 365 管理中心和资源
已禁用（30 天） | 仅管理员可访问数据 | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心，但无法向用户分配许可证，也无法更新用户
已取消预配（禁用后 30 天） | 已删除数据（没有使用其他服务时自动删除） | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心以购买和管理其他订阅

## <a name="delete-a-subscription"></a>删除订阅

可将订阅置于“已取消预配”状态，通过 Microsoft 365 管理中心在三天后将其删除。

1. 使用组织的全局管理员帐户登录到 [Microsoft 365 管理中心](https://admin.microsoft.com)。 如果尝试删除具有初始默认域 contoso.onmicrosoft.com 的“Contoso”组织，请使用 UPN（例如 admin@contoso.onmicrosoft.com）登录。

2. 确保已启用“试用新的管理中心”切换后，即可预览新版 Microsoft 365 管理中心。

   ![预览新版 M365 管理中心体验](./media/directory-delete-howto/preview-toggle.png)

3. 启用新的管理中心后，在删除订阅之前需要先取消订阅。 选择“计费”并选择“产品和服务”，然后为要取消的订阅选择“取消订阅”。 你将进入反馈页面。

   ![选择要取消的订阅](./media/directory-delete-howto/cancel-choose-subscription.png)

4. 完成反馈表，并选择“取消订阅”以取消该订阅。

   ![订阅预览中的取消命令](./media/directory-delete-howto/cancel-command.png)

5. 现在可以删除订阅。 为要删除的订阅选择“删除”。 如果在“产品和服务”页中找不到订阅，请确保已将“订阅状态”设置为“所有”。

   ![删除用于删除订阅的链接](./media/directory-delete-howto/delete-command.png)

6. 选择“删除订阅”以删除该订阅并接受条款和条件。 所有数据将在三天内永久删除。 如果改变了主意，可在这三天时间内[重新激活订阅](/office365/admin/subscriptions-and-billing/reactivate-your-subscription?view=o365-worldwide)。
  
   ![仔细阅读条款和条件](./media/directory-delete-howto/delete-terms.png)

7. 现在订阅状态已改变，且订阅已被标记为删除。 订阅在 72 小时后会进入“已取消预配”状态。

8. 删除组织中的订阅并且时间已过 72 小时后，可再次登录 Azure AD 管理中心，这里应没有必需的操作，也没有订阅影响你删除组织。 你应该可以成功删除 Azure AD 组织。
  
   ![在删除屏幕通过订阅检查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我有一个影响删除的试用订阅

对于 Microsoft Power BI、Rights Management Services、Microsoft Power Apps 或 Dynamics 365 等[自助服务注册产品](/office365/admin/misc/self-service-sign-up?view=o365-worldwide)，个人用户可通过 Office 365 注册，这也会在 Azure AD 组织中创建用于进行身份验证的来宾用户。 这些自助服务产品会阻止删除目录，直到这些产品从组织中完全删除，以避免数据丢失。 它们只能由 Azure AD 管理员删除，无论用户是单独注册还是分配了产品。

自助服务注册产品的分配方式有两种： 

* 组织级别分配：Azure AD 管理员会将产品分配到整个组织，用户可以使用此组织级别分配的服务，即使他们没有单独获得许可也无妨。
* 用户级别分配：在自助服务注册期间，单个用户实际上是在没有管理员的情况下将产品分配给自己的。组织被管理员管理后（请参阅[管理员接管非托管组织](domains-admin-takeover.md)），管理员就可以直接向用户分配产品，无需进行自助服务注册。  

开始删除自助服务注册产品时，该操作将永久删除数据并删除用户对该服务的所有访问权限。 然后，将阻止任何已单独分配产品/服务或组织级别的用户登录或访问任何现有数据。 如果想要防止自助服务注册产品（如 [Microsoft Power BI 仪表板](/power-bi/service-export-to-pbix)或[权限管理服务策略配置](/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)）中的数据丢失，请确保将数据备份并保存到其他位置。

若要详细了解当前可用的自助服务注册产品和服务，请参阅[可用的自助服务计划](/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 如需深入了解 Office 365 数据保留期和订阅生命周期，请参阅 [Office 365 商业版订阅结束后我的数据和访问会出现什么情况？](/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)。

产品状态 | 数据 | 对数据的访问
------------- | ---- | --------------
活动（30 天试用） | 所有用户均可访问数据 | 用户可正常访问自助服务注册产品、文件或应用<br>管理员可正常访问 Microsoft 365 管理中心和资源
Deleted | 数据已删除 | 用户无法访问自助服务注册产品、文件或应用<br>管理员可以访问 Microsoft 365 管理中心以购买和管理其他订阅

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何在 Azure 门户中删除自助服务注册产品？

可以将自助服务注册产品（如 Microsoft Power BI 或 Azure Rights Management Services）置于“删除”状态，以立即在 Azure AD 门户中将其删除。

1. 使用组织中的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。 如果尝试删除具有初始默认域 contoso.onmicrosoft.com 的“Contoso”组织，请使用 UPN（例如 admin@contoso.onmicrosoft.com）登录。

2. 选择“许可证”，然后选择“自助服务注册产品”。 可以从基于席位的订阅中单独查看所有自助服务注册产品。 选择要永久删除的产品。 下面是 Microsoft Power BI 中的一个示例：

    ![用户名键入错误或未找到](./media/directory-delete-howto/licenses-page.png)

3. 选择“删除”以删除产品并接受数据立即删除且不可撤销的条款。 此删除操作将删除所有用户并将删除组织对该产品的访问权限。 单击“是”以继续执行删除操作。  

    ![用户名键入错误或未找到](./media/directory-delete-howto/delete-product.png)

4. 如果选择“是”，将启动对自助服务产品的删除。 会通知你正在执行删除操作。  

    ![用户名键入错误或未找到](./media/directory-delete-howto/progress-message.png)

5. 现在，自助服务注册产品状态已更改为“已删除”。 刷新页面时，该产品应该已从“自助服务注册产品”页中删除。  

    ![用户名键入错误或未找到](./media/directory-delete-howto/product-deleted.png)

6. 删除所有产品后，可再次登录 Azure AD 管理中心，这里应没有必需的操作，也没有产品影响你删除组织。 你应该可以成功删除 Azure AD 组织。

    ![用户名键入错误或未找到](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>后续步骤

[Azure Active Directory 文档](../index.yml)