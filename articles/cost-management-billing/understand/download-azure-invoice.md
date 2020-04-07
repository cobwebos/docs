---
title: 查看和下载 Azure 发票
description: 介绍如何查看和下载 Azure 发票。
keywords: 帐单发票, 发票下载, azure 发票, azure 使用情况
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: banders
ms.openlocfilehash: 16534343a831f0802a60a9214f567742153360e0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479006"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>查看和下载 Microsoft Azure 发票

可以在 [Azure 门户](https://portal.azure.com/)中下载发票，或者让我们通过电子邮件发送发票。 如果你是签署了企业协议的 Azure 客户（EA 客户），则无法下载你的组织的发票。 发票将发送到已设置为针对该注册接收发票的人员。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="when-invoices-are-generated"></a>何时生成发票

发票是根据计费帐户类型生成的。 系统会为 Microsoft 在线服务计划 (MOSP)、Microsoft 客户协议 (MCA) 和 Microsoft 合作伙伴协议 (MPA) 计费帐户创建发票。 此外，还会为企业协议 (EA) 计费帐户生成发票。 但是，EA 计费帐户的发票不会在 Azure 门户中显示。

若要详细了解计费帐户并确定自己的计费帐户类型，请参阅[在 Azure 门户中查看计费帐户](../manage/view-all-accounts.md)。

## <a name="invoices-for-mosp-billing-accounts"></a>MOSP 计费帐户的发票

当你通过 Azure 网站注册 Azure 时，会创建一个 MOSP 计费帐户。 例如，当你注册 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)、[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或作为 [Visual Studio 订阅者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)时。

特定区域中的客户（通过 Azure 网站注册[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)）可以获得 MCA 的计费帐户。

如果你不确定自己的计费帐户类型，请先参阅[检查自己的计费帐户类型](../manage/view-all-accounts.md#check-the-type-of-your-account)，然后再按照本文中的说明操作。 

MOSP 计费帐户可获得以下发票：

**Azure 服务费** - 系统将为包含订阅所用 Azure 资源的每个 Azure 订阅生成一份发票。 该发票包含某个计费周期内的费用。 计费周期根据创建订阅时的月份日期确定。

例如，John 在 3 月 5 日创建了“Azure 订阅 1”，在 3 月 10 日创建了“Azure 订阅 2”。   “Azure 订阅 1”的发票将包含当月 5 日到下月 4 日的费用。  “Azure 订阅 2”的发票将包含当月 10 日到下月 9 日的费用。  所有 Azure 订阅的发票通常是在创建帐户时的月份日期生成的，但最多可能会推迟两天。 在此示例中，如果 John 在 2 月 2 日创建了他的帐户，则通常会在每月的 2 日生成“Azure 订阅 1”和“Azure 订阅 2”的发票，但最多可能会推迟两天生成发票。  

**Azure 市场产品、预留项和现成 VM** - 为预留项、市场产品以及通过订阅购买的现成 VM 生成发票。 该发票显示上个月的各项费用。 例如，John 在 3 月 1 日购买了某个预留项，在 3 月 30 日购买了另一个预留项。 在 4 月将为这两个预留项生成一份发票。 Azure 市场产品、预留项和现成 VM 的发票始终在当月的 9 日左右生成。

如果你使用信用卡支付 Azure 费用，并购买了预留项，则 Azure 会生成即时发票。 但是，在按发票计费时，将在下一份月度发票上向你收取预留费。

**Azure 支持计划** - 每月生成支持计划订阅的发票。 第一份发票将在购买当日生成，最多会推迟两天。 后续的支持计划发票通常在创建帐户时的同一月份日期生成，但最多可能会推迟两天生成。

## <a name="download-your-mosp-azure-subscription-invoice"></a>下载 MOSP Azure 订阅发票

只会为属于 MOSP 计费帐户的订阅生成发票。 [检查是否有权访问 MOSP 帐户](../manage/view-all-accounts.md#check-the-type-of-your-account)。 

若要下载某个订阅的发票，必须对该订阅具有帐户管理员角色。 对于具有所有者、参与者或读取者角色的用户，如果帐户管理员已向他们授予权限，则他们也可以下载该发票。 有关详细信息，请参阅[允许用户下载发票](../manage/manage-billing-access.md#opt-in)。

1. 在 Azure 门户上的[“订阅”页](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中选择你的订阅。
1. 在计费部分选择“发票”。   
    ![显示用户选择订阅发票选项的屏幕截图](./media/download-azure-invoice/select-subscription-invoice.png)
1. 选择“下载”以下载 PDF 版本的发票，然后在发票部分下选择“下载”。    
    [![显示计费周期、下载选项以及每个计费周期的总费用的屏幕截图](./media/download-azure-invoice/download-invoice-subscription.png)](./media/download-azure-invoice/download-invoice-subscription-zoomed.png#lightbox)
1. 还可以通过在使用情况详细信息部分下选择“下载”，来下载使用数量和费用的每日细目。  准备 CSV 文件可能需要几分钟时间。  
    ![显示“下载发票和使用情况”页的屏幕截图](./media/download-azure-invoice/usage-and-invoice-subscription.png)

有关发票的详细信息，请参阅[了解 Microsoft Azure 帐单](../understand/review-individual-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 账单和成本管理来防止意外成本](../manage/getting-started.md)。

## <a name="download-your-mosp-support-plan-invoice"></a>下载 MOSP 支持计划发票

只会为属于 MOSP 计费帐户的支持计划订阅生成发票。 [检查是否有权访问 MOSP 帐户](../manage/view-all-accounts.md#check-the-type-of-your-account)。 

若要下载支持计划订阅的发票，必须对该订阅具有帐户管理员角色。

1.  登录 [Azure 门户](https://portal.azure.com)。
1.  搜索“成本管理 + 计费”。   
    ![显示如何在门户中搜索“成本管理 + 计费”的屏幕截图](./media/download-azure-invoice/search-cmb.png)
1.  在左侧选择“发票”。 
1.  选择你的支持计划订阅，然后选择“下载”。   
    [![显示计费配置文件列表的屏幕截图](./media/download-azure-invoice/cmb-invoices.png)](./media/download-azure-invoice/cmb-invoices-zoomed-in.png#lightbox)
1.  选择“下载”以下载 PDF 版本的发票。   
    ![显示计费周期、下载选项以及每个计费周期的总费用的屏幕截图](./media/download-azure-invoice/download-invoice-support-plan.png)

## <a name="allow-others-to-download-an-mosp-subscription-invoice"></a>允许其他人下载 MOSP 订阅发票

PDF 版本的发票包含有关帐户管理员的个人信息，因此，此订阅的帐户管理员必须向其他用户授予下载发票的权限。 授予权限后，具有相应角色的以下人员和用户可以下载订阅发票：

- 用户
- 组
- 作为所有者的服务主体
- 参与者
- 读取器
- 用户访问管理员
- 计费读者
- 共同管理员
- 服务管理员

若要下载发票：

1.  以订阅帐户管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
1.  搜索“成本管理 + 计费”。   
1.  在左侧选择“发票”。 
1.  选择你的 Azure 订阅，并选择“访问发票”。   
    [![显示如何选择“访问发票”的屏幕截图](./media/download-azure-invoice/cmb-select-access-to-invoice.png)](./media/download-azure-invoice/cmb-select-access-to-invoice-zoomed-in.png#lightbox)
1.  选择“打开”，然后在页面顶部选择“保存”。    
    ![显示如何对“访问发票”选择“开”的屏幕截图](./media/download-azure-invoice/cmb-access-to-invoice.png)

## <a name="get-mosp-subscription-invoice-in-email"></a>通过电子邮件获取 MOSP 订阅发票

若要选择通过电子邮件接收某个订阅或支持计划的发票，必须对该订阅或支持计划具有帐户管理员角色。 电子邮件发票仅适用于订阅和支持计划，而不适用于预留项或 Azure 市场产品购买。 选择启用此接收方式后，可以添加更多的收件人，这样，他们也可以通过电子邮件收到发票。

1.  登录 [Azure 门户](https://portal.azure.com)。
1.  搜索“成本管理 + 计费”。   
1.  在左侧选择“发票”。 
1.  选择你的 Azure 订阅或支持计划订阅，然后选择“通过电子邮件发送发票”。   
    [![显示计费配置文件列表的屏幕截图](./media/download-azure-invoice/cmb-email-invoice.png)](./media/download-azure-invoice/cmb-email-invoice-zoomed-in.png#lightbox)
1. 选择“选择启用”并接受条款。   
    ![显示“选择启用”流程步骤 2 的屏幕截图](./media/download-azure-invoice/invoice-article-step02.png)
1. 发票将发送到首选的通讯电子邮件地址。 可以在[计费帐户的联系信息](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/Properties)中更新电子邮件地址。  
    ![显示“选择启用”流程步骤 3 的屏幕截图](./media/download-azure-invoice/invoice-article-step03-verify-email.png)

## <a name="share-your-mosp-invoices-with-others-by-email"></a>通过电子邮件与其他人共享 MOSP 发票

你可能需要每月与会计团队共享订阅和支持计划的发票，或者需要将发票发送到其他某个电子邮件地址。

1. 请按照[通过电子邮件获取订阅和支持计划的发票](#get-mosp-subscription-invoice-in-email)中的步骤操作，并选择“配置收件人”。   
    ![显示用户选择“配置收件人”的屏幕截图](./media/download-azure-invoice/invoice-article-step03.png)
1. 输入电子邮件地址，然后选择“添加收件人”。  可以添加多个电子邮件地址。  
    ![显示用户添加更多收件人的屏幕截图](./media/download-azure-invoice/invoice-article-step04.png)
1. 添加所有电子邮件地址后，在屏幕底部选择“完成”。 

## <a name="invoices-for-mca-and-mpa-billing-accounts"></a>MCA 和 MPA 计费帐户的发票

当组织与 Microsoft 代表一起签署 MCA 时，会创建一个 MCA 计费帐户。 特定区域中的某些客户（通过 Azure 网站注册[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)）也可以获得 MCA 的计费帐户。 有关详细信息，请参阅[开始使用 MCA 计费帐户](../understand/mca-overview.md)。

MPA 计费帐户是为云解决方案提供商 (CSP) 合作伙伴创建的，用于在新的商务体验中管理其客户。 合作伙伴至少需要有一个客户拥有 [Azure 计划](https://docs.microsoft.com/partner-center/purchase-azure-plan)，才能在 Azure 门户中管理其计费帐户。 有关详细信息，请参阅[开始使用 MPA 计费帐户](../understand/mpa-overview.md)。

每月发票是在当月开始时针对帐户中的每项计费配置文件生成的。 发票包含上月的所有 Azure 订阅和其他购买项目的相应费用。 例如，John 在 3 月 5 日创建了“Azure 订阅 1”，在 3 月 10 日创建了“Azure 订阅 2”。   他在 3 月 28 日使用“计费配置文件 1”购买了“Azure 支持计划 1”订阅。   John 将在 4 月初收到一份发票，其中包含 Azure 订阅和支持计划的费用。

##  <a name="download-an-mca-or-mpa-billing-profile-invoice"></a>下载 MCA 或 MPA 计费配置文件发票

若要在 Azure 门户中下载某个计费配置文件的发票，必须对该计费配置文件具有所有者、参与者、读取者或发票管理者角色。 对某个计费帐户具有所有者、参与者或读取者角色的用户可以下载该帐户中所有计费配置文件的发票。

1.  登录 [Azure 门户](https://portal.azure.com)。
1.  搜索“成本管理 + 计费”。   
1. 在左侧选择“发票”。 
    [![显示 MCA 计费帐户的发票页的屏幕截图](./media/download-azure-invoice/mca-billing-profile-invoices.png)](./media/download-azure-invoice/mca-billing-profile-invoices-zoomed.png#lightbox)
1. 在“发票”表中，选择要下载的发票。
1. 在页面顶部选择“下载发票 PDF”。   
    ![显示“下载发票 PDF”的屏幕截图](./media/download-azure-invoice/mca-billing-profile-download-invoice.png)
1. 还可以通过选择“下载 Azure 使用情况”，下载包含使用数量和估算费用的每日细目的 CSV 文件。  准备 CSV 文件可能需要几分钟时间。

## <a name="get-your-billing-profiles-invoice-in-email"></a>通过电子邮件获取计费配置文件的发票

若要更新计费配置文件或其计费帐户的电子邮件发票首选项，必须对此计费配置文件或计费帐户具有所有者或参与者角色。 选择启用后，对帐单配置文件具有所有者、参与者、读取者和发票管理者角色的所有用户都会在电子邮件中获取其发票。 

1.  登录 [Azure 门户](https://portal.azure.com)。
1.  搜索“成本管理 + 计费”。   
1.  在左侧选择“发票”，然后在页面顶部选择“通过电子邮件发送发票”。    
    [![显示 MCA 计费帐户的发票页的屏幕截图](./media/download-azure-invoice/mca-billing-profile-select-email-invoice.png)](./media/download-azure-invoice/mca-billing-profile-select-email-invoice-zoomed.png#lightbox)
1.  如果你有多个计费配置文件，请选择其中的一个，然后选择“选择启用”。   
    ![显示 MCA 计费帐户的发票页的屏幕截图](./media/download-azure-invoice/mca-billing-profile-email-invoice.png)
1.  选择“更新”  。

## <a name="give-others-access-to-mca-or-mpa-invoices"></a>允许其他人访问 MCA 或 MPA 发票

若要使其他人有权查看、下载和支付发票，可为他们分配对 MCA 或 MPA 计费配置文件的发票管理者角色。 如果你已选择通过电子邮件获取发票，则这些用户也会通过电子邮件收到发票。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。   
1. 在左侧选择“计费配置文件”。  从计费配置文件列表中，选择要为其分配发票管理者角色的计费配置文件。  
   ![显示计费配置文件列表的屏幕截图](./media/download-azure-invoice/mca-select-profile-zoomed-in.png)
1. 从左侧选择“访问控制(IAM)”  ，然后从页面顶部选择“添加”。   
    ![显示“访问控制”页的屏幕截图](./media/download-azure-invoice/mca-select-access-control-zoomed-in.png)
1. 在“角色”下拉列表中，选择“发票管理者”  。 输入要向其授予访问权限的用户的电子邮件地址。 选择“保存”以分配该角色。   
   ![显示如何将用户添加为发票管理者的屏幕截图](./media/download-azure-invoice/mca-added-invoice-manager.png)

## <a name="why-you-might-not-see-an-invoice"></a><a name="noinvoice"></a> 为何可能会看不到发票

看不到发票的几个可能原因：

- 从订阅 Azure 开始算还不到 30 天。 
- Azure 在计费周期结束后的几天才开始生成帐单。 因此，可能尚未生成发票。
- 你无权查看发票。
    如果你有 MCA 或 MPA 计费帐户，必须对计费配置文件具有“所有者”、“参与者”、“读取者”或“发票管理者”角色，或者对该计费帐户具有“所有者”、“参与者”或“读取者”角色，才能查看发票。 对于其他订阅，如果你不是帐户管理员，则可能看不到发票。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](../manage/manage-billing-access.md)。
- 如果你有 MOSP 计费帐户，并注册了带有每月额度的 Azure 免费帐户或订阅，则仅当超过了每月额度时，才会收到发票。 如果你有 MCA 或 MPA 的计费帐户，则始终会收到发票。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和费用，请参阅：

- [查看和下载 Microsoft Azure 使用情况与费用](download-azure-daily-usage.md)
- [了解 Microsoft Azure 账单](review-individual-bill.md)
- [了解有关 Azure 发票的术语](understand-invoice.md)

如果你已签署 MCA，请参阅：

- [了解计费配置文件发票上的费用](review-customer-agreement-bill.md)
- [了解计费配置文件发票上的术语](mca-understand-your-invoice.md)
- [了解计费配置文件的 Azure 使用情况和费用文件](mca-understand-your-usage.md)


