---
title: 根据需要组织发票-Azure
description: 了解如何在发票上组织成本。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991056"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>通过自定义计费帐户来组织成本

你的 Microsoft 客户协议计费帐户使你可以根据需要灵活地组织成本，无论是按部门、项目还是开发环境。 

本文介绍如何使用 Azure 门户来组织成本。 它适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>为帐户构建计费配置文件和发票部分

在 Microsoft 客户协议的计费帐户中，你可以使用帐单配置文件和发票部分来组织你的成本。

![显示 Microsoft 客户协议计费层次结构的屏幕截图](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>计费配置文件

计费配置文件表示发票和相关计费信息，例如支付方法和帐单地址。 每月发票是在当月开始时针对帐户中的每项计费对象信息生成的。 此发票包含上个月的 Azure 使用情况和其他购买费用。

注册 Azure 时，会自动创建计费配置文件和计费帐户。 您可以创建其他计费配置文件以在多个月度发票中组织成本。 

> [!IMPORTANT]
>
> 创建其他计费配置文件可能会影响你的总体成本。 有关详细信息，请参阅[添加新计费配置文件时要考虑的事项](#things-to-consider-when-adding-new-billing-profiles)。

### <a name="invoice-section"></a>发票科目

发票部分表示发票中的一组费用。 系统会自动为帐户中的每个计费配置文件创建一个发票部分。 您可以根据需要创建其他部分来组织成本。 每个发票部分都显示在发票上，并显示该月的费用。 

下图显示了一个发票，其中包含两个发票部分-"工程" 和 "营销"。 每个部分的摘要和详细费用显示在发票中。 图中所示的价格仅用于举例，不表示 Azure 服务的实际价格。 

![显示含部分发票的图像](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>常见方案的计费帐户结构

本部分介绍了用于组织成本和相应计费帐户结构的常见方案：

|方案  |结构  |
|---------|---------|
|插孔注册 Azure，需要一个月度发票。 | 计费配置文件和发票部分。 当用户注册 Azure 时，此结构会自动设置为插孔，无需任何其他步骤。 |

![简单计费方案的信息图形](./media/mca-section-invoice/organize-billing-scenario1.png)

|方案  |结构  |
|---------|---------|
|Contoso 是一家小型组织，需要单个月度发票，但按部门的部门（营销和财务）对成本进行分组。  | Contoso 的帐单配置文件和发票部分每个用于市场营销和财务部门。 |

![简单计费方案的信息图形](./media/mca-section-invoice/organize-billing-scenario2.png)

|方案  |结构  |
|---------|---------|
|Fabrikam 是一家中型组织，需要为其工程部门和营销部门单独提供发票。 对于工程部门，他们希望按环境（生产和开发）对成本进行分组。  | 每个用于市场营销和财务部门的计费配置文件。 对于市场营销部门，每个发票部分用于生产和开发环境。 |

![简单计费方案的信息图形](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>创建新发票部分

若要创建发票科目，需要是**计费对象信息所有者**或**计费对象信息参与者**。 有关详细信息，请参阅[管理计费配置文件的发票部分](understand-mca-roles.md#manage-invoice-sections-for-billing-profile)。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![显示在门户中搜索“成本管理 + 计费”的屏幕截图](./media/mca-section-invoice/search-cmb.png)

3. 从左侧窗格中选择 "**计费配置文件**"。 从列表中选择一个计费配置文件。 新节将显示在所选计费配置文件的发票上。 

   [显示计费配置文件列表的 ![屏幕快照](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. 从左侧窗格中选择 "**发票部分**"，然后从页面顶部选择 "**添加**"。

   [显示添加发票部分的 ![屏幕快照](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. 输入发票部分的名称。 

   [显示发票部分创建页面的 ![屏幕快照](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. 选择“创建”。

## <a name="create-a-new-billing-profile"></a>创建新的计费配置文件

若要创建计费配置文件，您需要是**帐单帐户所有者**或**计费帐户参与者**。 有关详细信息，请参阅[管理计费帐户的计费配置文件](understand-mca-roles.md#manage-billing-profiles-for-billing-account)。

> [!IMPORTANT]
>
> 创建其他计费配置文件可能会影响你的总体成本。 有关详细信息，请参阅[添加新计费配置文件时要考虑的事项](#things-to-consider-when-adding-new-billing-profiles)。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![显示在门户中搜索“成本管理 + 计费”的屏幕截图](./media/mca-section-invoice/search-cmb.png)

3. 从左侧窗格中选择 "**计费配置文件**"，然后从页面顶部选择 "**添加**"。

   [显示计费配置文件列表的 ![屏幕快照](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > 如果未在 "帐单配置文件" 页中看到 "添加" 按钮，则该功能对你的帐户不可用。 目前，它仅适用于在使用 Microsoft 代表时设置的帐户。

4. 填充表单，并单击“创建”。

   [显示计费配置文件创建页面的 ![屏幕快照](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |字段  |定义  |
    |---------|---------|
    |名称     | 一种显示名称，可帮助你轻松识别 Azure 门户中的计费配置文件。  |
    |PO 号    | 可选的采购订单号。 采购订单编号将显示在为计费配置文件生成的发票上。 |
    |帐单地址   | 计费地址将显示在为计费配置文件生成的发票上。 |
    |电子邮件发票   | 选中 "电子邮件发票" 框以通过电子邮件接收此计费配置文件的发票。 如果你不选择加入，则可以在 Azure 门户中查看和下载发票。|

5. 选择“创建”。

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>将费用链接到发票部分和计费配置文件

根据需要自定义了计费帐户后，可以将订阅和其他产品链接到所需发票部分和计费配置文件。

### <a name="link-a-new-subscription"></a>链接新订阅

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“订阅”。

   [显示订阅的门户中搜索的 ![屏幕快照](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. 从页面顶部选择“添加”。

   ![其中显示了“订阅”视图中的“添加”按钮的屏幕截图](./media/mca-section-invoice/subscription-add.png)

4. 如果有权访问多个计费帐户，请选择你的 Microsoft 客户协议计费帐户。

   ![其中显示了“订阅”视图中的“添加”按钮的屏幕截图](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. 选择将按订阅使用情况计费的计费配置文件。 针对此订阅的 Azure 使用情况和其他购买费用将按所选计费配置文件的发票进行计费。

6. 选择 "发票" 部分以链接订阅的费用。 此部分将在帐单配置文件的发票上显示该费用。

7. 选择 Azure 计划并输入订阅的友好名称。 

9. 单击“创建”。  

### <a name="link-existing-subscriptions-and-products"></a>链接现有订阅和产品

如果现有的 Azure 订阅或其他产品（例如 Azure Marketplace 和应用源资源），可以将它们从现有发票部分移到另一个发票部分，以重新组织成本。 

> [!IMPORTANT]
>
> 订阅和其他产品只能在属于相同计费配置文件的发票部分之间移动。 不支持在不同计费配置文件中跨发票部分移动订阅和产品。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![其中显示了在门户中搜索订阅的屏幕截图](./media/mca-section-invoice/search-cmb.png)

3. 若要将订阅链接到新发票部分，请从屏幕左侧选择 " **Azure 订阅**"。 对于其他产品（例如 Azure Marketplace 和应用源资源），请选择 "**定期收费**"。

   [显示更改发票部分选项的 ![屏幕快照](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. 在该页中，单击要链接到新发票部分的订阅或产品的省略号（三个点）。 选择 "**更改发票" 部分**。

5. 从下拉列表中选择 "新建发票" 部分。 下拉列表将仅显示与现有发票部分的相同计费配置文件关联的发票部分。

    [显示选择新发票部分的 ![屏幕快照](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. 选择“保存”。

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>添加新计费配置文件时要考虑的事项

### <a name="azure-usage-charges-may-be-impacted"></a>Azure 使用费用可能会受到影响

在你的 Microsoft 客户协议的计费帐户中，每月会为每个计费配置文件聚合 Azure 使用情况。 使用分层定价的 Azure 资源的价格是根据每个计费配置文件的使用情况确定的。 计算价格时，不会跨计费配置文件聚合使用量。 这可能会影响使用多个计费配置文件的帐户的 Azure 使用情况的总体成本。

我们来看一个示例，了解两个方案的成本如何变化。 在方案中使用的价格仅用于举例，不表示 Azure 服务的实际价格。

#### <a name="you-only-have-one-billing-profile"></a>只有一个计费配置文件。

假设你使用的是 Azure 块 blob 存储，它的成本为第一个 50 tb （TB），每 gb 00184 美元，再到 1 450 GB 00177。 你在订阅中使用了 100 TB，这些订阅将计费到你的计费配置文件，以下是你需要支付的费用。

|  层定价（美元） |数量 | 金额（美元）|
|---------|---------|---------|
|前 50 TB/月每 TB 1.84    | 50 TB        | 92.0   |
|下 450 TB/月每 TB 1.77    |  50 TB         | 88.5   |
|总计     |     100 TB  | 180.5

在这种情况下，使用 100 TB 数据的总费用为**180.5**

#### <a name="you-have-multiple-billing-profiles"></a>你有多个计费配置文件。

现在，假设你创建了另一个计费配置文件，并使用了 50 GB 通过订阅（通过第二个计费配置文件计费的订阅，以及 50 GB），这是你需要支付的费用。

`Charges for the first billing profile`

|  层定价（美元） |数量 | 金额（美元）|
|---------|---------|---------|
|前 50 TB/月每 TB 1.84    | 50 TB        | 92.0  |
|下 450 TB/月每 TB 1.77    |  0 TB         | 0.0  |
|总计     |     50 TB  | 92.0 

`Charges for the second billing profile`

|  层定价（美元） |数量 | 金额（美元）|
|---------|---------|---------|
|前 50 TB/月每 TB 1.84    | 50 TB        | 92.0  |
|下 450 TB/月每 TB 1.77    |  0 TB         | 0.0  |
|总计     |     50 TB  | 92.0 

在这种情况下，使用 100 TB 数据的总费用为**184.0** （92.0 * 2）。

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Azure 预订权益可能不适用于所有订阅

使用共享作用域的 Azure 保留项适用于单个计费配置文件中的订阅，不在计费配置文件之间共享。 

![不同计费帐户结构的预订应用程序的信息图](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

在上图中，Contoso 有两个订阅。 根据计费帐户的构建方式，Azure 预订权益的应用方式有所不同。 在左侧的方案中，预订权益适用于每个订阅都计费到工程帐单配置文件。 在右侧的方案中，预订权益将仅应用于订阅1，因为这是唯一向工程计费配置文件计费的订阅。 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [为 Microsoft 客户协议创建其他 Azure 订阅](create-subscription.md)
- [在 Azure 门户中管理计费角色](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [从其他计费帐户中的用户获取 Azure 订阅的计费所有权](mca-request-billing-ownership.md)
