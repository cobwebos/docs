---
title: 如何在 Microsoft 合作伙伴中心管理商业市场帐户
description: 了解如何在 Microsoft 合作伙伴中心管理商业市场帐户。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 08/27/2020
ms.openlocfilehash: a2d2c4d29a6af073e3e4e6a74c257cb864b8a78c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400683"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>在合作伙伴中心管理商业市场帐户

[创建合作伙伴中心帐户](./create-account.md)后，就可以使用[商业市场仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)管理帐户和产品/服务。

本文将深入介绍如何管理合作伙伴中心帐户，包括如何：

- [访问合作伙伴中心帐户设置](#access-your-account-settings)
- [查找发布者 ID、Symantec ID、卖家 ID、用户 ID、MPN ID 和 Azure AD 租户](#account-details)
- [更新联系人信息](#contact-info)
- [设置用于监视客户使用情况的跟踪 GUID](#tracking-guids)
- [管理用户](#manage-users)
- [管理组](#manage-groups)
- [管理 Azure AD 应用程序](#manage-azure-ad-applications)
- [定义用户角色和权限](#define-user-roles-and-permissions)
- [管理 Azure AD 租户（工作帐户）](#manage-tenants)
- [管理合作伙伴中心协议](#agreements)

## <a name="access-your-account-settings"></a>访问你的帐户设置

如果尚未执行此操作，你（或组织的管理员）应访问合作伙伴中心帐户的[帐户设置](https://partner.microsoft.com/dashboard/account/management)，以便：
- 检查公司的帐户验证状态
- 确认 Symantec ID、卖家 ID、Microsoft 合作伙伴网络 (MPN) ID、发布者 ID 和联系人信息，包括公司审批者和卖家联系人
- 为将在合作伙伴中心使用你的业务帐户的任何人创建用户帐户

### <a name="open-developer-settings"></a>打开开发人员设置

帐户设置位于合作伙伴中心的[商业市场仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace)的右上角。 选择齿轮图标（位于仪表板的右上角附近），然后选择“开发人员设置” **** 。

![合作伙伴中心的“帐户设置”菜单](./media/dashboard-developer-settings.png)

在“帐户设置”中，你将能够查看：
- 帐户详细信息：帐户类型和帐户状态
- 发布者 ID：卖家 ID、用户 ID、发布者 ID、Azure AD 租户等。
- 联系人信息：发布者显示名称、卖家联系人姓名、电子邮件、电话和地址
- 跟踪 GUID：任何跟踪 GUID 都与你的帐户关联

### <a name="account-details"></a>帐户详细信息

在“帐户详细信息”部分中，可以看到基本信息，如“帐户类型”（公司或个人）和帐户的“验证状态” 。 在帐户验证过程中，这些设置将显示所需的每个步骤，包括电子邮件验证、雇用验证和业务验证。 还可以在此处更新电子邮件，并根据需要重新发送验证。

### <a name="publisher-ids"></a>发布者 ID

在“发布者 ID”部分中，可以看到“Symantec ID”、“卖家 ID”、“用户 ID”、“MPN ID”和“Azure AD 租户”    。 这些值由 Microsoft 分配，用于唯一标识你的开发人员帐户，但不能进行编辑。

### <a name="contact-info"></a>联系信息

在“联系人信息”部分中，可以看到“发布者显示名称”、“卖家联系人信息”（公司卖家的联系人姓名、电子邮件、电话号码和地址）和“公司审批者”（有权审批公司决策的个人的姓名、电子邮件地址和电话号码）  。

#### <a name="payout-account"></a>付款帐户

付款帐户是将销售收款发送到的银行帐户。 此银行帐户必须位于注册合作伙伴中心帐户所在的国家/地区。

若要设置付款帐户，请执行以下操作：

1. 转到合作伙伴中心内的[商业市场概述页](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。
2. 在“配置文件”部分下的“付款配置文件”旁边，选择“更新” 。
3. 选择付款方式：银行帐户或 PayPal。
4. 添加付款信息：这可能包括选择帐户类型（支票或储蓄）、输入帐户持有者姓名、帐号和银行代号、帐单邮寄地址、电话号码或 PayPal 电子邮件地址。 *有关使用 PayPal 作为帐户付款方式，以及是否在你的市场区域中支持该付款方式的详细信息，请参阅 [PayPal 信息](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)。

> [!IMPORTANT]
> 更改付款帐户可能会推迟付款（最多一个付款周期）。 出现此推迟的原因是我们需要验证帐户更改，正如首次设置付款帐户时我们会进行验证。 帐户通过验证后，仍会收到全额款项；当前付款周期应得的任何付款将添加到下一个付款周期。  

#### <a name="tax-profile"></a>税务配置文件

查看当前的税务个人资料状态，确认是否显示正确的实体类型和税务证书信息 。 选择“编辑”以更新或填写任何所需的表单。

若要建立税务状态，必须指定你的居住地和国籍所属的国家/地区，并填写与你所在国家/地区关联的相应税务表单。

不管你的居住地或国籍属于哪个国家/地区，都必须填写美国税务表单才能通过 Microsoft 销售任何产品/服务。 满足特定美国居留要求的合作伙伴必须填写 IRS W-9 表单。 美国之外的其他合作伙伴必须填写 IRS W-8 表单。 完成你的税务个人资料后，即可在线填写这些表单。

无需美国个人纳税人标识号（即 ITIN），即可从 Microsoft 接收付款或申报税务协定权益。

可以在合作伙伴中心以电子方式完成和提交你的税务表单；在大多数情况下，无需打印和邮寄任何表单。

不同的国家和地区具有不同的税务要求。 应付税款的确切金额取决于销售产品/服务的国家和地区。 在某些国家/地区中，Microsoft 代表你缴纳销售和使用税。 这些国家/地区将在列出产品/服务的过程中确定。 在其他国家/地区中，根据你的注册位置，可能需要对销售的产品/服务缴纳销售和使用税，并直接支付给本地税收机构。 此外，你收到的销售收款还可以作为收入进行纳税。 我们强烈建议联系你所在国家或地区最能帮助你确定 Microsoft 销售交易的正确税务信息的相关机构。

##### <a name="withholding-rates"></a>预扣税率

在纳税表单中提交的信息可确定相应的预扣税率。 预扣税率仅适用于在美国进行的销售；在非美国位置进行的销售不受预扣税率的限制。 预扣税率变化多端，但对于大多数在美国之外注册的开发人员来说，默认预扣税率为 30%。 如果你所在的国家/地区与美国达成所得税协定，则可以选择降低此预扣税率。

##### <a name="tax-treaty-benefits"></a>税务协定权益

如果你不在美国，则可以利用税务协定权益。 这些权益因国家/地区而异，并且可让你减少 Microsoft 预扣的税金。 可以通过填写 W-8BEN 表单的第 II 部分来申报税务协定权益。 建议你与你所在国家或地区的相应资源通信，以确定这些权益是否适合你。

[详细了解 Windows 应用/游戏开发人员和 Azure 市场发布者的税务详细信息](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)。

#### <a name="payout-hold-status"></a>付款暂停状态

默认情况下，Microsoft 每月发送付款。 但是，可以选择将付款置于保持状态，这将阻止向帐户发送付款。 如果选择暂停付款，我们将继续记录你所获得的任何收入，并提供“付款摘要”中的详细信息。 但是，在删除暂停状态之前，我们不会向你的帐户发送任何付款。

若要暂停付款，请转到“帐户设置”。 在“财务详细信息”下的“付款暂停状态”部分中，将滑块切换为“打开”  。 可以随时更改付款暂停状态，但请注意，你的决策将影响下一次每月付款。 例如，如果要暂停四月付款，请确保在三月底之前将付款暂停状态设置为“打开”。

将付款暂停状态设置为“打开”后，所有付款都将处于暂停状态，直到将滑块切换回“关闭” 。 执行此操作时，将在下一次每月付款周期中生效（前提是已满足任何适用的付款阈值）。 例如，如果已暂停付款，但想要在六月生成付款，请确保在五月底之前将付款暂停状态切换为“关闭”。

> [!NOTE]
> “付款暂停状态”选择适用于通过 Microsoft 合作伙伴中心付费的所有收入来源，包括 Azure 市场、AppSource、Microsoft Store、广告等） 。 无法为每个收入来源选择不同的暂停状态。

### <a name="devices"></a>设备

设备管理设置仅适用于 UWP 发布。 [了解详细信息](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)。

### <a name="tracking-guids"></a>跟踪 GUID

全局唯一标识符 (GUID) 是可用于跟踪 Azure 使用情况的唯一参考编号（包含 32 个十六进制数字）。 

若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://aka.ms/StoragePartners)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。

建议为每个产品/服务和每个产品的分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用单个 GUID。

如果使用模板部署产品并且产品在 Azure 市场和 GitHub 中都提供，则可以创建并注册两个不同的 GUID：

- Azure 市场中的产品 A
- GitHub 中的产品 A

报告根据合作伙伴值（Microsoft 合作伙伴 ID）和 GUID 来完成。 还可以在更为精细的级别跟踪 GUID，从而使产品/服务中的每个计划一致。

有关详细信息，请参阅 [使用 Guid 跟踪 Azure 客户使用常见问题](../azure-partner-customer-usage-attribution.md#faq)) 。

## <a name="create-a-billing-profile"></a>创建计费对象信息

如果要发布 [Dynamics 365 for Customer Engagement & Power Apps](./create-new-customer-engagement-offer.md) 或 [Dynamics 365 for Operations](./create-new-operations-offer.md) 产品/服务，则需要填写计费对象信息。

帐单邮寄地址是通过你的法律实体预填充的，可以稍后更新此地址。 “税务 ID”和“增值税 ID”字段是可选的。  无法编辑国家/地区名称和公司名称。

## <a name="multi-user-account-management"></a>多用户帐户管理

合作伙伴中心使用 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 进行多用户帐户访问和管理。 组织的 Azure AD 会在注册过程中自动与合作伙伴中心帐户关联。

## <a name="manage-users"></a>管理用户

合作伙伴中心的“用户”部分（在“帐户设置”下）允许你使用 Azure AD 来管理有权访问合作伙伴中心帐户的用户、组和 Azure AD 应用程序 。 你的帐户必须对要在其中添加或编辑用户的[工作帐户（Azure AD 租户）](./company-work-accounts.md)具有[管理员级别](#define-user-roles-and-permissions)权限。 若要在不同的工作帐户/租户中管理用户，则将需要注销，然后以具有管理员权限的用户身份重新登录该工作帐户/租户。

使用工作帐户（Azure AD 租户）登录后，即可：

- [添加或删除用户](#add-existing-users)
- [更改用户密码](#change-a-user-password)
- [添加或删除组](#manage-groups)
- [添加或删除 Azure AD 应用程序](#add-new-azure-ad-applications)
- [管理 Azure AD 应用程序的密钥](#manage-keys-for-an-azure-ad-application)
- [定义用户角色和权限](#define-user-roles-and-permissions)

请记住，所有合作伙伴中心用户（包括组和 Azure AD 应用程序）必须在与合作伙伴中心帐户关联的 [Azure AD 租户](#manage-tenants)中具有有效的工作帐户。

### <a name="add-existing-users"></a>添加现有用户

若要将用户添加到已存在于你公司的[工作帐户（Azure AD 租户）](./company-work-accounts.md)中的合作伙伴中心帐户，请执行以下操作：

1. 转到“用户”（在“帐户设置”下），然后选择“添加用户”  。
2. 从显示的列表中选择一个或多个用户。 可以使用搜索框搜索特定用户。
*如果选择多个用户添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个用户，请对每个角色或自定义权限集重复这些步骤。
3. 选择 "用户" 后，选择 " **添加**"。
4. 在“角色”部分中，为所选用户指定角色或自定义权限。
5. 选择“保存”。

### <a name="create-new-users"></a>创建新用户

若要创建全新的用户帐户，必须创建具有[全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)权限的帐户。

1. 转到“用户”（在“帐户设置”下），选择“添加用户”，然后选择“创建新用户”   。
1. 输入每个新用户的名字、姓氏和用户名。 
1. 如果希望新用户在组织的目录中具有全局管理员帐户，请选中标记为“让此用户成为你 Azure AD 中的全局管理员，并完全控制所有目录资源”的框。 这将向用户授予对公司的 Azure AD 中的所有管理功能的完全访问权限。 他们将能够在组织的工作帐户（Azure AD 租户）中添加和管理用户，但在合作伙伴中心内不会，除非你向帐户授予适当的角色/权限。
1. 如果选中此框以让此用户成为全局管理员，则需要提供密码恢复电子邮件地址，以便用户在必要时恢复其密码 。
1. 在“组成员身份”部分中，选择希望新用户所属的任何组。
1. 在“角色”部分中，为用户指定角色或自定义权限。
1. 选择“保存”。

在合作伙伴中心创建新用户还会在你登录到的工作帐户（Azure AD 租户）中为该用户创建帐户。 在合作伙伴中心更改用户的姓名将在组织的工作帐户（Azure AD 租户）中做出相同的更改。

### <a name="invite-new-users-by-email"></a>通过电子邮件邀请新用户

若要通过电子邮件邀请当前不是公司工作帐户（Azure AD 租户）的一部分的用户，必须创建具有[全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)权限的帐户。

1. 转到“用户”（在“帐户设置”下），选择“添加用户”，然后选择“通过电子邮件邀请用户”   。
2. 输入一个或多个电子邮件地址，最多 (10 个) ，用逗号或分号分隔。
3. 在“角色”部分中，为用户指定角色或自定义权限。
4. 选择“保存”。

你邀请的用户将收到加入合作伙伴中心帐户的电子邮件邀请。 将在工作帐户（Azure AD 租户）中创建新的来宾用户帐户。 每个用户都需要接受邀请，然后才能访问你的帐户。

如果需要重新发送邀请，请访问“用户”页，在用户列表中查找该邀请，选择其电子邮件地址（或显示“邀请挂起”的文本）。 然后，在页面底部，选择“重新发送邀请”。

> [!NOTE]
> 如果你的组织使用[目录集成](https://go.microsoft.com/fwlink/p/?LinkID=724033)将本地目录服务与 Azure AD 同步，则无法在合作伙伴中心创建新用户、组或 Azure AD 应用程序。 你（或本地目录中的其他管理员）将需要直接在本地目录中创建它们，然后才能在合作伙伴中心查看并添加它们。

### <a name="remove-a-user"></a>删除用户

若要从工作帐户（Azure AD 租户）中删除用户，请转到“用户”（在“帐户设置”下），使用最右侧列中的复选框选择要删除的用户，然后从可用操作中选择“删除”  。 将出现一个弹出窗口，供你确认是否要删除所选用户。

### <a name="change-a-user-password"></a>更改用户密码

如果你的其中一个用户需要更改密码，他们可以自行完成此操作（如果在创建用户帐户时提供了密码恢复电子邮件地址）。 还可以通过执行以下步骤来更新用户的密码。 若要更改公司工作帐户（Azure AD 租户）中的用户密码，必须登录具有[全局管理员](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)权限的帐户。 这会更改用户在 Azure AD 租户中的密码，以及用于访问合作伙伴中心的密码。

1. 在“用户”页（在“帐户设置”下）上，选择要编辑的用户帐户的名称 。
2. 选择页面底部的“重置密码”按钮。
3. 将显示一个确认页，其中显示了用户的登录信息，包括临时密码。 请务必打印或复制此信息并将其提供给用户，因为离开此页面后将无法访问临时密码。

## <a name="manage-groups"></a>管理组

你可以通过组同时控制多个用户角色和权限。

### <a name="add-an-existing-group"></a>添加现有组

若要将已存在于组织的工作帐户（Azure AD 租户）中的组添加到合作伙伴中心帐户，请执行以下操作：

1. 在“用户”页（在“帐户设置”下）上，选择“添加组”  。
2. 从显示的列表中选择一个或多个组。 可以使用搜索框搜索特定组。
*如果选择多个组添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个组，请对每个角色或自定义权限集重复这些步骤。
3. 选择完组后，选择 " **添加**"。
4. 在“角色”部分中，为所选组指定角色或自定义权限。 无论与个人帐户关联的角色和权限如何，组的所有成员都将能够访问具有应用于组的权限的合作伙伴中心帐户。
5. 选择“保存”。

添加现有组时，属于该组成员的每个用户都能够访问合作伙伴中心帐户，且具有与组的分配角色关联的权限。

### <a name="add-a-new-group"></a>添加新组

若要将全新的组添加到合作伙伴中心帐户，请执行以下操作：

1. 在“用户”页（在“帐户设置”下）上，选择“添加组”  。
2. 在下一页，选择“新建组”。
3. 输入新组的显示名称。
4. 为组指定角色或自定义权限。 无论与个人帐户关联的角色/权限如何，组的所有成员都将能够访问具有在此处应用的权限的合作伙伴中心帐户。
5. 从显示的列表中选择新组的用户。 可以使用搜索框搜索特定用户。
6. 选择 "用户" 后，选择 " **添加** " 以将其添加到新组。
7. 选择“保存”。

将在组织的工作帐户中创建此新组 (Azure AD 租户) ，而不只是在合作伙伴中心帐户中。

### <a name="remove-a-group"></a>删除组

若要从工作帐户（Azure AD 租户）中删除组，请转到“用户”（在“帐户设置”下），使用最右侧列中的复选框选择要删除的组，然后从可用操作中选择“删除”  。 将出现一个弹出窗口，供你确认是否要删除所选组。

## <a name="manage-azure-ad-applications"></a>管理 Azure AD 应用程序

可以允许属于公司 Azure AD 的应用程序或服务访问合作伙伴中心帐户。

### <a name="add-existing-azure-ad-applications"></a>添加现有的 Azure AD 应用程序

若要添加已存在于公司的 Azure Active Directory 中的应用程序，请执行以下操作：

1. 在“用户”页（在“帐户设置”下）上，选择“添加 Azure AD 应用程序”  。
2. 从显示的列表中选择一个或多个 Azure AD 应用程序。 可以使用搜索框搜索特定 Azure AD 应用程序。 *如果选择多个 Azure AD 应用程序添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个 Azure AD 应用程序，请对每个角色或自定义权限集重复这些步骤。
3. 选择 Azure AD 应用程序后，选择 " **添加**" "选择"。
4. 在“角色”部分中，为所选 Azure AD 应用程序指定角色或自定义权限。
5. 选择“保存”。

### <a name="add-new-azure-ad-applications"></a>添加新的 Azure AD 应用程序

如果要向合作伙伴中心授予对全新 Azure AD 应用程序帐户的访问权限，则可以在“用户”部分中创建一个。 这会在公司工作帐户中创建一个新帐户 (Azure AD 租户) ，而不只是在合作伙伴中心帐户中。 如果你主要使用此 Azure AD 应用程序进行合作伙伴中心身份验证，并且不需要用户直接访问该应用程序，则可以为“回复 URL”和“应用 ID URI”输入任何有效地址，前提是你目录中的任何其他 Azure AD 应用程序都不使用这些值 。

1. 在“用户”页（在“帐户设置”下）上，选择“添加 Azure AD 应用程序”  。
2. 在下一页，选择“新建 Azure AD 应用程序”。
3. 为新的 Azure AD 应用程序输入“回复 URL”。 这是用户可在其中登录并使用 Azure AD 应用程序的 URL（有时也称为“应用 URL”或“登录 URL”）。 “回复 URL”的长度不得超过 256 个字符，并且在目录中必须是唯一的。
4. 为新的 Azure AD 应用程序输入“应用 ID URI”。 这是在向 Azure AD 发送单一登录请求时提供的 Azure AD 应用程序的逻辑标识符。 目录中的每个 Azure AD 应用程序的 **应用程序 ID URI** 必须是唯一的。 此 ID 的长度不得超过 256 个字符。 有关应用程序 ID URI 的详细信息，请参阅将 [应用程序与 Azure Active Directory) 集成](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts) 。
5. 在“角色”部分中，为 Azure AD 应用程序指定角色或自定义权限。
6. 选择“保存”。

添加或创建 Azure AD 应用程序后，可以返回到“用户”部分并选择应用程序名称以查看应用程序的设置，包括“租户 ID”、“客户端 ID”、“回复 URL”和“应用 ID URI”。

### <a name="remove-an-azure-ad-application"></a>删除 Azure AD 应用程序

若要从工作帐户（Azure AD 租户）中删除应用程序，请转到“用户”（在“帐户设置”下），使用最右侧列中的复选框选择要删除的应用程序，然后从可用操作中选择“删除”  。 将出现一个弹出窗口，供你确认是否要删除所选应用程序。

### <a name="manage-keys-for-an-azure-ad-application"></a>管理 Azure AD 应用程序的密钥

如果 Azure AD 应用程序在 Microsoft Azure AD 中读取和写入数据，则将需要密钥。 可以通过在合作伙伴中心编辑 Azure AD 应用程序的信息，为其创建密钥。 还可以删除不再需要的密钥。

1. 在“用户”页（在“帐户设置”下）上，选择 Azure AD 应用程序的名称 。 你将看到 Azure AD 应用程序的所有活动密钥，包括创建密钥的日期和过期时间（50）。
2. 若要删除不再需要的密钥，请选择“删除”。
3. 若要添加新密钥，请选择“添加新密钥”。
4. 你将看到一个屏幕，显示“客户端 ID”和“密钥值” 。 请务必打印或复制此信息，因为离开此页面后将无法再次访问。
5. 如果要创建更多密钥，请选择“添加另一个密钥”。

## <a name="define-user-roles-and-permissions"></a>定义用户角色和权限

公司的用户可以在合作伙伴中心为商业市场计划分配以下角色和权限：

- **所有者**
  - "这是首次创建帐户并拥有其完全访问权限的人员，包括创建和编辑所有帐户用户并更改所有财务和帐户设置的功能。 一个帐户通常只有一个所有者。 "
- 管理员
  - 可以访问除税务和付款设置之外的所有 Microsoft 帐户功能
  - 可以管理用户、角色和工作帐户（租户）
- **开发人员**
  - 可以管理和发布产品/服务
  - 可以查看一些发布服务器报表

> [!NOTE]
> 对于商业市场计划，不使用全局管理员、业务参与者、财务参与者和营销人员角色。 将这些角色分配给用户不起作用。 只有管理员和开发人员角色向用户授予权限。

有关在合作伙伴中心的其他区域（例如 Azure Active Directory (AD)、云解决方案提供商 (CSP)、控制面板供应商 (CPV),、来宾用户或 Microsoft 合作伙伴网络 (MPN)）中管理角色和权限的详细信息，请参阅[在合作伙伴中心分配用户角色和权限](https://docs.microsoft.com/partner-center/permissions-overview)。

## <a name="manage-tenants"></a>管理租户

本文档中的 Azure Active Directory (AD) 租户（也称为“工作帐户”）是 Azure 门户中设置的组织的表示形式，可帮助你为内部和外部用户管理 Microsoft 云服务的特定实例。 如果你的组织已订阅 Microsoft 云服务（如 Azure、Microsoft Intune 或 Microsoft 365），则会为你建立 Azure AD 租户。

可以设置多个用于合作伙伴中心的租户。 具有合作伙伴中心帐户中的管理员角色的任何用户都可以在该帐户中添加和删除 Azure AD 租户。  

### <a name="add-an-existing-tenant"></a>添加现有租户

若要将其他 Azure AD 租户与合作伙伴中心帐户关联，请执行以下操作：

1. 在“租户”页（在“帐户设置”下）上，选择“关联其他 Azure AD 租户”  。
2. 输入要关联的租户的 Azure AD 凭据。
3. 查看 Azure AD 租户的组织名称和域名。 若要完成关联，请选择“确认”。

如果关联成功，即可在合作伙伴中心的“用户”部分中添加和管理帐户用户。

### <a name="create-a-new-tenant"></a>创建新租户

若要使用合作伙伴中心帐户创建全新的 Azure AD 租户，请执行以下操作：

1. 在“租户”页（在“帐户设置”下）上，选择“创建新的 Azure AD 租户”  。
2. 为新的 Azure AD 输入目录信息：
    - **域名**：将用于 Azure AD 域的唯一名称，以及“.onmicrosoft.com”。 例如，如果输入了“example”，则 Azure AD 域为“example.onmicrosoft.com”。
    - 联系人电子邮件：电子邮件地址，我们可以在必要时就帐户相关事宜与你联系。
    - 全局管理员用户帐户信息：要用于新的全局管理员帐户的名字、姓氏、用户名和密码。
3. 选择“创建”以确认新域和帐户信息。
4. 使用新的 Azure AD 全局管理员用户名和密码登录以开始[添加和管理用户](#manage-users)。

有关如何在 Azure 门户中（而不是通过合作伙伴中心门户）创建新租户的详细信息，请参阅[在 Azure Active Directory 中创建新租户](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文。

### <a name="remove-a-tenant"></a>删除租户

若要从合作伙伴中心帐户中删除租户，请在“租户”页（在“帐户设置”中）上查找其名称，然后选择“删除”  。 系统将提示你确认要删除该租户。 完成此操作后，该租户中的任何用户都将无法登录到合作伙伴中心帐户，并且将删除为这些用户配置的任何权限。

删除租户后，从该租户添加到合作伙伴中心帐户的所有用户都将无法再登录该帐户。

> [!TIP]
> 如果你当前使用某个租户中的帐户登录到合作伙伴中心，则无法删除该租户。 若要删除租户，必须以与帐户关联的其他租户的管理员身份登录到合作伙伴中心。 如果只有一个租户与帐户相关联，则只能在使用打开帐户所用的 Microsoft 帐户登录后删除该租户。

## <a name="agreements"></a>协议

使用 "**帐户设置**") 的 "合作伙伴中心" (的 "**协议**" 部分，您可以查看已授权发布协议的列表。 这些协议根据名称和版本号列出，包括接受协议的日期和接受协议的用户的名称。

如果有需要注意的协议更新，此页顶部可能会显示所需的操作。 若要接受更新的协议，请先阅读链接的协议版本，然后选择“接受协议”。
