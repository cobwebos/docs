---
title: 如何在合作伙伴中心管理商业市场帐户
description: 了解如何在合作伙伴中心管理商业市场帐户。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 6d3952c38703d8102a45c4117e3c59e3fa464957
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275809"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>如何在合作伙伴中心管理商业市场帐户

[创建合作伙伴中心帐户](./create-account.md)后，您可以使用[商业市场仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)管理您的帐户和优惠。

在本文中，我们将深入探讨如何管理合作伙伴中心帐户，包括：

- [访问合作伙伴中心帐户设置](#access-your-account-settings)
- [查找发布者 ID、赛门铁克 ID、卖家 ID、用户 ID、MPN ID 和 Azure AD 租户](#account-details)
- [更新联系信息](#contact-info)
- [设置跟踪 GUID 以监控客户使用情况](#tracking-guids)
- [管理用户](#manage-users)
- [管理组](#manage-groups)
- [管理 Azure AD 应用程序](#manage-azure-ad-applications)
- [定义用户角色和权限](#define-user-roles-and-permissions)
- [管理 Azure AD 租户（工作帐户）](#manage-tenants)
- [管理合作伙伴中心协议](#agreements)

## <a name="access-your-account-settings"></a>访问您的帐户设置

如果您尚未这样做，您（或您的组织的管理员）应访问合作伙伴中心帐户的[帐户设置](https://partner.microsoft.com/dashboard/account/management)，以便：
- 检查您公司的帐户验证状态
- 确认您的赛门铁克 ID、卖家 ID、MPN ID、发布者 ID 和联系信息，包括公司审批人和卖家联系人
- 为将在合作伙伴中心使用您业务帐户的任何人创建用户帐户

### <a name="open-developer-settings"></a>打开开发人员设置

帐户设置位于合作伙伴中心[商业市场仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace)的右上角。 选择齿轮图标（靠近仪表板右上角），然后选择 **"开发人员"设置**。

![合作伙伴中心中的帐户设置菜单](./media/dashboard-developer-settings.png)

在**帐户设置**中，您将能够查看您的：
- **帐户详细信息**：帐户类型和帐户状态
- **发布者 ID：** 卖家 ID、用户 ID、发布者 ID、Azure AD 租户等。
- **联系信息**： 发布者显示名称、卖家联系人姓名、电子邮件、电话和地址
- **跟踪 GUID：** 任何跟踪 GUID 与您的帐户相关联

### <a name="account-details"></a>帐户详细信息

在"帐户详细信息"部分中，您可以看到基本信息，例如**您的帐户类型**（公司或个人）和帐户的**验证状态**。 在帐户验证过程中，这些设置将显示所需的每个步骤，包括电子邮件验证、雇佣验证和业务验证。 您也可以在此处更新电子邮件，并根据需要重新发送验证。

### <a name="publisher-ids"></a>发布者代

在发布者 ID 部分中，您可以看到**您的赛门铁克 ID、****卖家 ID、****用户 ID、MPN** **ID**和 Azure **AD 租户**。 这些值由 Microsoft 分配，以唯一标识您的开发人员帐户，并且无法编辑。

### <a name="contact-info"></a>联系信息

在"联系信息"部分中，您可以看到您的**Publisher 显示名称**、**卖家联系信息**（公司卖家的联系人姓名、电子邮件、电话号码和地址）**和公司审批人**（有权批准公司决策的个人的姓名、电子邮件和电话号码）。

#### <a name="payout-account"></a>付款帐户

付款帐户是从销售中汇到收益的银行帐户。 此银行帐户必须位于您注册合作伙伴中心帐户的同一国家/地区。

要设置您的支出帐户，您需要**关联您的 Microsoft 帐户**：
1. 转到合作伙伴中心[中的"商业市场概述"页面](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)。
2. 在"配置文件"部分下，选择 **"关联您的 Microsoft 帐户**"。
3. 出现提示后，请使用 Microsoft 帐户 （MSA） 登录。 此帐户不能已与其他合作伙伴中心帐户关联。
4. 要完成支出帐户的设置，请完全注销合作伙伴中心，然后使用 Microsoft 帐户（而不是您的工作帐户）重新登录。

现在，您的 Microsoft 帐户已关联，要添加支出帐户，您需要：
- **选择付款方式**：银行账户或PayPal
- **添加付款信息**：这可能包括选择帐户类型（支票或储蓄）、输入帐户持有人姓名、帐号和路由号码、帐单地址、电话号码或 PayPal 电子邮件地址。 *有关使用PayPal作为您的帐户付款方式，并了解其是否支持您的市场区域的详细信息，请参阅[PayPal信息](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)。

> [!IMPORTANT]
> 更改付款帐户可能会推迟付款（最多一个付款周期）。 出现此延迟的原因是我们需要验证帐户更改，就像我们首次设置支出帐户时一样。 帐户通过验证后，仍会收到全额款项；当前付款周期应得的任何付款将添加到下一个付款周期。  

#### <a name="tax-profile"></a>税务配置文件

查看您当前的税务配置文件状态，确认显示正确的**实体类型**和**税务证书信息**。 选择 **"编辑"** 以更新或完成任何必需的表单。

为了确立您的税务身份，您必须指定您的居住国和公民身份，并填写与您的国家/地区相关的相应纳税表格。

无论您的居住国还是公民权，您必须填写美国税务表格才能通过 Microsoft 销售任何优惠。 满足某些美国居住要求的合作伙伴必须填写 IRS W-9 表格。 美国以外的其他合作伙伴必须填写 IRS W-8 表格。 可以在完成纳税配置文件时在线填写这些表单。

不需要提供美国个人纳税识别号码（或 ITIN）即可从 Microsoft 接收付款或申请免税待遇。

您可以在合作伙伴中心以电子方式填写和提交您的纳税申报表;在大多数情况下，您不需要打印和邮寄任何表单。

不同的国家和地区有不同的纳税要求。 您必须在纳税中支付的确切金额取决于您销售优惠的国家和地区。 在某些国家/地区，Microsoft 代表您征收销售和使用税。 这些国家/地区将在列出您的报价的过程中被识别。 在其他国家/地区，根据您注册的地点，您可能需要将销售汇出，并将销售用税直接汇给当地税务机关。 此外，您收到的销售收入可能作为收入纳税。 我们强烈建议您联系您所在国家或地区的有关当局，以便最好地帮助您确定适用于 Microsoft 销售交易的税务信息。

##### <a name="withholding-rates"></a>预缴比率
你在税单中提交的信息将确定相应的预缴比率。 预缴比率仅适用于你在美国的销售；非美国地区的销售不受预缴约束。 不同情况下的预缴比率有所不同，但对于在美国之外国家或地区注册的大多数开发人员，黙认税率是 30%。 如果你的国家和地区与美国签有所得税条约，你可以选择降低此税率。

##### <a name="tax-treaty-benefits"></a>免税待遇
如果你在美国境外，你也许能够利用免税待遇。 这些福利因国家/地区而异，并可能允许您减少 Microsoft 扣缴的税款金额。 你可以通过填写 W-8BEN 表格的第 II 部分来申请免税待遇。 我们建议你咨询所在国家或地区的相关人员，以确定是否可以享受这些优惠。

[详细了解 Windows 应用/游戏开发人员和 Azure 应用商店发布商的税务详细信息](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)。

#### <a name="payout-hold-status"></a>付款暂停状态

默认情况下，Microsoft 每月发送一次付款。 但是，您可以选择暂停付款，这将阻止将付款发送到您的帐户。 如果您选择暂停您的支出，我们将继续记录您赚取的任何收入，并在**您的付款摘要**中提供详细信息。 但是，在您删除保留之前，我们不会向您的帐户发送任何付款。 

若要暂停付款，请转到**帐户设置**。 在 **“财务详细信息”** 下的 **“付款暂停状态”** 部分中，将滑块切换为 **“开”**。 你可以随时更改你的付款暂停状态，但请注意，你的决定将影响下一次每月付款。 例如，如果您希望保留 4 月份的付款，请确保在 3 月底之前将付款保留状态设置为 **"打开**"。

将付款暂停状态设置为 **“开”** 后，所有付款都将处于暂停状态，直到你将滑块切换回 **“关”**。 当您这样做时，您将在下一个月的支付周期中被包括在内（前提是任何适用的付款阈值都已满足）。 例如，如果您已暂停付款，但希望 6 月生成支出，请确保在 5 月底之前将付款保留状态切换为 **"关闭**"。

> [!NOTE]
> **您的"支出保留"状态**选择适用于通过 Microsoft 合作伙伴中心支付**的所有**收入来源，包括 Azure 应用商店、AppSource、Microsoft 应用商店、广告等。 无法为每个收入来源选择不同的暂停状态。

### <a name="devices"></a>设备

设备管理设置仅适用于 UWP 发布。 [了解详情](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)。

### <a name="tracking-guids"></a>跟踪 GUID

全局唯一标识符 （GUID） 是唯一的参考编号（具有 32 个十六进制数字），可用于跟踪 Azure 使用情况。 

若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://aka.ms/StoragePartners)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。

建议为每个产品/服务和每个产品的分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用单个 GUID。

如果使用模板部署产品并且产品在 Azure 市场和 GitHub 中都提供，则可以创建并注册 2 个不同的 GUID：

*   Azure 市场中的产品 A
*   GitHub 中的产品 A

报告根据合作伙伴值（Microsoft 合作伙伴 ID）和 GUID 来完成。 您还可以在更精细的级别跟踪 GUID，以便与产品/服务中的每个计划保持一致。

有关详细信息，请参阅[使用 GUID 跟踪 Azure 客户使用情况常见问题 。](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq)

## <a name="create-a-billing-profile"></a>创建帐单配置文件

如果您要发布动态[365 用于客户参与&电源应用](./create-new-customer-engagement-offer.md)或[动态 365 为运营](./create-new-operations-offer.md)产品，您需要填写您的**帐单配置文件**。

帐单地址从法人预先填充，您可以稍后更新此地址。 "税务"和"增值税 ID"字段是可选的。  无法编辑国家/地区名称和公司名称。

## <a name="multi-user-account-management"></a>多用户帐户管理

合作伙伴中心利用[Azure 活动目录](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)（Azure AD） 进行多用户帐户访问和管理。 作为注册过程的一部分，组织的 Azure AD 将自动与合作伙伴中心帐户关联。

## <a name="manage-users"></a>管理用户

合作伙伴中心**的用户**部分（在**帐户设置**）下，让我们使用 Azure AD 来管理有权访问合作伙伴中心帐户的用户、组和 Azure AD 应用程序。 为了管理用户，您必须使用[工作帐户](./company-work-accounts.md)（关联的 Azure AD 租户）登录。 要管理其他工作帐户/租户中的用户，您需要注销，然后以具有该工作帐户/租户的**Manager**权限的用户身份重新登录。

使用工作帐户（Azure AD 租户）登录后，可以：
- [添加或删除用户](#add-or-remove-users)
- [更改用户密码](#change-a-user-password)
- [添加或删除组](#add-or-remove-users)
- [添加或删除 Azure AD 应用程序](#add-new-azure-ad-applications)
- [管理 Azure AD 应用程序的密钥](#manage-keys-for-an-azure-ad-application)
- [定义用户角色和权限](#define-user-roles-and-permissions)

请记住，所有合作伙伴中心用户（包括组和 Azure AD 应用程序）都必须在与合作伙伴中心帐户关联的[Azure AD 租户](#manage-tenants)中具有活动工作帐户。

### <a name="add-or-remove-users"></a>添加或删除用户

您的帐户必须具有要在其中添加或编辑用户[的工作帐户（Azure AD 租户）](./company-work-accounts.md)[**的管理器级**](#define-user-roles-and-permissions)权限。

#### <a name="add-existing-users"></a>添加现有用户

要将用户添加到公司[工作帐户（Azure AD 租户）](./company-work-accounts.md)中已存在的合作伙伴中心帐户：

1. 转到**用户**（在 **"帐户设置**"下）并选择"**添加用户**"。
2. 从出现的列表中选择一个或多个用户。 你可以使用搜索框来搜索特定用户。
•如果选择要添加到合作伙伴中心帐户的多个用户，则必须为其分配相同的角色或自定义权限集。 要添加具有不同角色/权限的多个用户，请为每个角色或自定义权限集重复这些步骤。
3. 完成选择用户后，单击"**添加选定的**"。
4. 在**角色**部分中，为选定的用户指定角色或自定义权限。
5. 选择“保存”。****

#### <a name="create-new-users"></a>创建新用户

要创建新用户帐户，您必须具有具有[**全局管理员**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限的帐户。 

1. 转到**用户**（在 **"帐户设置**"下），选择 **"添加用户**"，然后选择"**创建新用户**"。
1. 为每个新用户输入名字、姓氏和用户名。 
1. 如果希望新用户在组织的目录中具有全局管理员帐户，请选中标记为 **"使此用户成为 Azure AD 中的全局管理员"复选框，并完全控制所有目录资源**。 这可使该用户完整访问公司 Azure AD 中的所有管理功能。 他们将能够添加和管理组织的工作帐户（Azure AD 租户）中的用户，尽管不在合作伙伴中心，除非您授予该帐户适当的角色/权限。
1. 如果选中此框**以使此用户成为全局管理员**，则需要提供**密码恢复电子邮件**，以便用户在必要时恢复其密码。
1. 在 **"组成员身份**"部分中，选择要新用户所属的任何组。
1. 在**角色**部分中，为用户指定角色或自定义权限。
1. 选择“保存”。****

在合作伙伴中心创建新用户还将在您登录的工作帐户（Azure AD 租户）中为该用户创建一个帐户。 更改合作伙伴中心中的用户名将在组织的工作帐户（Azure AD 租户）中进行相同的更改。

#### <a name="invite-new-users-by-email"></a>通过电子邮件邀请新用户

要通过电子邮件邀请当前不是公司工作帐户（Azure AD 租户）一部分的用户，您必须具有具有[**全局管理员**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限的帐户。

1. 转到**用户**（在 **"帐户设置**"下），选择 **"添加用户**"，然后选择**通过电子邮件邀请用户**。
2. 输入一个或多个电子邮件地址（最多十个），用逗号或分号隔开。
3. 在**角色**部分中，为用户指定角色或自定义权限。
4. 选择“保存”。****

您邀请的用户将获得加入合作伙伴中心帐户的电子邮件邀请。 将在工作帐户（Azure AD 租户）中创建新的来宾用户帐户。 每个用户需要接受邀请才能访问帐户。

如果需要重新发送邀请，请访问 **"用户"** 页面，在用户列表中查找邀请，选择其电子邮件地址（或显示 *"邀请待定*"的文本）。 然后，在页面底部，选择 **"重新发送邀请**"。

> [!NOTE]
> 如果组织使用[目录集成](https://go.microsoft.com/fwlink/p/?LinkID=724033)将本地目录服务与 Azure AD 同步，则无法在合作伙伴中心创建新用户、组或 Azure AD 应用程序。 您需要（或本地目录中的另一个管理员）需要在本地目录中直接创建它们，然后才能在合作伙伴中心中查看和添加它们。

#### <a name="remove-a-user"></a>删除用户

要从工作帐户（Azure AD 租户）中删除用户，请转到 **"用户**"（在 **"帐户设置**"下），使用最右侧列中的复选框选择要删除的用户，然后从可用操作中选择 **"删除**"。 将显示一个弹出窗口，以确认要删除选定的用户。

#### <a name="change-a-user-password"></a>更改用户密码

如果您的一个用户需要更改其密码，如果您在创建用户帐户时提供了**密码恢复电子邮件**，他们可以自己更改密码。 还可遵循以下步骤来更新用户密码。 要更改公司工作帐户（Azure AD 租户）中的用户密码，必须在具有[**全局管理员**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)权限的帐户上登录。 请注意，这将更改 Azure AD 租户中的用户的密码以及他们用于访问合作伙伴中心的密码。

1. 从 **"用户"** 页（在 **"帐户设置**"下）选择要编辑的用户帐户的名称。
2. 选择页面底部的 **"重置密码**"按钮。
3. 将显示一个确认页，显示用户的登录信息，包括临时密码。 务必打印或复制此信息，并将其提供给用户，因为你可能在离开此页面后无法访问临时密码。

## <a name="manage-groups"></a>管理组

组允许您一起控制多个用户角色和权限。

#### <a name="add-an-existing-group"></a>添加现有组

要将组织工作帐户（Azure AD 租户）中已存在的组添加到合作伙伴中心帐户：请执行：

1. 从 **"用户"** 页（在 **"帐户设置**"下）选择 **"添加组**"。
2. 从出现的列表中选择一个或多个组。 你可以使用搜索框来搜索特定组。
如果选择要添加到合作伙伴中心帐户的多个组，则必须为其分配相同的角色或自定义权限集。 要添加具有不同角色/权限的多个组，请为每个角色或一组自定义权限重复这些步骤。
3. 完成选择组后，单击"**添加所选**"。
4. 在**角色**部分中，为选定的组指定角色或自定义权限。 组的所有成员将能够访问您的合作伙伴中心帐户，您向该组申请的权限，而不考虑与其个人帐户关联的角色和权限。
5. 选择“保存”。****

添加现有组时，该组成员的每个用户将能够访问您的合作伙伴中心帐户，并具有与该组分配的角色关联的权限。

#### <a name="add-a-new-group"></a>添加新组

要向合作伙伴中心帐户添加全新组，请：

1. 从 **"用户"** 页（在 **"帐户设置**"下）选择 **"添加组**"。
2. 在下一页上，选择 **"新建组**"。
3. 输入新组的显示名称。
4. 为组指定角色或自定义权限。 无论与个人帐户关联的角色/权限如何，组所有成员都可以使用您在此处申请的权限访问您的合作伙伴中心帐户。
5. 从显示的列表中为新组选择用户。 你可以使用搜索框来搜索特定用户。
6. 完成选择用户后，请单击**添加选定项**将其添加到新组中。
7. 选择“保存”。****

请注意，此新组也将在组织的工作帐户（Azure AD 租户）中创建，而不仅仅是在合作伙伴中心帐户中。

#### <a name="remove-a-group"></a>删除组

要从工作帐户（Azure AD 租户）中删除组，请转到 **"用户**"（在 **"帐户设置"** 下），使用最右侧列中的复选框选择要删除的组，然后从可用操作中选择 **"删除**"。 将显示一个弹出窗口，以确认要删除选定的组。

## <a name="manage-azure-ad-applications"></a>管理 Azure AD 应用程序

您可以允许属于公司 Azure AD 的应用程序或服务访问合作伙伴中心帐户。

#### <a name="add-existing-azure-ad-applications"></a>添加现有的 Azure AD 应用程序

要添加公司 Azure 活动目录中已存在的应用程序，请执行：

1. 从 **"用户"** 页（在 **"帐户设置**"下）选择 **"添加 Azure AD 应用程序**"。
2. 从出现的列表中选择一个或多个 Azure AD 应用程序。 你可以使用搜索框来搜索特定的 Azure AD 应用程序。 如果选择要添加到合作伙伴中心帐户的多个 Azure AD 应用程序，则必须为其分配相同的角色或自定义权限集。 要添加具有不同角色/权限的多个 Azure AD 应用程序，请为每个角色或一组自定义权限重复这些步骤。
3. 选择完 Azure AD 应用程序后，单击"**添加选定的**"。
4. 在**角色**部分中，为选定的 Azure AD 应用程序指定角色或自定义权限。
5. 选择“保存”。****

#### <a name="add-new-azure-ad-applications"></a>添加新的 Azure AD 应用程序

如果要授予合作伙伴中心对全新 Azure AD 应用程序帐户的访问权限，可以在 **"用户"** 部分创建一个。 请注意，这将在公司工作帐户（Azure AD 租户）中创建新帐户，而不仅仅是在合作伙伴中心帐户中。 如果您主要使用此 Azure AD 应用程序进行合作伙伴中心身份验证，并且不需要用户直接访问它，则可以为**回复 URL** **和应用 ID URI**输入任何有效地址，只要这些值不由目录中的任何其他 Azure AD 应用程序使用。

1. 从 **"用户"** 页（在 **"帐户设置**"下）选择 **"添加 Azure AD 应用程序**"。
2. 在下一页上，选择**新的 Azure AD 应用程序**。
3. 输入新 Azure AD 应用程序的**回复 URL。** 这是用户可以登录并使用 Azure AD 应用程序的 URL（有时也称为“应用 URL”或“登录 URL”）。 **回复 URL**不能超过 256 个字符，且在你目录中必须是唯一的。
4. 输入新 Azure AD**应用程序的应用 ID URI。** 这是 Azure AD 应用程序的逻辑标识符，在将单个登录请求发送到 Azure AD 时显示。 请注意，对于目录中的每个 Azure AD 应用程序，**应用 ID URI**必须是唯一的。 此 ID 不能超过 256 个字符。 有关应用 ID URI 的详细信息，请参阅[将应用程序与 Azure 活动目录集成](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts)。
5. 在**角色**部分中，为 Azure AD 应用程序指定角色或自定义权限。
6. 选择“保存”。****

添加或创建 Azure AD 应用程序后，可以返回到**用户**部分，然后选择应用程序名称查看应用程序的设置，包括租户 ID、客户端 ID、回复 URL 和应用 ID URI。

#### <a name="remove-an-application"></a>删除应用程序

要从工作帐户（Azure AD 租户）中删除应用程序，请转到 **"用户**"（在 **"帐户设置"** 下），使用最右侧列中的复选框选择要删除的应用程序，然后从可用操作中选择 **"删除**"。 将显示一个弹出窗口，以确认要删除选定的应用程序。

#### <a name="manage-keys-for-an-azure-ad-application"></a>管理 Azure AD 应用程序的密钥

如果 Azure AD 应用程序在 Microsoft Azure AD 中读写数据，它将需要密钥。 通过在合作伙伴中心中编辑其信息，可以为 Azure AD 应用程序创建密钥。 还可以删除不再需要的密钥。

1. 从 **"用户"** 页（在 **"帐户设置**"下）选择 Azure AD 应用程序的名称。 您将看到 Azure AD 应用程序的所有活动密钥，包括创建密钥的日期和何时过期。 
2. 要删除不再需要的键，请选择 **"删除**"。
3. 要添加新键，请选择"**添加新键**"。
4. 您将看到一个屏幕，显示**客户端 ID**和**键值**。 请务必打印或复制此信息，因为离开此页面后将无法再次访问此信息。
5. 如果要创建更多键，请选择"**添加其他键**"。

## <a name="define-user-roles-and-permissions"></a>定义用户角色和权限

可以为合作伙伴中心上的商业市场计划分配以下角色和权限：

- **Manager**
  - 可以访问除税费和付款设置之外的所有 Microsoft 帐户功能
  - 可以管理用户、角色和工作帐户（租户）
- **开发人员**
  - 可以管理和发布优惠
  - 可以查看某些发布者报告

> [!NOTE]
> 对于商业市场计划，不使用全局管理员、业务贡献者、财务贡献者和营销人员角色。 将这些角色分配给用户不起作用。 只有管理器和开发人员角色向用户授予权限。

有关在合作伙伴中心的其他区域（如 Azure 活动目录 （AD）、云解决方案提供商 （CSP）、控制面板供应商 （CPV）、来宾用户或 Microsoft 合作伙伴网络 （MPN））中管理角色和权限的详细信息，请参阅[在合作伙伴中心中分配用户角色和权限](https://docs.microsoft.com/partner-center/permissions-overview)。

## <a name="manage-tenants"></a>管理租户

Azure 活动目录 （AD） 租户（在本文档中也称为"工作帐户"）是您在 Azure 门户中设置的组织的表示形式，可帮助您为内部管理 Microsoft 云服务的特定实例和外部用户。 如果您的组织订阅了 Microsoft 云服务（如 Azure、Microsoft Intune 或 Office 365），则为您建立了 Azure AD 租户。

您可以设置多个租户以与合作伙伴中心一起使用。 在合作伙伴中心帐户中具有**Manager**角色的任何用户都可以选择从帐户中添加和删除 Azure AD 租户。  

### <a name="add-an-existing-tenant"></a>添加现有租户

要将另一个 Azure AD 租户与您的合作伙伴中心帐户关联，请执行：

1. 从 **"租户"** 页（在 **"帐户设置**"下）选择 **"关联其他 Azure AD 租户**"。
2. 输入想要关联的租户 Azure AD 凭据。
3. 查看 Azure AD 租户的组织和域名。 若要完成关联，请选择**确认**。

如果关联成功，您将准备好在合作伙伴中心中的 **"用户"** 部分添加和管理帐户用户。

### <a name="create-a-new-tenant"></a>创建新租户

要使用合作伙伴中心帐户创建新的 Azure AD 租户，请执行：

1. 从 **"租户"** 页（在 **"帐户设置**"下）选择 **"创建新的 Azure AD 租户**"。
2. 输入新的 Azure AD 的目录信息：
    - **域名**：我们将用于 Azure AD 域的唯一名称，以及".onmicrosoft.com"。 例如，如果输入"示例"，则 Azure AD 域将为"example.onmicrosoft.com"。
    - **联系人电子邮件**：我们在必要时就帐户情况与你联系的电子邮件地址。
    - **全局管理员用户帐户信息**：要用于新全局管理员帐户的名字、姓氏、用户名和密码。
3. 选择 **"创建**"以确认新域和帐户信息。
4. 使用新的 Azure AD 全局管理员用户名和密码登录，开始[添加和管理用户](#manage-users)。

有关在 Azure 门户（而不是通过合作伙伴中心门户）中创建新租户的详细信息，请参阅在 Azure[活动目录中创建新租户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)的文章。

### <a name="remove-a-tenant"></a>删除租户

要从合作伙伴中心帐户中删除租户，请在 **"租户"** 页上查找其名称（在 **"帐户设置**"中），然后选择"**删除**"。 系统将提示您确认要删除租户。 执行此操作后，该租户中的任何用户都无法登录到合作伙伴中心帐户，并且您为这些用户配置的任何权限都将被删除。

删除租户时，从该租户添加到合作伙伴中心帐户的所有用户将不再能够登录到该帐户。

> [!TIP]
> 如果您当前使用同一租户中的帐户登录到合作伙伴中心，则无法删除租户。 要删除租户，您必须以与帐户关联的其他租户**的经理**身份登录到合作伙伴中心。 如果该帐户只关联了一个租户，则只有登录打开该帐户的 Microsoft 帐户后才能删除该租户。

## <a name="agreements"></a>协议

合作伙伴中心**的协议**部分（在**帐户设置**下）允许您查看您授权的发布协议的列表。 这些协议按名称和版本号列出，包括接受协议的日期和接受协议的用户的名称。

如果需要**的操作**可能会显示在此页面顶部，如果有需要您注意的协议更新。 要接受更新的协议，请先阅读链接的协议版本，然后选择 **"接受协议**"。

## <a name="next-steps"></a>后续步骤

- [创建新的 SaaS 套餐](./create-new-saas-offer.md)
