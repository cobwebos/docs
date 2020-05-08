---
title: 支出摘要概述-Azure Marketplace
description: "\"费用摘要\" 显示了你在产品/服务中获得的资金的详细信息。 它还使你可以知道你将何时收到付款和收到多少付款。"
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 48644f2f8148a7aa1974be0d7f761e9b3a55612d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783508"
---
# <a name="payout-summary-overview"></a>付款摘要概述

费用[摘要](./payout-summary.md)显示了你在 Microsoft 获得的资金的详细信息。 它还可让你了解何时会收到付款和支付的费用。

如果你在 Azure Marketplace 中销售产品/服务，你还将看到有关付款的信息。 有关 Azure Marketplace 支付的详细信息，请参阅[Azure marketplace 参与策略](https://docs.microsoft.com/legal/marketplace/participation-policy)和[Microsoft Azure 市场发布者协议](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)。

> [!NOTE]
> 为了满足付出的条件，你的继续必须达到[支付阈值](./payment-thresholds-methods-timeframes.md)$50。 有关付款阈值的详细信息，请参阅[Microsoft Azure 市场发布者协议](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)。

"事务中心" 的 "分析" 部分提供了有关 "事务发布" 选项的所有报告和见解，可使用门户右上角的此图标访问：

![说明合作伙伴中心门户右上角的支出图标。](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>角色和权限

这些是用于访问支出报表的角色和权限：

| 报表/页 | 帐户所有者 | Manager | 开发人员 | 业务参与者 | 财务参与者 | 营销人员 |
| --- | --- | --- | --- | --- | --- | --- |
| 购置报表（包括近乎实时的数据） | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 | 可以查看 | 无访问权限 |
| 反馈报告/响应 | 可以查看和发送反馈 | 可以查看和发送反馈 | 可以查看和发送反馈 | 无访问权限 | 无访问权限 | 可以查看和发送反馈 |
| --- | --- | --- | --- | --- | --- | --- |
| 运行状况报告（包括近乎实时的数据） | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 |
| 使用情况报告 | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 无访问权限 | 无访问权限 |
| 付款帐户 | 可以更新 | 无访问权限 | 无访问权限 | 无访问权限 | 可以更新 | 无访问权限 |
| 税务配置文件 | 可以更新 | 无访问权限 | 无访问权限 | 无访问权限 | 可以更新 | 无访问权限 |
| 付款摘要 | 可以查看 | 无访问权限 | 无访问权限 | 无访问权限 | 可以查看 | 无访问权限  |
| | | | | | | |

## <a name="payout-report-differences"></a>支出报表差异

下面是云合作伙伴门户（旧）和合作伙伴中心之间的支出报表之间的差异（新）：

| 云合作伙伴门户 | 合作伙伴中心 |
| --- | --- |
| **链接**：https://cloudpartner.azure.com/ | **链接**： https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory和https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **导航**： Insights 付出的支出报告 | **导航**：合作伙伴中心提供的支出报告-支出支出 |
| **范围**：<ul><li>对于正在进行的收集、收集和支付，每行项的事务均可见。</li><li>报告–在创建采购订单后显示所有行项，包括正在进行的收集和正在进行的计费，以及尚未获得资格支付的集合状态和行项。</li></ul> | **范围**：<ul><li>将行项视为合格收入后显示。</li><li>客户首先向 Microsoft 支付费用，然后 Isv 可以看到付出的费用报告。</li><li>支出报表不会显示正在进行的收集和正在进行计费。</li></ul> |
| **事务未准备好进行付出**：正在计费 | **事务未准备好进行支出**：下一个估计付款：支出状态为 "未处理" 状态。 |
| **付出状态**：暂缺 | **支出状态**：<ul><li>未处理：收益有资格支付。</li><li>即将推出：在接下来的每月额度内将收益发送到发布者。</li><li>已发送：付款已发送到银行。</li></ul> |
| | |

## <a name="payment-schedules"></a>支付计划

有关付款计划的讨论，包括保持期、合作伙伴可见性，以及客户使用信用卡或发票的时间，请参阅 "费用**详细信息**" 主题的 "[支付计划](./payout-policy-details.md#payment-schedules)" 部分。

## <a name="transaction-history-download-export"></a>事务历史记录下载导出

此选项提供了在 "事务历史记录" 页上看到的每个收益行项的下载。 这包括收入类型、日期、关联的交易金额、客户、产品以及与激励计划相关的其他交易记录详细信息。

| 列名称 | 说明 |
| --- | --- |
| earningId | 每个收益的唯一标识符 |
| participantId | 计划下合作伙伴收益的主要标识 |
| participantIdType | 激励计划和卖方的计划 ID （如果该计划适用于商店节目和 Azure Marketplace） |
| participantName | 收益伙伴的名称 |
| partnerCountryCode | 收益合作伙伴所在国家/地区 |
| programName | 激励/存储程序名称 |
| transactionId | 事务的唯一标识符 |
| transactionCurrency | 原始客户交易发生的币种（不是合作伙伴位置货币） |
| transactionDate | 交易日期。 适用于多个事务贡献给一个收益的程序 |
| transactionExchangeRate | 用于显示对应交易金额的汇率 |
| transactionAmount | 基于所生成收益的原始交易币种的交易金额 |
| transactionAmountUSD | 美元交易金额（美元） |
| 断裂 | 收益的业务规则 |
| earningRate | 应用于交易金额以生成收入的激励率 |
| quantity | 交易计划的计费数量。 根据程序而异 |
| quantityType | 数量类型，例如：计费数量、每月活动用户（MAU） |
| earningType | 指示是否收费、回扣、市场活动、销售等 |
| earningAmount | 按原始交易币种的收入金额 |
| earningAmountUSD | 收入金额（美元） |
| earningDate | 收益日期 |
| calculationDate | 在系统中计算收入的日期 |
| earningExchangeRate | 用于显示相应 USD 金额的汇率 |
| exchangeRateDate | 用于计算 EarningAmount USD 的汇率日期 |
| paymentAmountWOTax | 仅为&quot;已发送&quot;付款支付的金额（不含税） |
| paymentCurrency | 支付给伙伴在付款配置文件中选择的货币。 仅显示已发送的付款 |
| paymentExchangeRate | 用于使用 ExchangeRateDate 计算 paymentAmountWOTax 的汇率 |
| paymentId | 付款的唯一标识符。 此数字在银行报表中可见 |
| paymentStatus | 付款状态 |
| paymentStatusDescription | 付款状态的说明 |
| customerId | 始终为空 |
| CustomerName | 始终为空 |
| partNumber | 始终为空 |
| productName | 链接到事务的产品名称 |
| productId | 唯一产品标识符 |
| ParentProductID | 唯一的父产品标识符。 如果没有用于事务的父产品，则父产品 ID = 产品 ID。 |
| parentProductName | 父产品的名称。 如果没有适用于事务的父产品，则父产品名称 = 产品名称。 |
| productType | 产品类型（例如，应用、外接程序和游戏） |
| invoiceNumber | 发票编号（仅适用于企业协议） |
| resellerId | 经销商标识符 |
| resellerName | 经销商名称 |
| transactionType | 事务类型（如购买、退款、冲销和按使用计费） |
| localProviderSeller | 本地提供商/记录的卖方 |
| taxRemitted | 免除的税收额（销售税、使用税或 VAT/GST 税）。 |
| taxRemitModel | 负责代缴税款（销售税、使用税或 VAT/GST 税）的一方。 |
| storeFee | Microsoft 为使应用或外接程序在商业应用商店中提供的费用而保留的金额。 |
| transactionPaymentMethod | 用于交易（如卡、移动运营商计费和 PayPal）的客户付款方式 |
| tpan | 第三方 ad 网络 |
| customerCountry | 客户所在国家/地区 |
| customerCity | 客户城市 |
| customerState | 客户状态 |
| customerZip | 客户邮政编码 |
| TenantID | 租户的 ID |
| externalReferenceId | 程序的唯一标识符 |
| externalReferenceIdLabel | 唯一标识符标签 |
| transactionCountryCode | 交易发生的国家/地区代码 |
| taxCountry | 客户的国家/地区 |
| taxState | 客户的省/市/自治区 |
| taxCity | 客户所在的城市 |
| taxZipCode | 客户的邮政编码 |
| LicensingProgramName | 许可计划的名称 |
| 程序代码 | 要与程序名称一起映射的字符串 |
| earningAmountInLastPaymentCurrency | 按上一付款币种计算的收入量（如果尚未支付之前的付款，则字段将为空） |
| lastPaymentCurrency | 上次付款币种（如果尚未支付以前的付款，则字段将为空） |
| AssetId | 适用于 marketplace 服务的客户订单的唯一标识符。 它表示采购行项。 可以有多个资产。 |
| OrderId | 与客户的发票相关 |
| LineItemId | 客户发票中的单个行 |
| 客户所在国家/地区 | 客户提供的国家/地区名称。 这可能不同于客户的 Azure 订阅中的国家/地区。 |
| 客户 EmailAddress | 客户提供的电子邮件地址。 这可能与客户的 Azure 订阅中的电子邮件地址不同。 |
| SkuId | 在发布过程中定义的 SKU ID。 一个套餐可能有许多 SKU，但一个 SKU 只能与一个套餐相关联。 |

> [!NOTE]
> 可以在合作伙伴中心的 "分析" 部分找到有关 "事务发布" 选项的所有报告和见解。

## <a name="billing-questions-and-support"></a>计费问题和支持
若要获得计费支持，请联系商业 marketplace[发布者支持](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="next-step"></a>后续步骤

- [付款摘要](./payout-summary.md)
