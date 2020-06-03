---
title: Azure 市场
description: 介绍 EA 客户可如何使用 Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: banders
ms.openlocfilehash: 4721d8dbe5973baf2ac82124ff516a89398227cb
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298187"
---
# <a name="azure-marketplace"></a>Azure 市场

本文介绍 EA 客户和合作伙伴如何查看市场费用并启用 Azure Marketplace 购买功能。

## <a name="azure-marketplace-for-ea-customers"></a>面向 EA 客户的 Azure 市场

直接客户可以在 Azure Enterprise 门户上看到 Azure 市场费用。 Azure 市场购买和消耗内容将在货币承诺以外按季或按月以欠款的形式计费。

间接客户可以在 Azure Enterprise 门户的“管理订阅”页上找到其 Azure 市场订阅，但定价处于隐藏状态。 客户应该联系其授权解决方案提供商 (LSP) 了解有关 Azure 市场费用的信息。

对于每月或每年新的重复性 Azure 市场购买，在购买 Azure 市场项时将按周期全额计费。 这些项目会在原始购买的同一日在以下期间自动更新。

现有的每月重复费用将在每个日历月的第一日继续更新。 年度费用则在购买周年日更新。

Azure 市场提供的某些第三方经销商服务现在使用企业协议 (EA) 货币承诺余额。 这些服务以前不遵照 EA 货币承诺进行计费并且单独开具发票。 Azure 市场中这些服务采用 EA 货币承诺付款可帮助客户简化购买和支付管理过程。 如需现在使用货币承诺的服务的完整列表，请参阅 [2018 年 3 月 6 日的 Azure 网站更新内容](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)。

### <a name="partners"></a>合作伙伴

LSP 可以从 Azure Enterprise 门户中的价目表页下载 Azure 市场价目表。 在右上角选择“市场价目表”链接。 Azure 市场价目表显示所有可用服务及其价格。

若要下载价目表：

1. 在 Azure Enterprise 门户中，转到“报表”>“价目表”。 > 
1. 在右上角你的用户名下找到 Azure 市场价目表的链接。
1. 右键单击该链接，并选择“将目标另存为”。
1. 在“保存”窗口中，将文档的标题更改为 `AzureMarketplacePricelist.zip`，这会将该文件的格式由 .xlsx 更改为 .zip。
1. 下载完成后，你会得到一个包含特定于国家/地区的价目表的 zip 文件。
1. LSP 应参考各个国家/地区文件中国家/地区特定的定价。 LSP 可以使用“通知”选项卡来了解全新的或已停用的 SKU。
1. 价格不会频繁变化。 在价格提高和外汇 (FX) 发生变化之前的 30 天，LSP 会收到电子邮件通知。
1. LSP 每季度每个 ISV 的每个注册都会收到一份发票。

### <a name="enabling-azure-marketplace-purchases"></a>启用 Azure 市场购买

企业管理员可为其注册下的所有 Azure 订阅禁用或启用 Azure 市场购买功能。 如果企业管理员禁用购买且 Azure 订阅已经有了 Azure 市场订阅，这些 Azure 市场订阅不会被取消或不会受到影响。

尽管客户可将自己的直接 Azure 订阅转换为 Azure EA 订阅（通过将直接订阅关联到 Azure Enterprise 门户中的注册），但此操作不会自动转换子订阅。

若要启用 Azure 市场购买：

1. 以企业管理员身份登录到 Azure Enterprise 门户。
1. 转到“管理”。
1. 在“注册详细信息”下，选择“Azure 市场”行项旁边的铅笔图标。 
1. 相应地切换为“启用/禁用”或“仅免费 BYOL SKU”。 
1. 选择“保存”。

> [!NOTE]
> “BYOL (自带许可)和仅免费”选项会将购买和收购 Azure 市场 SKU 限制为仅 BYOL 和免费 SKU。

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA 按小时计费的服务

以下服务按企业协议计费，而不是按 MOSP 中的每月费率计费：

- 应用程序传送网络
- Web 应用程序防火墙

### <a name="azure-remoteapp"></a>Azure RemoteApp

企业协议客户根据其企业协议价格水平为 Azure RemoteApp 付费。 无需额外付费。 标准价格包括初始 40 小时。 无限制价格包括初始 80 小时。 RemoteApp 在 80 小时后停止发出用量。

## <a name="azure-marketplace-faq"></a>Azure 市场常见问题解答

本部分解释 Azure 货币承诺如何应用到 Azure 市场中的某些第三方经销商服务。

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Azure 市场服务和 EA 货币承诺方面发生了哪些更改？

从 2018 年 3 月 1 日起，某些第三方经销商服务会消耗 EA 货币承诺 (MC)。 以前，除 Azure 虚拟机预留实例 (RI) 外，其他服务会在 EA 货币承诺以外计费，并单独开票。

我们扩展了 MC 的使用范围，现在它包括客户频繁购买的、由第三方发布的一些 Azure 市场服务。 Azure 市场中这些服务采用 EA 货币承诺付款可帮助简化购买和支付管理过程。

### <a name="why-did-we-make-this-change"></a>为何做出这种更改？

客户不断寻找其他方式来利用 MC 提前付款。 此项更改因应了客户的频繁请求，并影响了大部分 Azure 市场客户。

### <a name="how-do-you-benefit"></a>它为客户带来了哪些好处？

客户可获得更简单的计费体验，并且可以更好地消费 EA 货币承诺。 由于这些服务包含在预付的 MC 中，因此 EA 货币承诺更有价值。

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>如何知道 Azure 市场服务使用了多少 EA 货币承诺？

购买使用 MC 的服务时，Azure 市场会显示免责声明。 支持 Red Hat、SUSE、Autodesk 和 Oracle 发布的某些服务。 目前，不会从 MC 中减去其他第三方发布的名称类似的服务。 本 FAQ 末尾提供完整的列表。

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>如果我的 EA 货币承诺已用尽，该怎么办？

如果你已消耗所有 MC 并处于超额状态，与这些服务相关的费用会连同所有其他消耗服务一起显示在下一份超额发票上。 在实施 2018 年 3 月 1 日更改之前，这些费用会连同其他 Azure 市场服务一起开票。

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>为何并非所有 Azure 市场项都消耗 EA 货币承诺？

我们始终致力于在 EA 货币承诺方面提供最佳客户体验。 此项更改满足了大量客户的需求，能应对 Azure 市场中总支出的很大一部分。 将来可能会添加其他服务。

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>这对间接合约和合作伙伴有何影响？

这不会对间接合约客户或合作伙伴产生任何影响。 这些服务与其他其他消费服务一样，受到相同的合作伙伴加价功能的约束。 唯一的变化是，费用将显示在不同的发票上，并在客户的 EA 货币承诺以外支付费用。

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>你们是否提供了消耗 EA 货币承诺的 Azure 市场服务列表？

特定的 Azure 市场套餐可以使用货币承诺资金。 有关参与此计划的产品的完整列表，请参阅[使用货币承诺的第三方服务](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)。


## <a name="next-steps"></a>后续步骤

- 获取有关[定价](ea-pricing-overview.md)的详细信息。