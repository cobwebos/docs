---
title: 删除 Azure AD 目录的 Azure Active Directory |Microsoft Docs
description: 介绍如何进行准备以进行删除，包括自助服务目录的 Azure AD 目录
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
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ac6b4530414850c52605bac8cb701aa2b877d4
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607290"
---
# <a name="delete-a-directory-in-azure-active-directory"></a>删除 Azure Active Directory 的目录中

删除 Azure AD 目录时，也会删除该目录中包含的所有资源。 通过最小化及其关联的资源，在删除之前准备你的组织。 只有 Azure Active Directory (Azure AD) 全局管理员可以从门户删除 Azure AD 目录。

## <a name="prepare-the-directory"></a>准备目录

无法在 Azure AD 之前将传递多个检查中删除一个目录。 这些检查降低风险，删除 Azure AD 目录产生负面影响用户访问权限，例如能够在登录到 Azure 中的 Office 365 或访问资源。 例如，如果无意中删除与订阅关联的目录，用户无法访问该订阅的 Azure 资源。 需检查以下条件：

* 除一个全局管理员是，以删除该目录的目录中可以有任何用户。 只有在删除所有其他用户后，才能删除该目录。 如果用户从本地同步的则同步必须首先已关闭，并且必须使用 Azure 门户或 Azure PowerShell cmdlet 的云目录中删除这些用户。
* 该目录中不能有任何应用程序。 必须先删除任何应用程序，可以删除目录。
* 可以有任何多重身份验证提供程序，链接到该目录。
* 与该目录相关联的任何 Microsoft Online Services（例如 Microsoft Azure、Office 365 或 Azure AD Premium）不存在任何订阅。 例如，如果在 Azure 中创建了一个默认目录，并且 Azure 订阅仍然依赖于此目录进行身份验证，则你不能删除此目录。 类似地，如果其他用户已将订阅与某个目录相关联，则你无法删除该目录。

## <a name="delete-the-directory"></a>删除目录

1. 登录到[Azure AD 管理中心](https://aad.portal.azure.com)是为你的组织的全局管理员的帐户。

2. 选择“Azure Active Directory”。

3. 切换到你想要删除的目录。
  
   ![确认删除之前的组织](./media/directory-delete-howto/delete-directory-command.png)

4. 选择“删除目录”。
  
   ![选择要删除的组织的命令](./media/directory-delete-howto/delete-directory-list.png)

5. 如果你的目录不会通过一个或多个检查，你要提供如何将传递的详细信息的链接。 通过所有检查后，选择“删除”以完成此过程。

## <a name="if-you-cant-delete-the-directory"></a>如果您不能删除目录

配置 Azure AD 目录，你可能有也基于许可证的订阅为组织激活等 Azure AD Premium P2、 Office 365 商业高级版，或企业移动性 + 安全性 E5。 若要避免意外数据丢失，无法完全删除订阅之前删除目录。 订阅必须位于**已取消预配**状态以允许目录删除。 **已过期**或**Canceled**订阅移到**禁用**状态和最后一个阶段是**已取消预配**状态。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 如需深入了解 Office 365 数据保留期和订阅生命周期，请参阅 [Office 365 商业版订阅结束后我的数据和访问会出现什么情况？](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)。 

订阅状态 | 数据 | 对数据的访问
----- | ----- | -----
活动（30 天试用） | 所有用户均可访问数据 | 用户可正常访问 Office 365 文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源 
已到期（30 天） | 所有用户均可访问数据| 用户可正常访问 Office 365 文件或应用<br>管理员可以正常访问 Microsoft 365 管理中心和资源
已禁用（30 天） | 仅管理员可访问数据 | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心内，但不能将许可证分配给或更新用户
已取消预配（禁用后 30 天） | 已删除数据（没有使用其他服务时自动删除） | 用户无法访问 Office 365 文件或应用<br>管理员可以访问 Microsoft 365 管理中心内购买和管理其他订阅

## <a name="delete-a-subscription"></a>删除订阅

可以将订阅放入要在使用 Microsoft 365 管理中心内的三天内删除的已取消预配状态。

1. 登录到[Microsoft 365 管理中心内](https://admin.microsoft.com)是你的组织中的全局管理员的帐户。 如果想要删除"Contoso"初始默认域 contoso.onmicrosoft.com 的目录，请使用登录 UPN 如admin@contoso.onmicrosoft.com。

2. 选择**计费**，然后选择**订阅**，然后选择你想要取消的订阅。 单击“取消”后，刷新该页面。
  
   ![删除用于删除订阅的链接](./media/directory-delete-howto/delete-command.png)
  
3. 选择“删除”，删除订阅并接受条款和条件。 所有数据将在三天内永久删除。 如果改变心意，可在这三天时间内重新激活订阅。
  
   ![请仔细阅读条款和条件](./media/directory-delete-howto/delete-terms.png)

4. 现在订阅状态已改变，订阅已被标记以进行删除。 订阅在 72 小时后会进入“已取消预配”状态。

5. 后已在目录中删除订阅和 72 小时，可以注册恢复到 Azure AD 管理员中心正常再次和有应是任何所需的操作并阻止目录删除任何订阅。 您应能够成功地删除你的 Azure AD 目录。
  
   ![在删除屏幕通过订阅检查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="i-have-a-trial-subscription-that-blocks-deletion"></a>我已将阻止删除的试用版订阅

有[自助服务注册产品](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide)像 Microsoft Power BI、 Rights Management Services、 Microsoft 强大的应用程序或 Dynamics 365，各个用户可以通过 Office 365，这也会创建来宾用户的身份验证中注册Azure AD 目录。 这些自助服务产品阻止目录删除操作，直到它们完全删除从目录，以避免数据丢失。 可以删除它们仅由 Azure AD 管理员是否在用户单独注册或已分配产品。

有两种类型的自助服务注册产品的分配方式： 

* 组织级别分配：Azure AD 管理员将该产品分配给整个组织和用户可以主动使用该服务与此组织级别分配即使他们未获得单独许可。
* 用户级别分配：单个用户在自助注册过程实质上是将产品分配给自身，而无需是管理员。一旦管理员将成为托管组织 (请参阅[管理员接管非托管目录](domains-admin-takeover.md)，然后管理员可以将该产品直接分配到没有自助注册的用户。  

开始时删除的自助服务注册产品，该操作将永久删除数据，并删除到服务的所有用户访问权限。 单独或在组织级别上已分配产品/服务的任何用户然后阻止登录或访问的任何现有数据。 如果你想要防止数据丢失与自助服务注册产品喜欢[Microsoft Power BI 仪表板](https://docs.microsoft.com/power-bi/service-export-to-pbix)或[Rights Management Services 策略配置](https://docs.microsoft.com/azure/information-protection/configure-policy#how-to-configure-the-azure-information-protection-policy)，确保备份数据和保存到其他位置。

有关当前可用的自助服务注册产品和服务的详细信息，请参阅[自助服务的可用程序](https://docs.microsoft.com/office365/admin/misc/self-service-sign-up?view=o365-worldwide#available-self-service-programs)。

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 Office 365 数据保留期和订阅生命周期的详细信息，请参阅 [时会发生什么情况对我的数据和访问 Office 365 商业版订阅结束？](https://docs.microsoft.com/office365/admin/subscriptions-and-billing/what-if-my-subscription-expires?view=o365-worldwide)。

产品状态 | 数据 | 对数据的访问
------------- | ---- | --------------
活动（30 天试用） | 所有用户均可访问数据 | 用户可以正常访问自助服务注册产品、 文件或应用程序<br>管理员可以正常访问 Microsoft 365 管理中心和资源
Deleted | 删除的数据 | 用户不能访问自助服务注册产品、 文件或应用程序<br>管理员可以访问 Microsoft 365 管理中心内购买和管理其他订阅

## <a name="how-can-i-delete-a-self-service-sign-up-product-in-the-azure-portal"></a>如何删除在 Azure 门户中的自助服务注册产品？

您可以将 Microsoft Power BI 或 Azure Rights Management Services 到等自助服务注册产品**删除**状态，以在 Azure AD 门户中立即删除。

1. 登录到 [Azure AD 管理中心](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 组织中的全局管理员的帐户。 如果想要删除"Contoso"初始默认域 contoso.onmicrosoft.com 的目录，登录，使用如admin@contoso.onmicrosoft.com。

2. 选择**许可证**，然后选择 **自助服务注册产品**。 您可以看到所有自助服务注册中的产品单独基于席位的订阅。 选择你想要永久删除的产品。 下面是在 Microsoft Power BI 中的示例：

    ![键入了错误或找不到用户名](./media/directory-delete-howto/licenses-page.png)

3. 选择 **删除** 若要删除的产品并接受条款删除数据后，立即且不可撤销。 此删除操作将删除所有用户，并都删除对该产品的组织访问权限。 单击是以继续进行删除。  

    ![键入了错误或找不到用户名](./media/directory-delete-howto/delete-product.png)

4. 当选择**是**，将启动自助服务产品的删除操作。 没有将告诉您删除正在进行中的通知。  

    ![键入了错误或找不到用户名](./media/directory-delete-howto/progress-message.png)

5. 现在，自助服务注册产品状态已更改为**已删除**。 刷新页面时，应从删除产品**自助服务注册产品**页。  

    ![键入了错误或找不到用户名](./media/directory-delete-howto/product-deleted.png)

6. 后已删除所有产品，可以返回到 Azure AD 管理员中心再次注册，并且应该没有所需的操作和阻止目录删除任何产品。 您应能够成功地删除你的 Azure AD 目录。

    ![键入了错误或找不到用户名](./media/directory-delete-howto/delete-organization.png)

## <a name="next-steps"></a>后续步骤

[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory/)
