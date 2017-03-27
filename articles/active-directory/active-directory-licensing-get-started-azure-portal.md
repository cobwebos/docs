---

title: "Azure Active Directory 预览版中的许可入门 | Microsoft 文档"
description: "介绍 Azure Active Directory 许可及其工作原理、入门方法和最佳做法，涉及的产品包括 Office 365、Microsoft Intune 及 Azure Active Directory Premium 和 Basic 版本"
services: active-directory
keywords: "Azure AD 许可"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 3bad24f35cf7867f1e02e8470c602a7f6a9ce8fb
ms.lasthandoff: 03/09/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory-preview"></a>向你自己以及 Azure Active Directory 预览版中你的用户进行许可

> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-licensing-get-started-azure-portal.md)
> * [Azure 经典门户](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) 是 Microsoft 的标识即服务 (IDaaS) 解决方案与平台。 Azure AD 提供许多功能与技术版本，从可配合任何 Microsoft 服务（例如 Office 365、Dynamics、Microsoft Intune 和 Azure）使用的 Azure AD 免费版（Azure AD 在此模式中不产生任何使用费），到许多 Azure AD 付费版，例如 Enterprise Mobility Suite (EMS)、Azure AD Premium（P1 和 P2）和 Azure AD Basic，以及 Azure 多重身份验证 (MFA)。 与许多 Microsoft 联机服务（如 Office 365、Microsoft Intune 和 Azure AD）一样，大多数 Azure AD 付费版都是通过向每位用户授权来提供。 在这些情况下，购买的服务以一个或多个订阅来表示，每个订阅在租户中包含预先购买数目的许可证。 基于用户的权利是通过许可证分配、在用户与产品之间创建关联、为用户启用服务组件并占用一个预付费许可证来实现的。

[立即试用 Azure AD 高级版](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)。

有关 Azure AD 服务功能的一般概述，请参阅[什么是 Azure AD](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/)。 有关详细信息，请参阅服务级别协议页面。

> [!NOTE]
> Azure 即付即用订阅则不相同：尽管也在目录中显示，但是这些订阅可以创建 Azure 资源并将资源映射到付款方式。 在这种情况下，不存在与订阅关联的许可证计数。 用户与订阅的关联就是用户管理订阅资源的访问权限，是通过授予用户权限，使他们能够操作已映射到订阅的 Azure 资源来实现的。

## <a name="how-does-azure-ad-licensing-work"></a>Azure AD 许可的工作原理

基于许可证的 Azure AD 服务的工作原理是在 Azure AD 目录/服务租户中激活订阅。 一旦激活订阅，服务功能就可以由目录/服务管理员管理，并可由许可的用户使用。

当购买或激活 Enterprise Mobility Suite、Azure AD Premium 或 Azure AD Basic 时，目录会随着订阅更新，包括其有效期和预付许可证。 可以在 Azure 门户中特定目录的 Azure Active Directory &gt;“许可证”磁贴下查看订阅信息，包括已分配或可用的许可证数目。 这也是管理许可证分配的最佳位置。

每个订阅都包含一个或多个服务计划，每个计划映射服务类型（例如，Azure AD、Azure MFA、Microsoft Intune、Exchange Online 或 SharePoint Online）包含的功能级别。  Azure AD 许可证管理不需要服务计划级别管理。 这与 Office 365 不同，Office 365 依赖于此高级配置模式来管理对包含的服务的访问。 Azure AD 则依赖于服务配置来启用功能和管理单个权限。

> [!IMPORTANT]
> Azure AD Premium 和 Basic 以及 Enterprise Mobility Suite 订阅局限于其预配的目录/租户。 订阅不能在目录之间拆分，也不能用于向其他目录中的用户授权。 可以在目录之间移动订阅，但是需要提交支持票证，如果订阅是直接购买的，则需要取消并重新购买。
>
> 通过批量许可购买 Azure AD 或 Enterprise Mobility Suite 时，如果协议包含其他 Microsoft Online 服务（例如 Office 365），将自动激活订阅。

### <a name="assigning-licenses"></a>分配许可证

只要获取了订阅，就能配置付费功能；但是，要使用 Azure AD 付费功能，就必须将许可证分发给适当的个人。 一般而言，应访问 Azure AD 付费功能或者通过 Azure AD 付费功能管理的人员都必须被分配许可证。 许可证分配是用户与购买的服务（例如 Azure AD Premium、Basic 或 Enterprise Mobility Suite）之间的映射。

管理目录中哪些用户应该拥有许可证很简单。 只要通过在 Azure 门户中将许可证分配给组，或者通过门户、PowerShell 或 API 直接将许可证分配给适当的人员即可。 将许可证分配给组时，将对所有组成员分配许可证。 如果在组中添加或删除用户，则会相应地分配或删除其许可证。 组分配可以使用可用的任何组管理功能，而且与基于组的应用程序分配一致。 使用这种方法，你可以设置规则以便为目录中所有用户进行自动分配，确保有适当职务的每个人都获得许可证，甚至委派决策权给组织中的其他管理人员。 

有关将许可证分配到组的详细介绍（包括高级方案和 Office 365 许可方案），请参阅[此文](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-assignment-azure-portal)。

## <a name="getting-started-with-azure-ad-licensing"></a>Azure AD 许可入门

Azure AD 很容易入门；始终可以在注册免费 Azure 试用版过程中创建目录。 [了解有关以组织身份注册的详细信息](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/)。 以下内容可帮助你确保目录最适合你可能要使用或打算使用的其他 Microsoft 服务，以及获取服务的目标。

以下是几项最佳实践：

- 如果你已在使用 Microsoft 的任何组织服务，则已经获得了一个 Azure AD 目录。 在此情况下，应该为其他服务继续使用相同的目录，以便可以跨服务使用核心标识管理，包括预配和混合单一登录 (SSO)。 用户将获得单一登录体验，并且可以受益于更丰富的跨服务功能。 因此，如果决定为员工购买 Azure AD 付费服务，建议使用相同的目录。

- 如果计划为一组不同的用户（合作伙伴、客户等）使用 Azure AD，或者想要评估 Azure AD 服务，并想要隔离生产服务，或者想要为服务设置沙盒环境，则建议先通过 Azure 经典门户创建新的目录。 详细了解如何在 Azure 经典门户中创建新的 Azure AD 目录。 你可以使用自己的帐户，以具有全局管理员权限的外部用户身份创建新目录。 使用此帐户登录 Azure 门户时，将会看到此目录，并可访问所有目录管理任务。

> [!NOTE]
> Azure AD 支持“外部用户”，这是在 Azure AD 实例中使用 Microsoft 帐户 (MSA) 或另一个目录中的 Azure AD 标识所创建的用户帐户。 尽管我们正在努力将此功能扩展到所有 Microsoft 的组织服务，但目前某些服务体验不支持这些帐户；例如，Office 365 系统管理门户目前不支持这些用户。 因此，使用 Microsoft 帐户的外部用户将无法访问 Office 365 系统管理门户，而来自其他 Azure AD 目录的外部用户将被忽略。 在后一种情况下，只有用户的本地帐户（最初创建用户的 Azure AD 或 Office 365 目录）才能通过这些体验进行访问。

如前所述，Azure AD 有不同的付费版本。 这些版本在其可购性方面略有不同：

|  产品       |              EA/VL  | 打开  | CSP |  MPN 使用权限  | 直接购买 |  试用 |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | X   |    X  |    X  |     X | &nbsp;  | X |
|  Azure AD Premium P2     |    X    |   X   |   X   | &nbsp;  |  X  |   X  
|  Azure AD Premium P1     |    X   |    X    |  X   |  &nbsp; |  X  |  &nbsp; |             
|  Azure AD Basic          |    X   |    X   |  X  |  X  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>选择一个或多个许可证试用版

可以在 Azure Active Directory &gt;“快速启动”下，激活 Azure AD Premium 或企业移动性套件试用版订阅。

![选择一个许可证试用版](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

“许可证”边栏选项卡上提供试用版许可证。

### <a name="assign-licenses-to-users-and-groups"></a>向用户和组分配许可证

激活订阅后，应该分配一个许可证给自己，然后刷新浏览器，确保可以看到所有功能。 下一步是将许可证分配给需要访问或要包含在 Azure AD 付费功能中的用户。 如前面在 [分配许可证](#assigning-licenses) 中所述，执行这项操作的最好方法是识别代表目标对象的组，并将许可证分配给该组；这样，在组的生命周期内添加到组或从组中删除的用户将分别被分配许可证或删除许可证。

> [!NOTE]
> 某些 Microsoft 服务并非在所有位置可用；将许可证分配给用户之前，管理员必须对该用户指定“使用位置”属性。 可在 Azure 门户中的“用户”&gt;“配置文件”&gt;“设置”部分执行此操作。 使用组许可证分配时，任何没有指定使用位置的用户将继承该目录的位置。

若要将许可证分配到组或各个用户，请在“Azure Active Directory”&gt;“许可证”&gt;“所有产品”下，选择一个或多个产品，然后单击命令栏上的“分配”按钮。

![选择一个要分配的许可证](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

这将显示一个新边栏选项卡，可在其中选择多个用户或组并选择性地禁用产品中的服务计划。 顶部的搜索栏可用于搜索用户和组名称。

![选择用于许可证分配的用户或组](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

将许可证分配给组时，可能需要一些时间待所有用户均继承许可证，具体取决于组中的用户数。 可在“许可证”磁贴下的“组”边栏选项卡上，查看处理状态。

![许可证分配状态](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

在 Azure AD 许可证分配期间，可能会发生分配错误，但在管理 Azure AD 和 EMS 产品时，这种情况相对很少见。 潜在的分配错误仅限于：
- 分配冲突：以前为用户分配的许可证与当前许可证不兼容。 在此情况下，若要分配新的许可证，必须先删除当前许可证。
- 超过可用的许可证数目 - 当分配组中的用户数目超过可用的许可证数目时，用户的分配状态将反映因缺少许可证而发生的分配失败。

本文中提供了有关组许可证分配的详细信息。

### <a name="view-assigned-licenses"></a>查看已分配的许可证

已分配和可用的许可证摘要视图显示在“Azure Active Directory”&gt;“许可证”&gt;“所有产品”下。

![查看许可证摘要](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

单击特定产品时，会提供已分配的用户和组的详细列表。 **许可用户**显示所有当前正在使用许可证的用户，包括直接向其分配许可证的用户以及从组继承许可证的用户。

![查看许可证详细信息](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

同样，**许可组**显示所有已向其分配许可证的组。 单击这些视图中的用户或组将打开“许可证”边栏选项卡，其中显示分配给该对象的所有许可证。

### <a name="removing-a-license"></a>删除许可证

若要删除许可证，请转到用户或组，打开“许可证”磁贴。 选择许可证，然后单击“删除”。

![删除许可证](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

请注意，无法直接删除用户从组继承的许可证。 需要从继承许可证的组删除该用户。

### <a name="extending-trials"></a>延期试用

通过 Office 365 门户可以自助延长客户试用期限。 客户管理员可以导航到 Office 门户（访问权限取决于对 Office 门户的权限），然后选择“Azure AD Premium 试用版”。 单击“延长试用期”链接将开始延期。 需要信用卡，但无需支付费用。

![在 Azure 门户中延长试用期](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>后续步骤

若要详细了解通过组管理许可证的高级方案，请阅读此文

现在，你可能已准备好配置和使用某些 Azure AD Premium 功能。

* [自助密码重置](active-directory-manage-passwords.md)
* [自助组管理](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Heath](active-directory-aadconnect-health.md)
* [将组分配到应用程序](active-directory-manage-groups.md)
* [将许可证分配到组](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md)
* [直接购买 Azure AD Premium 许可证](http://aka.ms/buyaadp)

