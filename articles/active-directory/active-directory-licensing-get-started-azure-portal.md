---
title: "Azure Active Directory 中的许可入门 | Microsoft Docs"
description: "Azure Active Directory 许可的工作原理，如何按照最佳做法开始使用该许可。"
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
ms.date: 07/26/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>在 Azure Active Directory 中向自己及用户发放许可

> [!div class="op_single_selector"]
> * [Azure 门户说明](active-directory-licensing-get-started-azure-portal.md)
> * [获得 Azure 经典门户信息](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) 是 Microsoft 的标识即服务 (IDaaS) 解决方案与平台。 有多种 Azure AD 服务版本：

* Azure Active Directory Free，可通过任何 Microsoft 服务获取，例如 Office 365、Dynamics、Microsoft Intune 或 Azure。 在此模式下，Azure AD 不产生任何使用费。

* Azure AD 付费版本，如：
  - 企业移动性 + 安全性 
  - Azure AD Premium（P1 和 P2）
  - Azure AD Basic
  - Azure 多重身份验证

与许多 Microsoft 联机服务（如 Office 365、Microsoft Intune 和 Azure AD）一样，大多数 Azure AD 付费版都是通过向每位用户授权来提供。 在这些情况下，服务购买是以一个或多个订阅来表示的，在租户中每个订阅包含一些预先购买的许可证。 用户权利获得方式（按用户）：

* 分配许可证。 
* 创建用户与产品之间的链接。
* 为用户启用服务组件。
* 使用一个预付费的许可证。

[立即试用 Azure AD Premium。](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

有关 Azure AD 服务功能的一般概述，请参阅[什么是 Azure AD？](active-directory-whatis.md)。 有关详细信息，请参阅[服务级别协议页面](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/)。

> [!NOTE]
> 通过使用 Azure 即用即付订阅，可创建 Azure 资源，然后将其映射到付款方式。 不存在与订阅关联的许可证计数。 如果向用户授予在映射到订阅的 Azure 资源上进行操作的权限，则用户便与订阅相关联，并可以管理订阅资源。

## <a name="how-does-azure-active-directory-licensing-work"></a>Azure Active Directory 许可的工作原理。

基于许可证的 Azure AD 服务的工作原理是在 Azure AD 服务租户中激活订阅。 激活订阅之后，服务功能由 Azure AD 管理员管理，并由获得许可的用户使用。

### <a name="manage-subscription-information"></a>管理订阅信息

当购买企业移动性 + 安全性、Azure AD Premium 或 Azure AD Basic 时，租户会随着订阅更新，包括其有效期和预付许可证。 可以在 Azure 门户中的 Azure Active Directory 下，打开特定目录的“许可证”磁贴查看订阅信息，包括已分配或可用的许可证数目。 “许可证”边栏选项卡也是管理许可证分配的最佳场所。

每个订阅都包含一个或多个服务计划，例如：Azure AD、多重身份验证、Intune、Exchange Online 或 SharePoint Online。  Azure AD 许可证管理*不*需要服务计划级别管理。 这与 Office 365 不同，Office 365 依赖于此高级配置模式来管理对包含的服务的访问。 Azure AD 则依赖于服务配置来启用功能以及管理单个权限。

> [!IMPORTANT]
> Azure AD Premium、Azure AD Basic 和企业移动性 + 安全性订阅局限于其预配的目录/租户。 订阅不能在目录之间拆分，也不能用于向其他目录中的用户授权。 可以在目录之间移动订阅，但是需要提交支持票证，如果订阅是直接购买的，则需要取消订阅并重新购买。
>
> 若通过批量许可订阅购买 Azure AD 或企业移动性 + 安全性，以及如果协议包含其他 Microsoft 联机服务（例如 Office 365），将自动激活订阅。 

### <a name="assign-licenses"></a>分配许可证

只要获取了订阅，就能配置付费功能，但是，要使用 Azure AD 付费功能，就必须将许可证分发给用户。 必须向应有权访问 Azure AD 付费功能或者通过 Azure AD 付费功能管理的用户分配许可证。 许可证分配是用户与购买的服务（例如 Azure AD Premium、Basic 或企业移动性 + 安全性）之间的映射。


可通过以下方式就目录中哪些用户应拥有许可证进行管理： 

* 在 [Azure 门户](active-directory-licensing-whatis-azure-portal.md)中为各组分配许可证。
* 可通过门户、PowerShell 或 API 直接给用户分配许可证。 

将许可证分配给组时，将向所有组成员分配许可证。 如果在组中添加或删除用户，则会相应地为其分配许可证，或删除其许可证。 组分配可以使用任何可用的组管理功能，而且其与基于组的应用程序分配一致。

可使用[基于组的许可证分配](active-directory-licensing-whatis-azure-portal.md)设置规则，如下所示：
* 目录中的所有用户会自动获取许可证
* 具有相应职务的每个人都会获得许可证
* 可通过使用[自助服务组](active-directory-accessmanagement-self-service-group-management.md)，将决策权委派给组织中的其他管理者

有关将许可证分配到组的详细介绍（包括高级方案和 Office 365 许可方案），请参阅[在 Azure Active Directory 中按组成员身份为用户分配许可证](active-directory-licensing-group-assignment-azure-portal.md)。

## <a name="get-started-with-azure-ad-licensing"></a>Azure AD 许可入门

Azure AD 入门较为简单。 可始终在注册 [Azure 免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)的过程中创建一个目录。

以下最佳做法有助于确保租户与你可能正在使用的其他 Microsoft 服务保持一致，同时与你的服务目标保持一致：

- 如果正在通过 Microsoft 使用任何一种组织服务，则意味着已经获得了一个 Azure AD 租户。 应为其他服务使用同一租户，以便可以跨服务使用核心标识管理，包括预配和混合单一登录 (SSO)。 使用单一登录，用户可跨各服务享受丰富功能。 因此，如果决定为员工购买 Azure AD 付费服务，建议使用相同的租户。

- 如果计划执行以下操作，建议在 Azure 门户中使用新租户：
  - 为另一组用户使用 Azure AD（例如合作伙伴或客户）。
  - 将 Azure AD 服务独立（与生产服务分开）进行评估。
  - 为服务设置一个沙盒环境。

  可使用自己的帐户，以具有全局管理员权限的外部用户身份创建新目录。 使用此帐户登录 Azure 门户时，可看到此租户，并可访问所有管理任务。

> [!NOTE]
> Azure AD 支持“来宾用户”，这是 Azure AD 租户中通过使用 Microsoft 帐户或通过另一个租户的 Azure AD 标识所创建的用户帐户。 Office 365 管理门户目前不支持这些用户。 使用 Microsoft 帐户的来宾用户完全无法访问 Office 365 管理门户，而来自其他 Azure AD 租户的来宾用户将被忽略。 在后一种情况下，仅用户的本地帐户（最初创建用户的 Azure AD 或 Office 365 租户）可访问。

### <a name="select-one-or-more-license-trials"></a>选择一个或多个许可证试用版

可以在 Azure Active Directory &gt;“快速入门”下，激活 Azure AD Premium 或企业移动性 + 安全性试用版订阅。

![选择一个许可证试用版](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

“许可证”边栏选项卡上提供试用版许可证。

### <a name="assign-licenses-to-users-and-groups"></a>向用户和组分配许可证

订阅激活之后，应为自己分配许可证。 然后刷新浏览器以确保能看到所有功能。 下一步是向需要访问 Azure AD 付费功能的用户分配许可证。 如[分配许可证](#assign-licenses)中所述，分配许可证的一个简单方法是识别代表目标受众的组并为其分配许可证。 通过此方法，在组的生命周期内，将相应向添加至组中或从组中删除的用户分配许可证或删除其许可证。

> [!NOTE]
> 某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 Azure 门户中的“用户”&gt;“配置文件”&gt;下的“设置”中设置此属性。 使用组许可证分配时，未指定使用位置的任何用户将继承该目录的位置。

要分配许可证，可在“Azure Active Directory”&gt;“许可证”&gt;“所有产品”下，选择一个或多个产品，然后选择命令栏上的“分配”。

![选择一个要分配的许可证](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

可使用“用户和组”边栏选项卡选择多个用户或组，或在产品中禁用服务计划。 使用顶部的搜索栏搜索用户和组名称。

![选择用于许可证分配的用户或组](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

将许可证分配给组时，可能需要一些时间待所有用户均继承该许可证，具体等待时间取决于组中的用户数。 可在“许可证”磁贴下的“组”边栏选项卡上，查看处理状态。

![许可证分配状态](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

在 Azure AD 许可证分配期间，可能会发生分配错误，但在管理 Azure AD 和企业移动性 + 安全性产品时，这种情况相对很少见。 潜在的分配错误仅限于：
- 分配冲突：以前为用户分配的许可证与当前许可证不兼容。 在此情况下，若要分配新的许可证，必须先删除当前许可证。
- 超过可用许可证数目：分配组中的用户数目超过可用的许可证数目时，用户的分配状态将反映因缺少许可证而发生分配失败。

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure AD B2B 协作授权

通过 B2B 协作，可将来宾用户邀请至自己的 Azure AD 租户，以提供 Azure AD 服务及任何设为可用资源的 Azure 资源的访问权限。  

邀请 B2B 用户并将其分配到 Azure AD 中的应用程序不会产生费用。 B2B 协作用户还可免费使用最多 10 个应用（每个来宾用户）和 3 个基本报表。 如果来宾用户在合作伙伴的 Azure AD 租户中具有相应的许可证，那么他们也将在你的租户中获得相应许可。

这不是必需的，但如果要提供 Azure AD 付费功能的访问权限，这些 B2B 来宾用户必须使用相应的 Azure AD 许可证来获得许可。 一个具有 Azure AD 付费许可证的邀请方租户可向其他五个受邀加入租户的来宾用户分配 B2B 协作用户权限。 如需相关方案和信息，请参阅 [B2B 协作许可指南](active-directory-b2b-licensing.md)。

### <a name="view-assigned-licenses"></a>查看已分配的许可证

已分配和可用的许可证摘要视图显示在“Azure Active Directory”&gt;“许可证”&gt;“所有产品”下。

![查看许可证摘要](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

选择特定产品时，会提供已分配的用户和组的详细列表。 “许可用户”列表显示所有当前正在使用许可证的用户，并显示出直接向其分配许可证的用户以及从组继承许可证的用户。

![查看许可证详细信息](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

同样，“许可组”列表显示所有已向其分配许可证的组。 选择一个用户或组以打开“许可证”边栏选项卡，该选项卡显示分配给该对象的所有许可证。

### <a name="remove-a-license"></a>删除许可证

若要删除许可证，请转到用户或组，打开“许可证”磁贴。 选择许可证，然后单击“删除”。

![删除许可证](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

无法直接删除用户从组继承的许可证。 需要从继承许可证的组删除该用户。

### <a name="extend-trials"></a>延长试用期

可以通过 Office 365 门户自助注册延长客户试用期限。 客户管理员可以导航到 Office 门户（访问权限取决于对 Office 门户的权限），然后选择“Azure AD Premium 试用版”。 单击“延长试用期”链接将开始延期。 需要信用卡，但无需支付费用。

![在 Azure 门户中延长试用期](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>后续步骤

若要详细了解通过组管理许可证的高级方案，请阅读[为组分配许可证](active-directory-licensing-group-assignment-azure-portal.md)。

下面是有关如何配置和使用 Azure AD 其他付费功能的信息：

* [自助密码重置](active-directory-manage-passwords.md)
* [自助组管理](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md)
* [直接购买 Azure AD Premium 许可证](http://aka.ms/buyaadp)

