---
title: 商业应用商店付出汇总 |Azure Marketplace
description: "\"费用摘要\" 显示了你在产品/服务中获得的资金的详细信息。 它还可让你了解何时会收到付款和支付的费用。"
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 6ee6b6f325ba58ecaa3c3acb5d5ded173262bafb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78344052"
---
# <a name="payout-reporting"></a>支出报表

费用[**摘要**](https://docs.microsoft.com/windows/uwp/publish/payout-summary)显示了你在 Microsoft 获得的资金的详细信息。 它还可让你了解何时会收到付款和支付的费用。

如果你在 Azure Marketplace 中销售产品/服务，你**还将看到**有关付款的信息。 有关 Azure Marketplace 支付的详细信息，请参阅[Microsoft Azure 市场参与策略](https://go.microsoft.com/fwlink/p/?LinkId=722436)和[Microsoft Azure 市场发行者协议](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

> [!NOTE]
> 为了满足付出的条件，你的继续必须达到[支付阈值](payment-thresholds-methods-timeframes.md)$50。 有关付款阈值的详细信息，请参阅此页并查看[Microsoft Azure 市场发布者协议](https://go.microsoft.com/fwlink/p/?LinkID=699560)。

- [用于访问支出报表的角色和权限](#roles-and-permission-to-access-the-payout-report)
- [支出报表：云合作伙伴门户和合作伙伴中心之间的差异](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [客户类型](#customer-types)
- [支出与使用量之间的相关](#corelation-between-payout-and-usage)
- [事务历史记录下载](#transaction-history-download-export)
- [计费问题和支持](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>用于访问支出报表的角色和权限

| 报表/页    | 帐户所有者    | Manager  | 开发人员 | 业务参与者 |  财务参与者 | 营销人员 |
|------------------|------------------|----------|-----------|----|----|-----|
| 购置报表（包括近乎实时的数据） | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 | 可以查看 | 无访问权限 |
| 反馈报告/响应 | 可以查看和发送反馈 | 可以查看和发送反馈 | 可以查看和发送反馈 | 无访问权限 | 无访问权限 | 可以查看和发送反馈 |
| 运行状况报告（包括近乎实时的数据） | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 |
| 使用情况报表 | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 |
| 帐户支出 | 可以更新 | 无访问权限 | 无访问权限 | 无访问权限 | 可以更新 | 无访问权限 |
| 税务配置文件 | 可以更新 | 无访问权限 | 无访问权限 | 无访问权限 | 可以更新 | 无访问权限 |
| 付款摘要 | 可以查看 | 无访问权限 | 无访问权限 | 无访问权限 | 可以查看 | 无访问权限 |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>支出报表：云合作伙伴门户和合作伙伴中心之间的差异

| | 云合作伙伴门户 | 合作伙伴中心 |
|---------|---------|---------|
| 链接 | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory)和[https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| 导航 | Insights 付出的支出报告 | 合作伙伴中心提供的支出报告-支出支出 |
| 范围 | <ul> <li>每行的事务项是可见的，表示正在进行收集、收集和支付 </li> <li>报告–在创建采购订单后显示所有行项，包括正在进行的收集和正在进行的计费，以及尚未获得资格支付的集合状态和行项。 </li> </ul> | <ul> <li>将行项视为符合条件后显示。</li> <li>客户首先向 Microsoft 支付费用，然后 Isv 可以看到付出的费用报告。</li> <li>支出报表将不会显示正在进行的收集和正在进行的计费。  </li> </ul>  |
| 事务未准备好进行支出 | 正在进行计费 | 下一次估计付款：支出状态为 "未处理" 状态。  |
| 支出状态 |  | 尚未 <br> 收益有资格支付。 它在激励计划的收视指南中定义的冷却周期处于此状态。 <br> <br> 到来 <br> 支付订单-在处理付款之前生成的待定内部审查。 <br> <br> 发送时间: <br> 已将付款发送到银行。 |

## <a name="customer-types"></a>客户类型

### <a name="enterprise-agreement"></a>企业协议

将对没有企业协议的客户就市场软件许可证按月计费。 通过每季度显示的发票对具有企业协议的客户按月计费。

### <a name="credit-cards-and-monthly-invoice"></a>信用卡和月度发票

客户还可以使用信用卡和每月发票进行付款。 在这种情况下，软件许可证费用将按月计费。

### <a name="csp-and-direct-pay-users"></a>CSP 和直接支付用户

例如，如果客户是使用信用卡购买的。

## <a name="corelation-between-payout-and-usage"></a>支出与使用量之间的相关

|说明    |    日期  | 订单/使用情况  | 付款 |
|----------|----------|-----------|-------------|
|订购期   | 8月15日，2019-8 月30日，2019 | **相关属性订单** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **使用情况** <br> <ul> <li>CustomerId </li> <li>客户名称</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> 估计的扩展费用 <br> 估计的付款金额 (PC) </li> </ul> |  |
|期限结束（月）   | 8月30日，2019 | | |
|计费日期 | 9月1日，2019 | | |
|客户付款日期 | 9月1日，2019 | | |
|代管期（仅限信用卡，30 天） | 9月1日，2019-9 月30日，2019 | | **相关属性订单：** <br> <ul><li>AssetId</li> <li>客户 ID</li> <li> 客户名称</li> </ul> <br> **使用情况** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **支出状态：** 尚未 |
|费用收集期开始 | 9月1日，2019 | | |
|费用收集期结束（最大值，30 天） | 9月30日，2019 | | |
|付款计算日期（每月第 15 天） | 10月1日，2019 | | **相关属性** <br> <ul><li>AssetId</li> <li>客户 ID</li> <li>客户名称</li> </ul> <br> **使用情况** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **支出状态：** 到来 |
|付款日期 | 十月15，2019 | | **相关属性** <br> <ul><li>AssetId</li> <li>客户 ID</li> <li> 客户名称</li> </ul> <br> **使用情况** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **支出状态：** 已发送付款 |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>企业协议（每季度/每月客户）

| 说明 |    日期  | 用法 | 付款 |
|----------|----------|---------|-----------|
|订购期 | 8月15日，2019-8 月30日，2019 | **相关属性订单** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **使用情况报表** <br> <ul> <li>CustomerId </li> <li>客户名称</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> 估计的扩展费用 <br> 估计的付款金额 (PC) </li> </ul> | |
|期限结束（季度） | 9月30日，2019 | | |
|计费日期 | 十月15，2019 | | |
|代管期（仅限信用卡，30 天） | 不适用 | | |
|费用收集期开始 | 十月15，2019 | | |
|信用卡仅限30天 | 11月1日，2019-11 月30日，2019 | | |
|费用收集期结束（最大值，90 天） | 2020年1月15日 | | |
|客户付款日期 | 2019年12月30日 | | |
|支出计算 | 2020年1月15日 | | |
|付款日期 | 2020年2月15日 | | **对于基于季度的客户** <br> <br> **订单报表** <br> <ul><li>AssetId</li> <li>客户 ID</li> <li> 客户名称</li> </ul> <br> **使用情况** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>客户名称</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **付出状态：** 已发送 |

## <a name="transaction-history-download-export"></a>事务历史记录下载导出

此选项提供了在交易历史记录页中看到的每个收益行项的下载、收入类型、日期、关联的交易金额、客户、产品和其他适用于激励计划的事务详细信息。

| 列名称     | 说明    |
|-------------|-------------------------------|
| earningId                      | 每个收益的唯一标识符                                                                                                       |
| participantId                  | 计划下合作伙伴收益的主要标识                                                                            |
| participantIdType              | 应用商店计划和 Azure Marketplace 的激励计划和卖方的主要计划 ID                                          |
| participantName                | 收益伙伴的名称                                                                                                              |
| partnerCountryCode             | 收益合作伙伴所在国家/地区                                                                                                  |
| programName                    | 激励/存储程序名称                                                                                                             |
| transactionId                  | 事务的唯一标识符                                                                                                    |
| transactionCurrency            | 原始客户交易发生的币种（不是合作伙伴位置货币）                                     |
| transactionDate                | 交易日期。 适用于多个事务贡献给一个收益的程序                                           |
| transactionExchangeRate        | 用于显示对应交易金额的汇率                                                                 |
| transactionAmount              | 基于所生成收益的原始交易币种的交易金额                                              |
| transactionAmountUSD           | 交易金额（美元）                                                                                                                |
| 断裂                          | 指示收入的业务规则                                                                                                  |
| earningRate                    | 应用于交易金额以生成收入的激励率                                                                      |
| quantity                       | 根据程序而异。 指示交易计划的计费数量                                                            |
| quantityType                   | 指示数量的类型，例如：计费数量、MAU                                                                                     |
| earningType                    | 指示是费用、回扣、市场活动、销售等。                                                                                          |
| earningAmount                  | 按原始交易币种的收入金额                                                                                      |
| earningAmountUSD               | 收入金额（美元）                                                                                                                    |
| earningDate                    | 收益日期                                                                                                                      |
| calculationDate                | 在系统中计算收入的日期                                                                                            |
| earningExchangeRate            | 用于显示相应 USD 金额的汇率                                                                                  |
| exchangeRateDate               | 用于计算 EarningAmount USD 的汇率日期                                                                                   |
| paymentAmountWOTax             | 仅为 "已发送" 付款支付的金额（不含税）                                                                 |
| paymentCurrency                | 支付给伙伴在付款配置文件中选择的货币。 仅显示已发送的付款                                                   |
| paymentExchangeRate            | 用于使用 ExchangeRateDate 计算 paymentAmountWOTax 的汇率                                            |
| paymentId            | 付款的唯一标识符。 此数字在银行报表中可见                                            |
| paymentStatus            | 付款状态                                            |
| paymentStatusDescription            | 付款状态的友好说明                                            |
| 顾客                     | 始终为空                                                                                                                     |
| customerName                   | 始终为空                                                                                                                     |
| partNumber                     | 始终为空                                                                                                                     |
| productName                    | 链接到事务的产品名称                                                                                                       |
| productId                      | 唯一产品标识符                                                                                                                |
| parentProductId                | 唯一父产品标识符。 注意：如果没有适用于事务的父产品，则父产品 ID = 产品 ID。 |
| parentProductName              | 父产品的名称。 注意：如果没有适用于事务的父产品，则父产品名称 = 产品名称。   |
| productType                    | 产品类型（如应用、外接程序、游戏等）                                                                                        |
| invoiceNumber                  | 发票编号（仅适用于 EA）                                                                                                  |
| resellerId                     | 经销商标识符                                                                                                                      |
| resellerName                   | 经销商名称                                                                                                                            |
| transactionType                | 事务类型（如购买、退款、冲销、按容量计费等）                                                               |
| localProviderSeller            | 本地提供商/记录的卖方                                                                                                          |
| taxRemitted                    | 汇款的金额（销售、使用或 VAT/GST 税）。                                                                                   |
| taxRemitModel                  | 负责 remitting 税款的参与方（销售、使用或 VAT/GST 税）。                                                                    |
| storeFee                       | Microsoft 为使应用或外接程序在应用商店中可用而保留的金额。                                            |
| transactionPaymentMethod       | 用于交易的客户支付方式（例如卡、移动运营商计费、PayPal 等）                                |
| tpan                           | 指示第三方 ad 网络                                                                                                     |
| customerCountry                | 客户所在国家/地区                                                                                                                         |
| customerCity                   | 客户城市                                                                                                                            |
| customerState                  | 客户状态                                                                                                                           |
| customerZip                    | 客户邮政编码                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | 程序的唯一标识符                                                                                                        |
| externalReferenceIdLabel       | 唯一标识符标签                                                                                                                  |
| transactionCountryCode       | 交易发生的国家/地区代码                                                                                                                  |
| taxCountry       | 销售给客户国家/地区                                                                                                                  |
| taxState       | 销售到客户状态                                                                                                                  |
| taxCity       | 销售给 Customer City                                                                                                                  |
| taxZipCode       | 销售给客户 Zip                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| 程序代码       | 要与程序名称一起映射的字符串                                                                                                                   |
| earningAmountInLastPaymentCurrency       | 按上一付款币种计算的数量（如果没有支付以前的付款，则字段将为空）                                                                                                                   |
| lastPaymentCurrency       | 上次支付的货币（如果尚未支付以前的付款，则字段将为空）                                                                                                                   |
| AssetId       | 适用于 marketplace 服务的客户订单的唯一标识符。  它表示交易记录的采购订单行项。 可以有多个资产。                                                                                                                   |
| OrderId       | 与客户的发票相关                                                                                                                   |
| LineItemId       | 客户发票中的单个行                                                                                                                   |
| 客户所在国家/地区       | 客户提供的国家/地区名称。  这可能不同于客户的 Azure 订阅中的国家/地区。                                                                                                                   |
| 客户 EmailAddress       | 最终客户提供的电子邮件地址。  这可能与客户的 Azure 订阅中的电子邮件地址不同。                                                                                                                   |
| skuId       | 在发布过程中定义的 SKU ID。 产品/服务可能有许多 Sku，但 SKU 只能与单个产品/服务相关联。                                                                                                                   |

>[!Note]
>对于 "事务发布" 选项的所有报告和见解，均可通过合作伙伴中心的 "云合作伙伴门户或分析" 部分的 "见解" 部分获得。

## <a name="billing-questions-and-support"></a>计费问题和支持

若要获取有关计费问题的帮助，请联系[商业 marketplace 发布者支持](https://aka.ms/marketplacepublishersupport)。
