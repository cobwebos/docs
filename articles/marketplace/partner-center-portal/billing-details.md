---
title: 商业市场计费 |Azure Marketplace
description: 本文介绍适用于商业应用商店的商业相关主题。
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: 0fda04bbfa632f429539ddc0ad90941cb645226b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934638"
---
# <a name="commercial-marketplace-billing"></a>商业市场计费 

本文介绍适用于商业应用商店的商业相关主题：

- [Marketplace 发布选项](#marketplace-publishing-options) 
- [事务常规概述](#transact-general-overview)
- [Transact-sql 计费模型](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>市场发布选项 

商业市场为发布者提供了若干发布选项。

### <a name="list-and-trial-publishing-options"></a>列表和试用版发布选项

发布者可以利用列表、试用版和自带许可证（BYOL）发布选项来进行促销和用户获取。 借助这些选项，Microsoft 不会直接参与发布者的软件许可证事务，也不会产生关联的事务费用。 发布者负责支持软件许可证交易的各个方面，包括但不限于：订单、履行、计量、计费、开具发票、付款和费用收集。 借助列表和试用发布选项，发布者保留 100% 的收集自客户的发布者软件许可费。

### <a name="transact-publishing-option"></a>交易发布选项

除了列表和试用版发布选项以外，发布服务器还提供了 "事务发布" 选项。 此选项利用 Microsoft 的全球可用商业功能，并允许 Microsoft 代表发布者托管云 marketplace 交易。

## <a name="transact-general-overview"></a>交易一般概述

使用 "事务发布" 选项时，Microsoft 允许销售第三方软件，并将某些产品/服务类型部署到客户的 Azure 订阅。 在选择计费模型和产品/服务类型时，发布者必须考虑基础结构费用的计费以及发布者自己的软件许可费用。 

以下产品/服务类型当前支持 "事务发布" 选项：虚拟机、Azure 应用程序和 SaaS 应用程序。 

![Azure Marketplace 中的事务](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>计费基础结构成本

#### <a name="for-virtual-machines-and-azure-applications"></a>适用于虚拟机和 Azure 应用程序

对于虚拟机和 Azure 应用程序，Azure 基础结构使用费用会按客户的 Azure 订阅计费。 基础结构使用费用是定价的，并与该客户的发票上的软件提供商许可费用分开显示。

#### <a name="for-saas-apps"></a>适用于 SaaS 应用

对于 SaaS 应用，发布者必须将 Azure 基础结构使用费和软件许可费视为单一费用项。 它表示为客户的固定费用。 Azure 基础结构使用情况由合作伙伴直接管理并对其计费。 客户将无法看到实际的基础结构使用费。 发布者通常选择将 Azure 基础结构使用费捆绑到其软件许可证定价中。 不计量软件许可费，它也不基于消耗量。

## <a name="transact-billing-models"></a>交易计费模式

根据所使用的事务选项，发布者的软件许可费用可以按如下方式显示：

- *免费*：不收取软件许可证费用。
- *自带许可（BYOL）* ：软件许可证的任何适用收费直接在发布方和客户之间进行管理。 Microsoft 仅收取 Azure 基础结构使用费。 （仅限虚拟机和 Azure 应用程序。）
- 即用即*付*：根据所使用的 Azure 基础结构，以每小时、每核（vCPU）定价费率的形式呈现软件许可证费用。 此模型仅适用于虚拟机和 Azure 应用程序。
- *订阅定价*：软件许可费用以每月或每年的形式显示，按固定费率或按座位计费。 此模型仅适用于 SaaS 应用和 Azure 应用程序托管的应用。
- *免费软件试用*：对于30天或90天，软件许可证不收取任何费用。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免费和自带许可 (BYOL) 定价

发布免费或自带许可的交易产品/服务时，Microsoft 在促成有关软件许可证费用的销售交易中不扮演任何角色。 如列表和试用发布选项一样，发布者保留 100% 的软件许可证费用。

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>即用即付和订阅（基于站点）定价

在发布即用即付或订阅交易时，Microsoft 提供了相应的技术和服务来处理软件许可证购买、退货和充电。 在这种情况下，发布者出于上述目的授权 Microsoft 充当代理人。 发布者允许 Microsoft 促成软件许可交易，同时保留自身的卖家、提供商、分销商和许可方的称号。

Microsoft 使客户能够订购、许可和使用出版商软件，经受 Microsoft 的商业市场和发布者的最终用户许可协议的条款和条件。 发布者必须提供最终用户许可协议，或在创建产品/服务时选择[标准合同](https://docs.microsoft.com/azure/marketplace/standard-contract)。

### <a name="free-software-trials"></a>免费软件试用

对于交易发布方案，发布者可使软件许可证免费提供 30 天或 90 天。 此折扣功能不包括使用合作伙伴解决方案引致的 Azure 基础结构使用费用。

### <a name="private-offers"></a>专用产品/服务

除了使用产品/服务类型和计费模型来盈利产品/服务，发布者还可以使用协商和交易特定定价或自定义配置来处理专用产品/服务。 专用产品/服务受全部 3 个交易发布选项的支持。

此选项允许比公开提供的产品/服务更高或更低的定价。 专用产品/服务可用于为产品/服务提供折扣或添加高级版。 可在产品/服务级别将其 Azure 订阅添加到允许列表，向一个或多个客户提供专用产品/服务。

### <a name="examples"></a>示例

#### <a name="pay-as-you-go"></a>现用现付

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

### <a name="bring-your-own-license-byol"></a>自带许可（BYOL）

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

### <a name="saas-app-subscription"></a>SaaS 应用订阅

此选项必须配置为通过 Microsoft 销售，并可按固定费率或每个用户每月或每年的价格定价。

•如果启用了 SaaS 产品/服务的 "销售通过 Microsoft" 选项，则可以使用以下成本结构。

|你的许可证费用       | 100.00 美元/月  |
|--------------|---------|
|Azure 使用费用（D1/1 核）    | 直接向发布者而不是客户收费 |
|*由 Microsoft 向客户收费*    |  100.00 美元/月（注意：发布者必须将任何引致的或者转付的基础结构费用考虑到许可证费用中）  |

* 在这种情况下，Microsoft 向你收取 100.00 美元的软件许可证费用，并向发布者支付 80.00 美元。
* 符合 Marketplace 服务费用的合作伙伴将从5月2019到6月5日起，从 5 2020 月5日起，对 SaaS 产品/服务的事务费用将减少。 在此方案中，Microsoft 为你的软件许可证计费 $100.00，并向发布者支付 $90.00。

|Microsoft 收费  | 100.00 美元/月  |
|---------|---------|
|Microsoft 将许可证费用的 80% 支付给你 <br> \* Microsoft 为任何合格的 SaaS 应用支付90% 的许可费用   |   80.00 美元/月 <br> 每月 \* $90.00    |
|Microsoft 保留许可证费用的 20% <br> \* Microsoft 将为任何合格的 SaaS 应用保留10% 的许可费用。  |  20.00 美元/月 <br> \* $10.00     |

**降低 Marketplace 服务费用：** 对于在我们的商业市场上发布的某些 SaaS 产品，Microsoft 将其 Marketplace 服务费用从20% 降低到10% （如 Microsoft 发布者协议中所述）。  为了使你的产品符合资格，Microsoft 至少必须将其中一个产品指定为 IP 联销售就绪或 IP 共同销售的优先级。 若要在该月内享受此降低的 Marketplace 服务费用，必须在上一个日历月结束之前至少在5个工作日内满足资格。 降低 Marketplace 服务费用不适用于 Vm、托管应用或通过我们的商业市场提供的任何其他产品。  使用 Microsoft 在5月1日到2019到6月 30 2020 日之间收集的许可费用，此降低了 Marketplace 服务费用。  在此之后，Marketplace 服务费用将恢复为其正常量。
