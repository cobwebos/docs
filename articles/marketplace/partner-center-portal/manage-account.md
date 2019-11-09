---
title: 如何在合作伙伴中心管理商业 Marketplace 帐户
description: 了解如何在合作伙伴中心管理商业 Marketplace 帐户。
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 2edc644f003af2b33054a5cbdbaee81c9baa38cd
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847456"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>如何在合作伙伴中心管理商业 Marketplace 帐户 

[创建合作伙伴中心帐户](./create-account.md)后，可以使用 "[商业应用商店" 仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)管理你的帐户和产品/服务。

本文将介绍如何管理合作伙伴中心帐户，包括如何执行以下操作： 

- [访问合作伙伴中心帐户设置](#access-your-account-settings)
- [查找你的发布者 ID、卖方 ID、用户 ID 和 Azure AD 租户](#account-details)
- [更新联系人信息](#contact-info)
- [管理财务详细信息（帐户、税务配置文件、支出保留状态）](#financial-details)
- [设置跟踪 Guid 以便监视客户使用情况](#tracking-guids)
- [管理用户](#manage-users)
- [管理组](#manage-groups)
- [管理 Azure AD 应用程序](#manage-azure-ad-applications)
- [定义用户角色和权限](#define-user-roles-and-permissions)
- [管理 Azure AD 租户（工作帐户）](#manage-tenants)
- [管理合作伙伴中心协议](#agreements)


## <a name="access-your-account-settings"></a>访问你的帐户设置

如果你尚未执行此操作，你（或你的组织的管理员）应访问合作伙伴中心帐户的[帐户设置](https://partner.microsoft.com/dashboard/account/management)，以便：
- 检查公司的帐户验证状态
- 确认你的卖方 ID、MPN ID、发布者 ID 和联系人信息，包括公司审批者和卖方联系人
- 设置公司的财务详细信息，包括免税（如果适用）
- 为将在合作伙伴中心使用你的业务帐户的任何人创建用户帐户

### <a name="open-developer-settings"></a>打开开发人员设置

帐户设置位于合作伙伴中心的 "[商用 Marketplace" 仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace)的右上角。 选择齿轮图标（位于仪表板的右上角附近），然后选择 " **开发人员设置**"。 

![合作伙伴中心的 "帐户设置" 菜单](./media/dashboard-developer-settings.png)

在**帐户设置**内部，你将能够查看：
- **帐户详细信息**：帐户类型和帐户状态
- **发布者**id：卖方 ID、用户 Id、发布者 id、Azure AD 租户等。
- **联系人信息**：发布者显示名称、卖方联系人姓名、电子邮件、电话和地址
- **财务详细信息**：支出帐户、税务配置文件和支出保留状态
- **设备**：任何与帐户关联的测试设备
- **跟踪 guid**：任何跟踪 guid 与你的帐户关联

### <a name="account-details"></a>帐户详细信息

在 "帐户详细信息" 部分中，可以看到基本信息，如**帐户类型**（公司或个人）以及帐户的**验证状态**。 在帐户验证过程中，这些设置将显示所需的每个步骤，包括电子邮件验证、雇用验证和业务验证。 你还可以在此处更新电子邮件，并根据需要重新发送验证。 

### <a name="publisher-ids"></a>发布者 Id

在 "发布者 Id" 部分中，可以看到**卖方 id**、 **MPN Id**和**发布者 id**。 这些值由 Microsoft 分配，用于唯一标识你的开发人员帐户，而不能进行编辑。

### <a name="contact-info"></a>联系信息

在 "联系人信息" 部分中，你可以看到你的**发布者显示名称**、**卖方联系信息**（公司卖方的联系人姓名、电子邮件、电话号码和地址），以及**公司批准者**（姓名、电子邮件和电话号码。拥有批准公司决策的权利的个人。 

### <a name="financial-details"></a>财务详细信息

在 "财务详细信息" 部分中，如果发布付费应用程序、外接程序或服务，则可以提供或更新财务信息。 

如果只打算列出免费产品/服务，则无需设置付出的帐户或填写任何税务形式。 如果你稍后改变主意，并决定要通过 Microsoft 进行销售，则可以设置你的帐户并在该时间填写纳税表单。 

#### <a name="payout-account"></a>帐户支出

费用帐户是银行帐户，将从你的销售开始向其发送邮件。 此银行帐户必须在注册合作伙伴中心帐户所在的同一国家/地区。

若要设置你的帐户，需要**关联你的 Microsoft 帐户**：
1. 在 "**帐户设置**" 的 "**财务详细信息**" 部分下，选择 "**关联你的 Microsoft 帐户**"。 
2. 出现提示时，请用你的 Microsoft 帐户（MSA）登录。 此帐户不能与另一个合作伙伴中心帐户关联。 
3. 若要完成帐户的设置，请完全从合作伙伴中心注销，然后使用你的 Microsoft 帐户（而不是工作帐户）重新登录。 

现在，你的 Microsoft 帐户已关联，若要添加支出帐户，你将需要：
- **选择付款方式**：银行帐户或 PayPal
- **添加支付信息**：这可能包括选择帐户类型（检查或节省）、输入帐户持有者名称、帐号和银行代号、帐单地址、电话号码或 PayPal 电子邮件地址。 \* 有关使用 PayPal 作为帐户付款方式的详细信息，以及确定它是否在你的市场区域中受支持，请参阅[PayPal 信息](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)。

> [!IMPORTANT]
> 更改付款帐户可能会推迟付款（最多一个付款周期）。 出现这种延迟的原因是，我们需要验证帐户更改，就像第一次设置付出的帐户一样。 帐户通过验证后，仍会收到全额款项；当前付款周期应得的任何付款将添加到下一个付款周期。  

#### <a name="tax-profile"></a>税务配置文件

查看当前的税务配置文件状态，确认是否显示了正确的**实体类型**和**税务证书信息**。 选择 "**编辑**" 以更新或完成任何所需的窗体。

若要建立税务状态，你必须指定国家/地区和公民，并完成与你所在国家/地区关联的相应纳税窗体。

无论你所在国家/地区的居民如何，你都必须填写美国税务表格才能通过 Microsoft 销售任何优惠。 满足特定美国派驻要求的合作伙伴必须填写 IRS （9）表单。 美国以外的其他合作伙伴必须填写 IRS （8）表单。 完成税务分析后，可以联机填写这些表单。

不需要使用单独美国的纳税人标识号（或某种）来接收来自 Microsoft 的付款或声明税务条约权益。

您可以在合作伙伴中心以电子方式完成和提交您的税务表格;大多数情况下，无需打印和邮寄任何形式。

不同国家/地区的税务要求不同。 你必须按税款支付的确切数量取决于你销售产品/服务的国家/地区。 在某些国家/地区，Microsoft 汇寄销售并使用了税款。 这些国家/地区将在列出产品/服务的过程中确定。 在其他国家/地区，根据你的注册位置，你可能需要将销售的销售和销项直接寄给本地税务主管机构。 此外，销售人员还可以根据收入获得收入。 我们强烈建议你联系你所在国家或地区的相关机构，最能最好地帮助你确定适用于你的 Microsoft 销售交易的正确税务信息。

##### <a name="withholding-rates"></a>预缴汇率
在纳税窗体中提交的信息将确定相应的预缴税金费率。 预缴税金仅适用于在美国中进行的销售;在非美国位置进行的销售不受预扣的限制。 预缴汇率有所不同，但对于大多数在美国之外注册的开发人员来说，默认速率为30%。 如果你的国家/地区同意与美国的所得税条约，则可以选择降低此速率。

##### <a name="tax-treaty-benefits"></a>税务条约权益
如果你在美国之外，则可以利用税务条约权益。 这些权益因国家/地区而异，可让你减少 Microsoft withholds 的税费。 可以通过完成 "8BEN" 窗体的第 II 部分来声明税务条约权益。 建议你与你所在国家或地区的相应资源通信，以确定这些权益是否适用于你。

[详细了解 Windows 应用/游戏开发人员和 Azure Marketplace 发布者的税务详细信息](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)。

#### <a name="payout-hold-status"></a>付出的保留状态

默认情况下，Microsoft 每月发送付款。 但是，可以选择将付款置于保持状态，这将阻止向帐户发送付款。 如果选择将付款置于保持状态，我们将继续记录你所获得的任何收入，并提供你的付出的详细信息**摘要**。 但是，我们不会向你的帐户发送任何付款，直到你删除该保留。 

若要按保留付款，请参阅 "**帐户设置**"。 在 "**财务详细信息**" 下的 "**付出支出状态**" 部分中，将滑块切换到 **"打开**"。 你可以随时更改你的支出保留状态，但请注意，你的决策将影响下一次的月额度。 例如，如果您想要保留四月的付出，请确保在三月份结束之前将您的支出保持状态设置为 **"开**"。

将付出的保留状态设置为 **"开**" 后，所有付款都将处于暂停状态，直到将滑块切换回 "**关闭**"。 当你执行此操作时，你将在下一个月度费用周期内包含（前提是已满足任何适用的付款阈值）。 例如，如果你已有付款，但想要在六月生成一项支出，则请确保在 "可能" 结束之前，将 "付出保留" 状态切换为 "**关**"。

> [!NOTE]
> 你的**支出保留状态**选择适用于通过 Microsoft 合作伙伴中心支付的**所有**收入源，包括 Azure Marketplace、AppSource、Microsoft Store、广告等）。 不能为每个收入源选择不同的保留状态。

### <a name="devices"></a>设备

设备管理设置仅适用于 UWP 发布。 [了解详细信息](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)。

### <a name="tracking-guids"></a>跟踪 Guid

全局唯一标识符（Guid）是可用于跟踪 Azure 使用情况的唯一参考编号（含32十六进制数字）。 

若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://aka.ms/StoragePartners)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。

建议为每个产品/服务和每个产品的分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用单个 GUID。

如果使用模板部署产品并且产品在 Azure 市场和 GitHub 中都提供，则可以创建并注册 2 个不同的 GUID：

*   Azure 市场中的产品 A
*   GitHub 中的产品 A

报告根据合作伙伴值（Microsoft 合作伙伴 ID）和 GUID 来完成。 你还可以在产品/服务中的每个计划之间进行更详细的跟踪。

有关详细信息，请参阅[使用 Guid 跟踪 Azure 客户使用情况常见问题解答](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq)。


## <a name="establish-a-billing-profile"></a>建立计费配置文件

如果要发布[dynamics 365 For Customer Engagement &](./create-new-customer-engagement-offer.md)适用[于运营部门](./create-new-operations-offer.md)的 Power Apps 或 Dynamics 365，需要完成**计费配置文件**。

将从你的 MPN ID 预填充计费地址，稍后你可以更新此地址。 税和 VAT ID 字段是可选的。  不能编辑国家/地区名称和公司名称。

## <a name="multi-user-account-management"></a>多用户帐户管理

合作伙伴中心利用[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) （Azure AD）进行多用户帐户访问和管理。 你的组织的 Azure AD 会在注册过程中自动与你的合作伙伴中心帐户关联。 

## <a name="manage-users"></a>管理用户

合作伙伴中心的 "**用户**" 部分（在 "**帐户设置**" 下）让你使用 Azure AD 来管理有权访问你的合作伙伴中心帐户的用户、组和 Azure AD 应用程序。 为了管理用户，必须使用[工作帐户](./company-work-accounts.md)（关联的 Azure AD 租户）登录。 若要管理不同工作帐户/租户中的用户，你需要注销，然后以具有该工作帐户/租户的**管理员**权限的用户身份重新登录。 

使用工作帐户（Azure AD 租户）登录后，可以：
- [添加或删除用户](#add-or-remove-users)
- [更改用户密码](#change-a-user-password)
- [添加或删除组](#add-or-remove-users)
- [添加或删除 Azure AD 应用程序](#add-new-azure-ad-applications)
- [管理 Azure AD 应用程序的密钥](#manage-keys-for-an-azure-ad-application)
- [定义用户角色和权限](#define-user-roles-and-permissions)


请记住，所有合作伙伴中心用户（包括组和 Azure AD 应用程序）必须在与你的合作伙伴中心帐户关联的[Azure AD 租户](#manage-tenants)中拥有一个活动工作帐户。 

### <a name="add-or-remove-users"></a>添加或删除用户

你的帐户必须具有要在其中添加或编辑用户的[工作帐户（Azure AD 租户）](./company-work-accounts.md)的[**管理者级别**](#define-user-roles-and-permissions)权限。

#### <a name="add-existing-users"></a>添加现有用户

若要将用户添加到你的公司[工作帐户（Azure AD 租户）](./company-work-accounts.md)中已存在的合作伙伴中心帐户，请执行以下操作：

1. 请在 "**帐户设置**"**下，选择**"**添加用户**"。
2. 从显示的列表中选择一个或多个用户。 您可以使用 "搜索" 框搜索特定的用户。
\* 如果选择将多个用户添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个用户，请对每个角色或自定义权限集重复这些步骤。
3.  选择完用户后，请单击 "**添加**"。
5.  在 "**角色**" 部分中，指定所选用户的角色或自定义权限。
6.  选择“保存”。

#### <a name="create-new-users"></a>创建新用户

若要创建新的用户帐户，必须具有具有[**全局管理员**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限的帐户。 

1. 请在 "**帐户设置**"**下，选择**"**添加用户**"，然后选择 "**新建用户**"。
1. 输入每个新用户的 "名字"、"姓氏" 和 "用户名"。 
1. 如果希望新用户在组织的目录中拥有全局管理员帐户，请选中 "**将此用户设为 Azure AD 中的全局管理员" 框，并完全控制所有目录资源**。 这将允许用户完全访问公司的 Azure AD 中的所有管理功能。 他们将能够添加和管理组织的工作帐户（Azure AD 租户）中的用户，但不是在合作伙伴中心，除非你向帐户授予适当的角色/权限。 
1. 如果选中此复选框以**使此用户成为全局管理员**，则需要提供**密码恢复电子邮件**，以便用户在必要时恢复其密码。
1. 在 "**组成员身份**" 部分中，选择希望新用户所属的任何组。
1. 在 "**角色**" 部分中，指定用户的角色或自定义权限。
1. 选择“保存”。

在合作伙伴中心创建新用户时，还会在你登录到的工作帐户（Azure AD 租户）中为该用户创建一个帐户。 更改伙伴中心中的用户名称将在组织的工作帐户（Azure AD 租户）中做出相同的更改。

#### <a name="invite-new-users-by-email"></a>通过电子邮件邀请新用户

若要通过电子邮件邀请当前不是公司工作帐户（Azure AD 租户）的一部分的用户，你必须拥有具有[**全局管理员**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限的帐户。 

1. 请在 "**帐户设置**"**下，选择**"**添加用户**"，然后选择 "**通过电子邮件邀请用户**"。
2. 输入一个或多个电子邮件地址（最多10个），用逗号或分号分隔。
3. 在 "**角色**" 部分中，指定用户的角色或自定义权限。
4. 选择“保存”。

你邀请的用户将收到一封电子邮件邀请，可加入你的合作伙伴中心帐户。 将在你的工作帐户（Azure AD 租户）中创建新的来宾用户帐户。 每个用户都需要接受其邀请，才能访问你的帐户。

如果需要重新发送邀请，请访问 "**用户**" 页，在用户列表中找到该邀请，选择其电子邮件地址（或显示 "*邀请待定*" 的文本）。 然后，在页面底部，选择 "**重新发送邀请**"。
 

> [!NOTE]
> 如果你的组织使用[目录集成](https://go.microsoft.com/fwlink/p/?LinkID=724033)将本地目录服务与你的 Azure AD 同步，则你将无法在合作伙伴中心创建新的用户、组或 Azure AD 应用程序。 你（或你的本地目录中的另一个管理员）需要直接在本地目录中创建它们，然后才能在合作伙伴中心内查看并添加它们。

#### <a name="remove-a-user"></a>删除用户

**若要**从工作帐户（Azure AD 租户）中删除用户，请在 "**帐户设置**" 下，选择想要使用最右侧列中的复选框删除的用户，然后从 "可用操作" 中选择 "**删除**"。 将显示一个弹出窗口，供您确认是否要删除所选用户。

#### <a name="change-a-user-password"></a>更改用户密码

如果你的某个用户需要更改其密码，则如果你在创建用户帐户时提供了**密码恢复电子邮件**，则他们可以自行完成此操作。 还可以通过执行以下步骤来更新用户的密码。 若要更改你的公司工作帐户（Azure AD 租户）中的用户密码，你必须使用具有[**全局管理员**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限的帐户登录。 请注意，这将更改用户的 Azure AD 租户中的密码，以及他们用于访问合作伙伴中心的密码。

1.  从 "**用户**" 页（在 "**帐户设置**" 下）中，选择要编辑的用户帐户的名称。
2.  选择页面底部的 "**重置密码**" 按钮。
3.  将显示一个确认页，其中显示了用户的登录信息，包括临时密码。 请确保打印或复制此信息并将其提供给用户，因为离开此页面后你将无法访问临时密码。


## <a name="manage-groups"></a>管理组

组允许您同时控制多个用户角色和权限。

#### <a name="add-an-existing-group"></a>添加现有组

若要将组织的工作帐户（Azure AD 租户）中已存在的组添加到合作伙伴中心帐户，请执行以下操作： 

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 "**添加组**"。
2.  从显示的列表中选择一个或多个组。 您可以使用 "搜索" 框搜索特定组。
如果选择将多个组添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个组，请对每个角色或自定义权限集重复这些步骤。
3.  选择完组后，单击 "**添加**"。
4.  在 "**角色**" 部分中，指定所选组的角色或自定义权限。 组的所有成员都能够访问你的合作伙伴中心帐户，其中包含你应用于组的权限，而不考虑与其个人帐户关联的角色和权限。
5.  选择“保存”。

添加现有组时，作为该组成员的每个用户都可以访问你的合作伙伴中心帐户，以及与组的分配角色关联的权限。

#### <a name="add-a-new-group"></a>添加新组

若要向合作伙伴中心帐户添加全新组，请执行以下操作： 

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 "**添加组**"。
2.  在下一页上，选择 "**新建组**"。
3.  输入新组的显示名称。
4.  指定组的角色或自定义权限。 组的所有成员都能够访问你在此处应用的权限的合作伙伴中心帐户，而与它们的个人帐户关联的角色/权限无关。
5.  从显示的列表中选择新组的 "用户"。 您可以使用 "搜索" 框搜索特定的用户。
6.  选择完用户后，单击 "**添加**" 以将其添加到新组。
7.  选择“保存”。

请注意，将在组织的工作帐户（Azure AD 租户）中创建此新组，而不只是在合作伙伴中心帐户中创建。

#### <a name="remove-a-group"></a>删除组

若要从工作帐户（Azure AD 租户）中删除组，请在 "**帐户设置**" 下，**选择要使用**最右侧列中的复选框删除的组，然后从 "可用的操作" 中选择 "**删除**"。 将显示一个弹出窗口，供您确认是否要删除选定的组。

## <a name="manage-azure-ad-applications"></a>管理 Azure AD 应用程序

你可以允许作为公司 Azure AD 的一部分的应用程序或服务访问你的合作伙伴中心帐户。 

#### <a name="add-existing-azure-ad-applications"></a>添加现有 Azure AD 应用程序 

添加已存在于公司的 Azure Active Directory 中的应用程序： 

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 "**添加 Azure AD 应用程序**"。
2.  从显示的列表中选择一个或多个 Azure AD 应用程序。 您可以使用 "搜索" 框搜索特定的 Azure AD 应用程序。 如果选择多个 Azure AD 应用程序添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个 Azure AD 应用程序，请对每个角色或自定义权限集重复这些步骤。
3.  选择 Azure AD 应用程序后，单击 "添加" "**选择**"。
5.  在 "**角色**" 部分中，指定所选 Azure AD 应用程序的角色或自定义权限。
6.  选择“保存”。

#### <a name="add-new-azure-ad-applications"></a>添加新的 Azure AD 应用程序 

如果要向合作伙伴中心授予全新 Azure AD 应用程序帐户的访问权限，则可以在 "**用户**" 部分中创建一个。 请注意，这会在你的公司工作帐户（Azure AD 租户）中创建新帐户，而不只是在你的合作伙伴中心帐户中。 如果主要使用此 Azure AD 应用程序进行合作伙伴中心身份验证，并且不需要用户直接访问该应用程序，则可以输入**回复 URL**和**应用 ID URI**的任何有效地址，前提是这些值未被任何其他 Azure 使用目录中的 AD 应用程序。

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 "**添加 Azure AD 应用程序**"。
2.  在下一页上，选择 "**新建" Azure AD 应用程序**"。
3.  输入新 Azure AD 应用程序的**回复 URL** 。 这是用户可在其中登录并使用你的 Azure AD 应用程序（有时也称为 "应用 URL" 或 "登录 URL"）的 URL。 **回复 URL**的长度不能超过256个字符，并且在你的目录中必须是唯一的。
4.  输入新 Azure AD 应用程序的**应用 ID URI** 。 这是在向 Azure AD 发送单一登录请求时提供的 Azure AD 应用程序的逻辑标识符。 请注意，每个 Azure AD 应用程序的目录中的应用程序**ID URI**必须是唯一的。 此 ID 长度不能超过256个字符。 有关应用程序 ID URI 的详细信息，请参阅将[应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts)。
5.  在 "**角色**" 部分中，指定 Azure AD 应用程序的角色或自定义权限。
6.  选择“保存”。

添加或创建 Azure AD 应用程序后，你可以返回到 "**用户**" 部分，并选择应用程序名称以查看应用程序的设置，包括租户 ID、客户端 ID、回复 URL 和应用 ID URI。

#### <a name="remove-an-application"></a>删除应用程序

若要从工作帐户中删除应用程序（Azure AD 租户），请在 "**帐户设置**" 下，**选择要使用**最右侧列中的复选**框删除的**应用程序，然后从可用的操作。 将显示一个弹出窗口，供您确认是否要删除选定的应用程序。

#### <a name="manage-keys-for-an-azure-ad-application"></a>管理 Azure AD 应用程序的密钥

如果 Azure AD 应用程序在 Microsoft Azure AD 中读取和写入数据，将需要一个密钥。 可以通过在合作伙伴中心编辑其信息，为 Azure AD 应用程序创建密钥。 你还可以删除不再需要的密钥。

1.  在 "**用户**" 页（在 "**帐户设置**" 下）中，选择 Azure AD 应用程序的名称。 你将看到 Azure AD 应用程序的所有活动密钥，包括创建密钥的日期和过期时间。 
2. 若要删除不再需要的密钥，请选择 "**删除**"。
3.  若要添加新项，请选择 "**添加新项**"。
4.  你将看到一个屏幕，其中显示了**客户端 ID**和**密钥值**。 请确保打印或复制此信息，因为离开此页面后将无法再次访问。
4.  如果要创建更多密钥，请选择 "**添加其他密钥**"。


## <a name="define-user-roles-and-permissions"></a>定义用户角色和权限

公司的用户可以在合作伙伴中心为商业市场计划分配以下角色和权限：

- **Manager**
  - 可以访问除税务和支出设置之外的所有 Microsoft 帐户功能
  - 可以管理用户、角色和工作帐户（租户）
- **开发人员**
  - 可以管理和发布产品/服务
  - 可以查看一些发布服务器报表

> [!NOTE]
> 对于商业市场计划，不使用全局管理员、业务参与者、财务参与者和营销人员角色。 将这些角色分配给用户不起作用。 只有管理人员和开发人员角色向用户授予权限。

有关在合作伙伴中心的其他区域（例如 Azure Active Directory （AD）、云解决方案提供商（CSP）、控制面板供应商（CPV）、来宾用户或 Microsoft 合作伙伴网络（MPN））管理角色和权限的详细信息，请参阅[分配用户角色和权限](https://docs.microsoft.com/partner-center/permissions-overview)。


## <a name="manage-tenants"></a>管理租户

在本文档中，Azure Active Directory （AD）租户（也称为 "工作帐户"）是组织在 Azure 门户中的表示形式，可帮助你管理内部 Microsoft 云服务的特定实例和外部用户。 如果你的组织已订阅 Microsoft 云服务（如 Azure、Microsoft Intune 或 Office 365），则会为你建立一个 Azure AD 租户。 

你可以设置多个租户以与合作伙伴中心一起使用。 具有合作伙伴中心帐户中**管理员**角色的任何用户都可以选择在帐户中添加和删除 Azure AD 租户。  

### <a name="add-an-existing-tenant"></a>添加现有租户

若要将另一个 Azure AD 租户与合作伙伴中心帐户关联：

1.  在 "**租户**" 页（在 "**帐户设置**" 下）中，选择 "**关联其他 Azure AD 租户**"。
2. 输入要关联的租户的 Azure AD 凭据。
3. 查看 Azure AD 租户的组织名称和域名。 若要完成关联，请选择 "**确认**"。

如果关联成功，你将可以在合作伙伴中心的 "**用户**" 部分中添加和管理帐户用户。

### <a name="create-a-new-tenant"></a>创建新租户

使用合作伙伴中心帐户创建全新的 Azure AD 租户：

1.  从 "**租户**" 页（在 "**帐户设置**" 下）中，选择 "**创建新的 Azure AD 租户**"。
2. 输入新 Azure AD 的目录信息：
    - **域名**：我们将用于 Azure AD 域的唯一名称，以及 "onmicrosoft.com"。 例如，如果输入 "example"，则 Azure AD 域应为 "example.onmicrosoft.com"。
    - **联系人电子邮件**：一个电子邮件地址，我们可以在必要时联系你的帐户。
    - **全局管理员用户帐户信息**：要用于新的全局管理员帐户的名字、姓氏、用户名和密码。
3. 选择 "**创建**" 以确认新的域和帐户信息。
4. 以新的 Azure AD 全局管理员用户名和密码登录，开始[添加和管理用户](#manage-users)。

若要详细了解如何在 Azure 门户中创建新租户，而不是通过合作伙伴中心门户创建，请参阅文章[在 Azure Active Directory 中创建新租户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)。

### <a name="remove-a-tenant"></a>删除租户

若要从合作伙伴中心帐户中删除租户，请在**租户**页上查找其名称（在 "**帐户设置**" 中），然后选择 "**删除**"。 系统将提示你确认是否要删除该租户。 完成此操作后，该租户中的用户将无法登录到合作伙伴中心帐户，并且将删除你为这些用户配置的任何权限。

删除租户后，从该租户添加到合作伙伴中心帐户的所有用户将无法再登录该帐户。

> [!TIP]
> 如果你当前使用同一个租户中的帐户登录到合作伙伴中心，则无法删除该租户。 若要删除租户，必须以**管理员**身份登录到与帐户关联的另一个租户的合作伙伴。 如果只有一个租户与帐户相关联，则只能在用打开帐户的 Microsoft 帐户登录后删除该租户。


## <a name="agreements"></a>协议

使用合作伙伴中心的 "**协议**" 部分（在 "**帐户设置**" 下），可以查看已授权发布协议的列表。 这些协议根据名称和版本号列出，包括接受日期和接受协议的用户的姓名。 

如果有需要注意的协议更新，则可能会在此页面顶部显示**所需的操作**。 若要接受更新的协议，请首先阅读链接的协议版本，然后选择 "**接受协议**"。 


## <a name="next-steps"></a>后续步骤

- [创建新的 SaaS 产品/服务](./create-new-saas-offer.md)
