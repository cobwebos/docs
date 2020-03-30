---
title: 商业市场计费 |Azure 应用商店
description: 本文介绍商业市场与商业相关的主题。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279736"
---
# <a name="commercial-marketplace-billing"></a>商业市场计费

本文介绍商业市场与商业相关的主题：

- [市场发布选项](#marketplace-publishing-options)
- [交易一般概述](#transact-general-overview)
- [交易计费模式](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>市场发布选项

商业市场为出版商提供了多种出版选择。

### <a name="list-and-trial-publishing-options"></a>列表和试用发布选项

发布商可以利用列表、试用和自带许可证 （BYOL） 发布选项进行促销和用户获取。 使用这些选项，Microsoft 不会直接参与发布者的软件许可证交易，并且没有关联的交易费用。 发布者负责支持软件许可证交易的各个方面，包括但不限于：订单、履行、计量、计费、开具发票、付款和费用收集。 借助列表和试用发布选项，发布者保留 100% 的收集自客户的发布者软件许可费。

### <a name="transact-publishing-option"></a>交易发布选项

除了列表和试用发布选项外，发行商还可以使用交易发布选项。 此选项利用了 Microsoft 的全球可用商务功能，并允许 Microsoft 代表发布者托管云市场事务。

## <a name="transact-general-overview"></a>交易一般概述

使用交易发布选项时，Microsoft 允许销售第三方软件，并将某些产品/服务类型部署到客户的 Azure 订阅。 在选择计费模型和产品/服务类型时，发布者必须考虑基础结构费用的计费以及发布者自己的软件许可费用。

当前支持以下产品类型（虚拟机、Azure 应用程序和 SaaS 应用程序）的 Transact 发布选项。

![在 Azure 应用商店中交易](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>计费基础结构成本

#### <a name="for-virtual-machines-and-azure-applications"></a>虚拟机和 Azure 应用程序

对于虚拟机和 Azure 应用程序，Azure 基础结构使用费将计费到客户的 Azure 订阅。 基础设施使用费在客户发票上与软件提供商的许可费分开定价和列报。

#### <a name="for-saas-apps"></a>对于 SaaS 应用

对于 SaaS 应用，发布者必须将 Azure 基础结构使用费和软件许可费视为单一费用项。 它表示为客户收取固定费用。 Azure 基础结构使用情况由合作伙伴直接管理并对其计费。 客户将无法看到实际的基础结构使用费。 发布者通常选择将 Azure 基础结构使用费捆绑到其软件许可证定价中。 不计量软件许可费，它也不基于消耗量。

## <a name="transact-billing-models"></a>交易计费模式

根据所使用的交易选项，发布者的软件许可费用可以显示如下：

- *免费*：软件许可证免费。
- *自带许可证 （BYOL）*： 软件许可证的任何适用费用都直接由发布商和客户管理。 Microsoft 仅收取 Azure 基础结构使用费。 （仅限虚拟机和 Azure 应用程序。）
- 即*用即付*：软件许可证费用基于所使用的 Azure 基础结构作为每小时每核 （vCPU） 定价费率显示。 此模型仅适用于虚拟机和 Azure 应用程序。
- *订阅定价*：软件许可费以每月或每年定期费用的形式提出，按统一费率或按席位计费。 此模型仅适用于 SaaS 应用和 Azure 应用程序 - 托管应用。
- *免费软件试用*：软件许可证30天或90天不收取任何费用。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免费和自带许可 (BYOL) 定价

发布免费或自带许可的交易产品/服务时，Microsoft 在促成有关软件许可证费用的销售交易中不扮演任何角色。 如列表和试用发布选项一样，发布者保留 100% 的软件许可证费用。

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>即用即付和订阅（基于站点）定价

发布即用即付或订阅交易产品/服务时，Microsoft 提供处理软件许可证购买、退货和退款的技术和服务。 在这种情况下，发布者出于上述目的授权 Microsoft 充当代理人。 发布者允许 Microsoft 促成软件许可交易，同时保留自身的卖家、提供商、分销商和许可方的称号。

Microsoft 使客户能够订购、许可和使用发布商软件，但须遵守 Microsoft 商业应用商店和发布商最终用户许可协议的条款和条件。 发布商必须在创建产品/服务时提供其最终用户许可协议或选择[标准合同](https://docs.microsoft.com/azure/marketplace/standard-contract)。

### <a name="free-software-trials"></a>免费软件试用

对于交易发布方案，发布者可使软件许可证免费提供 30 天或 90 天。 此折扣功能不包括使用合作伙伴解决方案引致的 Azure 基础结构使用费用。

### <a name="private-offers"></a>专用产品/服务

除了使用产品/服务类型和计费模型将产品/服务货币化外，发布商还可以处理私人产品/服务，并完成协商和交易特定定价或自定义配置。 专用产品/服务受全部 3 个交易发布选项的支持。

此选项允许比公开产品更高或更低的定价。 私人优惠可用于折扣或为优惠添加溢价。 可在产品/服务级别将其 Azure 订阅添加到允许列表，向一个或多个客户提供专用产品/服务。

### <a name="examples"></a>示例

#### <a name="pay-as-you-go"></a>即用即付

* 如果启用“即用即付”选项，则会得到以下成本结构。

|你的许可证费用  | 每小时 $1.00  |
|---------|---------|
|Azure 使用费用（D1/1 核）    |   每小时 $0.14     |
|*由 Microsoft 向客户收费*    |  *每小时 $1.14*       |

* 在此场景中，Microsoft 按每小时 $1.14 收取所发布的 VM 映像的使用费用。

|Microsoft 收费  | 每小时 $1.14  |
|---------|---------|
|Microsoft 将许可证费用的 80% 支付给你|   每小时 $0.80     |
|Microsoft 保留许可证费用的 20%  |  每小时 $0.20       |
|Microsoft 保留 100% 的 Azure 使用费 | 每小时 $0.14 |

### <a name="bring-your-own-license-byol"></a>自带许可证 （BYOL）

* 如果启用“BYOL”选项，则会得到以下成本结构。

|你的许可证费用  | 许可证费用由你协商确定和收取  |
|---------|---------|
|Azure 使用费用（D1/1 核）    |   每小时 $0.14     |
|*由 Microsoft 向客户收费*    |  *每小时 $0.14*       |

* 在此场景中，Microsoft 按每小时 $0.14 收取所发布的 VM 映像的使用费用。

|Microsoft 收费  | 每小时 $0.14  |
|---------|---------|
|Microsoft 保留 Azure 使用费用    |   每小时 $0.14     |
|Microsoft 保留许可证费用的 0%   |  每小时 $0.00       |

### <a name="saas-app-subscription"></a>SaaS 应用程序订阅

此选项必须配置为通过 Microsoft 销售，并且可以按固定费率定价，也可以按月或按年按每个用户定价。

• 如果您为 SaaS 产品/服务启用"通过 Microsoft 销售"选项，则具有以下成本结构。

|你的许可证费用       | 100.00 美元/月  |
|--------------|---------|
|Azure 使用费用（D1/1 核）    | 直接向发布者而不是客户收费 |
|*由 Microsoft 向客户收费*    |  100.00 美元/月（注意：发布者必须将任何引致的或者转付的基础结构费用考虑到许可证费用中）**  |

* 在这种情况下，Microsoft 向你收取 100.00 美元的软件许可证费用，并向发布者支付 80.00 美元。
* 符合减少市场服务费条件的合作伙伴将在 2019 年 5 月至 2020 年 6 月期间降低 SaaS 优惠的交易费用。 在这种情况下，Microsoft 会为您的软件许可证支付 100.00 美元，然后向发布者支付 90.00 美元。

|Microsoft 收费  | 100.00 美元/月  |
|---------|---------|
|Microsoft 将许可证费用的 80% 支付给你 <br> \*Microsoft 为任何合格的 SaaS 应用支付 90% 的许可证成本   |   80.00 美元/月 <br> \*$90.00 每月    |
|Microsoft 保留许可证费用的 20% <br> \*对于任何合格的 SaaS 应用，Microsoft 保留 10% 的许可证成本。  |  20.00 美元/月 <br> \*$100.00     |

**降低市场服务费：** 对于您在我们的商业市场上发布的某些 SaaS 产品，Microsoft 将降低其市场服务费从 20%（如 Microsoft 发布商协议中所述）降至 10%。  为了使您的产品符合资格，Microsoft 必须至少将其中一种产品指定为 IP 共同销售就绪或 IP 共同销售优先级。 要获得该月此降低的市场服务费，必须在上一个日历月结束前至少五 （5） 个工作日满足资格。 降低的市场服务费不适用于 VM、托管应用或通过我们的商业市场提供的任何其他产品。  此降低的市场服务费将提供给合格的产品/服务，Microsoft 将在 2019 年 5 月 1 日至 2020 年 6 月 30 日期间收取许可费。  之后，市场服务费将恢复到正常金额。
