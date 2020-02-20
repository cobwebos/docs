---
title: 根据需要组织发票 - Azure
description: 了解如何在发票中组织成本。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: c9cb1d7d1dcc6e7872b22d8c58fe44b9bce25f13
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200738"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>通过自定义计费帐户来组织成本

Microsoft 客户协议的计费帐户可让你根据需要（按部门、项目或开发环境）灵活组织成本。

本文介绍如何使用 Azure 门户来组织成本。 本文的内容适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>使用计费配置文件和发票科目构建帐户

在 Microsoft 客户协议的计费帐户中，可以使用计费配置文件和发票科目来组织成本。

![显示 Microsoft 客户协议计费层次结构的屏幕截图](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>计费配置文件

计费配置文件代表发票和相关的计费信息（例如付款方式和计费地址）。 每月发票是在当月开始时针对帐户中的每项计费对象信息生成的。 发票包含上个月的 Azure 使用费以及其他购买项的费用。

注册 Azure 时，会自动创建计费配置文件和计费帐户。 可以创建更多的计费配置文件，以便在多份月度发票中组织成本。

> [!IMPORTANT]
>
> 创建更多计费配置文件可能会影响总体成本。 有关详细信息，请参阅[添加新计费配置文件时要考虑的事项](#things-to-consider-when-adding-new-billing-profiles)。

### <a name="invoice-section"></a>发票科目

发票科目代表发票中成本的分组。 系统会自动为帐户中的每个计费配置文件创建一个发票科目。 可以根据需要创建其他科目来组织成本。 每个发票科目显示在发票上，其中包括当月产生的费用。

下图显示了包含两个发票科目（“工程”和“营销”）的发票。 每个科目的摘要和详细费用显示在发票中。 图中所示的价格仅为示例，而不代表 Azure 服务的实际价格。

![显示包含科目的发票的插图](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>常见场景的计费帐户结构

本部分介绍组织成本和相应计费帐户结构的常见场景：

|场景  |结构  |
|---------|---------|
|Jack 注册了 Azure，他需要一份月度发票。 | 一个计费配置文件和一个发票科目。 当 Jack 注册 Azure 时，系统会自动为他设置此结构，不需要他执行任何附加步骤。 |

![简单计费场景的信息图](./media/mca-section-invoice/organize-billing-scenario1.png)

|场景  |结构  |
|---------|---------|
|Contoso 是一家小型组织，需要一份月度发票，但需要按部门（营销和工程）将成本分组。  | Contoso 的计费配置文件，营销和工程部门各有一个发票科目。 |

![简单计费场景的信息图](./media/mca-section-invoice/organize-billing-scenario2.png)

|场景  |结构  |
|---------|---------|
|Fabrikam 是一家中型组织，需要为其工程部门和营销部门单独提供发票。 对于工程部门，他们希望按环境（生产和开发）将成本分组。  | 营销和工程部门各有一个计费配置文件。 对于工程部门，生产和开发环境各有一个发票科目。 |

![简单计费场景的信息图](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>创建新的发票科目

若要创建发票科目，需要是**计费对象信息所有者**或**计费对象信息参与者**。 有关详细信息，请参阅[管理计费配置文件的发票科目](understand-mca-roles.md#manage-invoice-sections-for-billing-profile)。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示在门户中搜索“成本管理 + 计费”的屏幕截图](./media/mca-section-invoice/search-cmb.png)

3. 在左侧窗格中选择“计费配置文件”。  在列表中选择一个计费配置文件。 新科目将显示在所选计费配置文件的发票上。

   [![显示计费配置文件列表的屏幕截图](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. 在左侧窗格中选择“发票科目”，然后在页面顶部选择“添加”。  

   [![演示如何添加发票科目的屏幕截图](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. 输入发票科目的名称。

   [![显示发票科目创建页的屏幕截图](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. 选择“创建”  。

## <a name="create-a-new-billing-profile"></a>创建新的计费配置文件

只有**计费帐户所有者**或**计费帐户参与者**才能创建计费配置文件。 有关详细信息，请参阅[管理计费帐户的计费配置文件](understand-mca-roles.md#manage-billing-profiles-for-billing-account)。

> [!IMPORTANT]
>
> 创建更多计费配置文件可能会影响总体成本。 有关详细信息，请参阅[添加新计费配置文件时要考虑的事项](#things-to-consider-when-adding-new-billing-profiles)。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示在门户中搜索“成本管理 + 计费”的屏幕截图](./media/mca-section-invoice/search-cmb.png)

3. 在左侧窗格中选择“计费配置文件”，然后在页面顶部选择“添加”。  

   [![显示计费配置文件列表的屏幕截图](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > 如果“计费配置文件”页中未显示“添加”按钮，则表示该功能对你的帐户不可用。 目前，只有在 Microsoft 代表的配合下设置的帐户才能使用该功能。

4. 填充表单，并单击“创建”  。

   [![显示计费配置文件创建页的屏幕截图](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |字段  |定义  |
    |---------|---------|
    |名称     | 可帮助你在 Azure 门户中轻松识别该计费配置文件的显示名称。  |
    |PO 编号    | 可选的采购订单编号。 PO 编号将显示在为计费配置文件生成的发票上。 |
    |帐单地址   | 帐单地址将显示在为计费配置文件生成的发票上。 |
    |通过电子邮件发送发票   | 选中“通过电子邮件发送发票”框可以通过电子邮件接收此计费配置文件的发票。 如果不选择此项，可以在 Azure 门户中查看和下载发票。|

5. 选择“创建”  。

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>将费用链接到发票科目和计费配置文件

根据需要自定义计费帐户后，可将订阅和其他产品链接到所需的发票科目和计费配置文件。

### <a name="link-a-new-subscription"></a>链接新订阅

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“订阅”。 

   [![演示如何在门户中搜索订阅的屏幕截图](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. 从页面顶部选择“添加”。 

   ![其中显示了“订阅”视图中的“添加”按钮的屏幕截图](./media/mca-section-invoice/subscription-add.png)

4. 如果你有权访问多个计费帐户，请选择自己的 Microsoft 客户协议计费帐户。

   ![其中显示了“订阅”视图中的“添加”按钮的屏幕截图](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. 选择用于计收订阅使用费的计费配置文件。 Azure 使用费以及此订阅的其他购买费用将从所选计费配置文件的发票中计收。

6. 选择发票科目以链接订阅费用。 费用将显示在计费配置文件发票上的此科目下。

7. 选择 Azure 计划，并输入订阅的易记名称。

9. 单击“创建”。   

### <a name="link-existing-subscriptions-and-products"></a>链接现有的订阅和产品

如果你有现有的 Azure 订阅或其他产品（例如 Azure 市场和应用源资源），可将其从现有发票科目移到另一个发票科目，以重新组织成本。

> [!IMPORTANT]
>
> 只能在属于同一计费配置文件的发票科目之间移动订阅和其他产品。 不支持在不同计费配置文件中的发票科目之间移动订阅和产品。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![其中显示了在门户中搜索订阅的屏幕截图](./media/mca-section-invoice/search-cmb.png)

3. 若要将订阅链接到新的发票科目，请在屏幕的左侧选择“Azure 订阅”。  对于其他产品（例如 Azure 市场和应用源资源），请选择“重复性费用”。 

   [![显示用于更改发票科目的选项的屏幕截图](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. 在页面中，单击要链接到新发票科目的订阅或产品旁边的省略号（三个点）。 选择“更改发票科目”。 

5. 从下拉列表中选择新的发票科目。 下拉列表中只会显示已关联到现有发票科目所属的同一计费配置文件的发票科目。

    [![演示如何选择新发票科目的屏幕截图](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. 选择“保存”。 

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>添加新计费配置文件时要考虑的事项

### <a name="azure-usage-charges-may-be-impacted"></a>可能会影响 Azure 使用费

在 Microsoft 客户协议的计费帐户中，每月会聚合每个计费配置文件的 Azure 用量。 采用分层定价的 Azure 资源的价格是根据每个计费配置文件的用量单独确定的。 计算价格时，不会聚合各个计费配置文件的用量。 对于包含多个计费配置文件的帐户，这可能会影响总体的 Azure 使用成本。

让我们通过一个示例来了解这两种场景下的成本差异。 场景中使用的价格仅为示例，而不代表 Azure 服务的实际价格。

#### <a name="you-only-have-one-billing-profile"></a>只有一个计费配置文件。

假设你使用的是 Azure 块 Blob 存储，在前 50 TB 中，每 GB 的成本为 0.00184 美元；在随后的 450 TB 中，每 GB 成本为 0.00177 美元。 你在订阅中使用了 100 TB，其费用将从你的计费配置文件中计收。下面是需要支付的费用。

|  分层定价（美元） |数量 | 金额（美元）|
|---------|---------|---------|
|前 50 TB 中每 TB 费用为 1.84/月    | 50 TB        | 92.0   |
|后 450 TB 中每 TB 费用为 1.77/月    |  50 TB         | 88.5   |
|总计     |     100 TB  | 180.5

在此场景中，使用 100 TB 数据的总费用为 **180.5**

#### <a name="you-have-multiple-billing-profiles"></a>有多个计费配置文件。

现在，假设你创建了另一个计费配置文件。通过订阅使用的 50 GB 将从第一个计费配置文件计收，通过订阅使用的另外 50 GB 将从第二个计费配置文件计收。下面是需要支付的费用。

`Charges for the first billing profile`

|  分层定价（美元） |数量 | 金额（美元）|
|---------|---------|---------|
|前 50 TB 中每 TB 费用为 1.84/月    | 50 TB        | 92.0  |
|后 450 TB 中每 TB 费用为 1.77/月    |  0 TB         | 0.0  |
|总计     |     50 TB  | 92.0

`Charges for the second billing profile`

|  分层定价（美元） |数量 | 金额（美元）|
|---------|---------|---------|
|前 50 TB 中每 TB 费用为 1.84/月    | 50 TB        | 92.0  |
|后 450 TB 中每 TB 费用为 1.77/月    |  0 TB         | 0.0  |
|总计     |     50 TB  | 92.0

在此场景中，使用 100 TB 数据的总费用为 **184.0** (92.0 * 2)。

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Azure 预留权益可能不会应用到所有订阅

具有共享范围的 Azure 预留将应用到单个计费配置文件中的订阅，且不在计费配置文件之间共享。

![不同计费帐户结构的预留应用信息图](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

在上图中，Contoso 有两个订阅。 将会根据计费帐户的构建方式，以不同的方式应用 Azure 预留权益。 在左侧的场景中，预留权益将应用到从工程计费配置文件计费的两个订阅。 在右侧的场景中，预留权益只会应用到订阅 1，因为该订阅是唯一一个从工程计费配置文件计费的订阅。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [为 Microsoft 客户协议创建其他 Azure 订阅](create-subscription.md)
- [在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [从其他计费帐户中的用户获取 Azure 订阅的计费所有权](mca-request-billing-ownership.md)
