---
title: 商业市场支付摘要 |Azure 应用商店
description: "\"付款\"摘要可显示有关您通过优惠赚取的资金的详细信息。 它还使你可以知道你将何时收到付款和收到多少付款。"
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 678dc8b058d0ae0694dafeb4222b2fc9f10ecda7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288693"
---
# <a name="payout-reporting"></a>付款报告

[**"付款"摘要**](https://docs.microsoft.com/windows/uwp/publish/payout-summary)显示您在 Microsoft 赚取的资金的详细信息。 它还使你可以知道你将何时收到付款和收到多少付款。

如果在 Azure 应用商店中销售产品，您还将在 **"付款摘要**"中看到有关成功付款的信息。 有关 Azure 应用商店付款的详细信息，请参阅[Microsoft Azure 应用商店参与策略](https://go.microsoft.com/fwlink/p/?LinkId=722436)和[Microsoft Azure 应用商店发布者协议](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

> [!NOTE]
> 要有资格获得付款，您的收益必须达到 50 美元的[付款阈值](payment-thresholds-methods-timeframes.md)。 有关付款阈值的详细信息，请参阅此页面并查看[Microsoft Azure 应用商店发布商协议](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

- [访问支出报告的角色和权限](#roles-and-permission-to-access-the-payout-report)
- [付款报告：云合作伙伴门户和合作伙伴中心之间的差异](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [客户类型](#customer-types)
- [付款和使用之间的共同关系](#corelation-between-payout-and-usage)
- [交易历史记录下载](#transaction-history-download-export)
- [计费问题和支持](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>访问支出报告的角色和权限

| 报告/页面    | 帐户所有者    | Manager  | 开发人员 | 业务贡献者 |  财务贡献者 | 营销人员 |
|------------------|------------------|----------|-----------|----|----|-----|
| 采集报告（包括近实时数据） | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 | 可以查看 | 无访问权限 |
| 反馈报告/响应 | 可以查看和发送反馈 | 可以查看和发送反馈 | 可以查看和发送反馈 | 无访问权限 | 无访问权限 | 可以查看和发送反馈 |
| 健康报告（包括近实时数据） | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 |
| 使用情况报告 | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 |
| 付款帐户 | 可以更新 | 无访问权限 | 无访问权限 | 无访问权限 | 可以更新 | 无访问权限 |
| 税务配置文件 | 可以更新 | 无访问权限 | 无访问权限 | 无访问权限 | 可以更新 | 无访问权限 |
| 付款摘要 | 可以查看 | 无访问权限 | 无访问权限 | 无访问权限 | 可以查看 | 无访问权限 |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>付款报告：云合作伙伴门户和合作伙伴中心之间的差异

| | 云合作伙伴门户 | 合作伙伴中心 |
|---------|---------|---------|
| 链接 | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)和[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| 导航 | 在见解支付中提供的付款报告 | 合作伙伴中心提供的付款报告 – 付款图标 |
| 范围 | <ul> <li>每行交易记录可见，用于正在进行的收集、收集和支付 </li> <li>报告 = 在创建采购订单后显示所有行项目，包括正在进行的收集和正在进行的计费，以及尚未有资格付款的集合状态和行项目。 </li> </ul> | <ul> <li>一旦项目被视为合格收入，则显示它们。</li> <li>客户先向微软付款，然后 ISV 可以看到开始付款报告。</li> <li>付款报告不会显示正在进行的集合和正在进行的计费。  </li> </ul>  |
| 交易未准备好付款 | 正在进行的计费 | 下一个估计付款：付款状态处于未处理状态。  |
| 付款状态 |  | 未处理： <br> 收入有资格获得付款。 它在激励计划计划指南中定义的冷却期间保持此状态。 <br> <br> 即将 到来： <br> 付款单生成的待定内部审核，然后处理付款。 <br> <br> 发送时间: <br> 付款已发送到您的银行。 |

## <a name="customer-types"></a>客户类型

### <a name="enterprise-agreement"></a>企业协议

将对没有企业协议的客户就市场软件许可证按月计费。 通过每季度显示的发票对具有企业协议的客户按月计费。

### <a name="credit-cards-and-monthly-invoice"></a>信用卡和每月发票

客户还可以使用信用卡和每月发票进行付款。 在这种情况下，您的软件许可费将按月计费。

### <a name="csp-and-direct-pay-users"></a>CSP 和直接付费用户

例如，如果客户使用信用卡购买。

## <a name="corelation-between-payout-and-usage"></a>付款和使用之间的共同关系

|描述    |    Date  | 订单/使用情况  | 付款 |
|----------|----------|-----------|-------------|
|订购期   | 8 月 15， 2019 - 8 月 30， 2019 | **关联属性订单** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **使用** <br> <ul> <li>CustomerId </li> <li>客户名称</li> <li>（使用参考）采购记录Id/行项目Id</li> <li> 估计延长费用 <br> 估计的付款金额 (PC) </li> </ul> |  |
|期限结束（月）   | 8 月 30， 2019 | | |
|计费日期 | 2019年9月1日 | | |
|客户付款日期 | 2019年9月1日 | | |
|代管期（仅限信用卡，30 天） | 九月 1， 2019 - 九月 30， 2019 | | **关联属性订单：** <br> <ul><li>资产 Id</li> <li>客户 ID</li> <li> 客户名称</li> </ul> <br> **使用** <br> <ul> <li>资产 Id</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>行项目Id</li> <li>交易金额</li> <li>收入金额 最后支付货币</li> </ul> <br> **付款状态：** 未处理 |
|费用收集期开始 | 2019年9月1日 | | |
|费用收集期结束（最大值，30 天） | 2019年9月30日 | | |
|付款计算日期（每月第 15 天） | 2019年10月1日 | | **相关属性** <br> <ul><li>资产 Id</li> <li>客户 ID</li> <li>客户名称</li> </ul> <br> **使用** <br> <ul> <li>资产 Id</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>行项目Id</li> <li>交易金额</li> <li>收入金额 最后支付货币</li> </ul> <br> **付款状态：** 即将 到来 |
|付款日期 | 2019年10月15日 | | **相关属性** <br> <ul><li>资产 Id</li> <li>客户 ID</li> <li> 客户名称</li> </ul> <br> **使用** <br> <ul> <li>资产 Id</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>行项目Id</li> <li>交易金额</li> <li>收入金额 最后支付货币</li> </ul> <br> **付款状态：** 已发送付款 |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>企业协议（季度/月度客户）

| 描述 |    Date  | 使用情况 | 付款 |
|----------|----------|---------|-----------|
|订购期 | 8 月 15， 2019 - 8 月 30， 2019 | **关联属性订单** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **使用情况报告** <br> <ul> <li>CustomerId </li> <li>客户名称</li> <li>（使用参考）采购记录Id/行项目Id</li> <li> 估计延长费用 <br> 估计的付款金额 (PC) </li> </ul> | |
|期限结束（季度） | 2019年9月30日 | | |
|计费日期 | 2019年10月15日 | | |
|代管期（仅限信用卡，30 天） | 不适用 | | |
|费用收集期开始 | 2019年10月15日 | | |
|仅限信用卡，30 天 | 2019年11月1日 - 2019年11月30日 | | |
|费用收集期结束（最大值，90 天） | 2020年1月15日 | | |
|客户付款日期 | 2019年12月30日 | | |
|付款计算 | 2020年1月15日 | | |
|付款日期 | 2 月 15， 2020 | | **对于基于季度的客户** <br> <br> **订单报告** <br> <ul><li>资产 Id</li> <li>客户 ID</li> <li> 客户名称</li> </ul> <br> **使用** <br> <ul> <li>资产 Id</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>行项目Id</li> <li>交易金额</li> <li>收入金额 最后支付货币</li> </ul> <br> **付款状态：** 已发送 |

## <a name="transaction-history-download-export"></a>事务历史记录下载导出

此选项提供您在交易记录页中看到的每个收益行项目的下载、收入类型、日期、关联交易金额、客户、产品和其他适用于奖励计划的交易详细信息。

| 列名称     | 描述    |
|-------------|-------------------------------|
| 盈利 Id                      | 每个收入的唯一标识符                                                                                                       |
| 参与者Id                  | 根据计划赚取的合作伙伴的主要身份                                                                            |
| 参与者 IdType              | 主要用于激励计划和商店计划和 Azure 应用商店的卖家 IF 的程序 ID                                          |
| 参与者姓名                | 收入合作伙伴的姓名                                                                                                              |
| 合作伙伴国家代码             | 赚取合作伙伴的位置/国家/地区                                                                                                  |
| 程序名称                    | 奖励/商店计划名称                                                                                                             |
| transactionId                  | 事务的唯一标识符                                                                                                    |
| 交易货币            | 发生原始客户交易记录的货币（不是合作伙伴位置货币）                                     |
| 交易日期                | 交易记录的日期。 适用于许多交易有助于一次收益的程序                                           |
| 交易汇率        | 用于显示相应交易美元金额的汇率                                                                 |
| 交易金额              | 基于生成赚取的原始交易币种的交易金额                                              |
| 交易金额           | 以美元为单位的交易金额                                                                                                                |
| 杠杆                          | 指示收益的业务规则                                                                                                  |
| 赚取率                    | 对交易金额施加的激励率以产生收益                                                                      |
| quantity                       | 因计划而异。 指示交易程序的计费数量                                                            |
| 数量类型                   | 指示数量类型，例如：计费数量、MAU                                                                                     |
| 收入类型                    | 指示是否收费、返利、合作、销售等。                                                                                          |
| 收入金额                  | 以原始交易货币赚取金额                                                                                      |
| 赚取金额               | 以美元计价的赚取金额                                                                                                                    |
| 赚取日期                    | 赚取日期                                                                                                                      |
| 计算日期                | 在系统中计算收入的日期                                                                                            |
| 赚取汇率            | 用于显示相应美元金额的汇率                                                                                  |
| exchangeRateDate               | 用于计算赚取金额美元的汇率日期                                                                                   |
| 支付金额WO税             | 仅"已发送"付款的赚取金额（不含税款） 以付款到货币                                                                 |
| 付款货币                | 支付合作伙伴在付款配置文件中选择的货币。 仅显示已发送付款                                                   |
| 付款汇率            | 汇率用于计算使用汇率日期以支付货币计算付款金额                                            |
| 付款 ID            | 付款的唯一标识符。 此号码在银行对帐单中可见                                            |
| 付款状态            | 付款状态                                            |
| 付款状态描述            | 付款状态的友好描述                                            |
| customerId                     | 将永远为空                                                                                                                     |
| CustomerName                   | 将永远为空                                                                                                                     |
| partNumber                     | 将永远为空                                                                                                                     |
| productName                    | 链接到交易记录的产品名称                                                                                                       |
| productId                      | 唯一产品标识符                                                                                                                |
| ParentProductID                | 唯一的父产品标识符。 注意：如果交易记录没有父产品，则父产品 ID = 产品 ID。 |
| 父产品名称              | 父产品的名称。 注意：如果事务没有父产品，则父产品名称 = 产品名称。   |
| productType                    | 产品的类型（如应用、加载项、游戏等）                                                                                        |
| 发票编号                  | 发票编号（仅适用于 EA）                                                                                                  |
| 经销商Id                     | 经销商标识符                                                                                                                      |
| 经销商名称                   | 经销商名称                                                                                                                            |
| 事务类型                | 交易的类型（如购买、退款、冲销、拒付等）                                                               |
| 本地供应商卖方            | 本地供应商/记录销售商                                                                                                          |
| 税务汇出                    | 免除的税收额（销售税、使用税或 VAT/GST 税）。                                                                                   |
| 税务分类模型                  | 负责代缴税款（销售税、使用税或 VAT/GST 税）的一方。                                                                    |
| 商店费                       | Microsoft 保留的金额，作为在应用商店中提供应用或加载项的费用。                                            |
| 交易付款方式       | 客户用于交易的付款方式（如信用卡、移动运营商结算、PayPal 等）                                |
| 特潘                           | 指示第三方广告网络                                                                                                     |
| 客户国家                | 客户国家/地区                                                                                                                         |
| 客户城市                   | 客户城市                                                                                                                            |
| 客户国                  | 客户状态                                                                                                                           |
| 客户Zip                    | 客户邮政编码                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| 外部参考 Id            | 程序的唯一标识符                                                                                                        |
| 外部参考IdLabel       | 唯一标识符标签                                                                                                                  |
| 交易国家代码       | 发生交易的国家/地区代码                                                                                                                  |
| 税务国家       | 销售给客户国家/地区                                                                                                                  |
| 税务国家       | 已销售到客户状态                                                                                                                  |
| 税务城       | 出售给客户城市                                                                                                                  |
| 税务代码       | 出售给客户 Zip                                                                                                                  |
| 许可计划名称       |                                                                                                                   |
| 程序代码       | 使用程序名称映射的字符串                                                                                                                   |
| 赚取金额 最后支付货币       | 以最后付款货币赚取金额（如果没有支付预付款，字段将为空）                                                                                                                   |
| 最后支付货币       | 上次付款货币（字段将为空，如果未支付预付款）                                                                                                                   |
| 资产 Id       | 市场服务的客户订单的唯一标识符。  它表示已交易的采购行物料。 可以有多个资产。                                                                                                                   |
| OrderId       | 与客户的发票有关                                                                                                                   |
| 行项目Id       | 客户发票中的单个行                                                                                                                   |
| 客户所在国家/地区       | 客户提供的国家/地区名称。  这可能与客户 Azure 订阅中的国家/地区不同。                                                                                                                   |
| 客户电子邮件地址       | 最终客户提供的电子邮件地址。  这可能与客户 Azure 订阅中的电子邮件地址不同。                                                                                                                   |
| SkuId       | 发布期间定义的 SKU ID。 一个套餐可能有许多 SKU，但一个 SKU 只能与一个套餐相关联。                                                                                                                   |

>[!Note]
>交易发布选项的所有报告和见解均可通过合作伙伴中心的云合作伙伴门户或分析部分的"见解"部分获得。

## <a name="billing-questions-and-support"></a>计费问题和支持

要获取有关计费问题的帮助，请联系[商业市场发布商支持](https://aka.ms/marketplacepublishersupport)。
