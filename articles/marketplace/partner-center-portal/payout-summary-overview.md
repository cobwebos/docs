---
title: 支出摘要概述，Azure Marketplace
description: 付款摘要显示了你通过产品/服务赚取的资金的详细信息。 它还显示了收到付款的时间以及付款金额。
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 09/09/2020
ms.openlocfilehash: 708fc2c0783bdefa4ac4fa4b73f10733bba0bc04
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006918"
---
# <a name="payout-summary-overview"></a>付款摘要概述

[付款摘要](./payout-summary.md)显示有关通过 Microsoft 赚取的资金的详细信息。 它还显示了收到付款的时间以及付款金额。

如果你在 Azure Marketplace 中销售产品/服务，你还将看到有关付款的信息。 有关 Azure Marketplace 支付的详细信息，请参阅商业市场和[Microsoft 发行者协议](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)[中的付费](./get-paid.md)。

> [!NOTE]
> 你的收款必须达到 50 美元的付款阈值，才符合成为付款的条件。 有关详细信息，请参阅 [Microsoft 发行者协议](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4xqkx)。

若要查看付出的详细信息，请登录到 [合作伙伴中心](https://partner.microsoft.com/dashboard/home) ，并选择屏幕右上角的 "支出" 图标：

![展示了合作伙伴中心门户右上角的“付款”图标。](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>角色和权限

这些是访问支出报表的角色和权限：

| 报表/页 | 帐户所有者 | Manager | 开发人员 | 业务参与者 | 财务参与者 | 营销人员 |
| --- | --- | --- | --- | --- | --- | --- |
| 获取报表（包括准实时数据） | 可以查看 | 可以查看 | 不允许访问 | 不允许访问 | 可以查看 | 不允许访问 |
| 反馈报告/响应 | 可以查看和发送反馈 | 可以查看和发送反馈 | 可以查看和发送反馈 | 不允许访问 | 不允许访问 | 可以查看和发送反馈 |
| --- | --- | --- | --- | --- | --- | --- |
| 运行状况报告（包括准实时数据） | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 不允许访问 | 不允许访问 |
| 使用情况报告 | 可以查看 | 可以查看 | 可以查看 | 可以查看 | 不允许访问 | 不允许访问 |
| 付款帐户 | 可以更新 | 不允许访问 | 不允许访问 | 不允许访问 | 可以更新 | 不允许访问 |
| 税务资料 | 可以更新 | 不允许访问 | 不允许访问 | 不允许访问 | 可以更新 | 不允许访问 |
| 付款摘要 | 可以查看 | 不允许访问 | 不允许访问 | 不允许访问 | 可以查看 | 不允许访问  |
| | | | | | | |

## <a name="payment-schedules"></a>付款计划

若要深入了解付款计划，包括保持期、合作伙伴可见性以及客户使用信用卡或发票的情况，请参阅**付出详细信息**一文中的[付款计划](./payout-policy-details.md#payment-schedules)部分。

## <a name="transaction-history-download-export"></a>交易历史记录下载导出

此选项提供你在“交易历史记录”页面上看到的每个收入行项的下载。 这包括收入类型、日期、关联的交易金额、客户、产品和与激励计划相关的其他交易详细信息。

| 列名称 | 说明 |
| --- | --- |
| earningId | 每笔收入的唯一标识符 |
| participantId | 计划下合作伙伴收入的主要标识 |
| participantIdType | 激励计划和卖家的计划 ID（如果计划适用于商店计划和 Azure 市场） |
| participantName | 收入合作伙伴的名称 |
| partnerCountryCode | 收入合作伙伴所在的位置/国家/地区 |
| programName | 激励/存储计划名称 |
| transactionId | 交易的唯一标识符 |
| transactionCurrency | 原始客户交易所用的币种（不是合作伙伴所在位置的币种） |
| transactionDate | 交易日期。 适用于多笔交易达成一项收入的计划 |
| transactionExchangeRate | 用于显示相应交易美元金额的汇率 |
| transactionAmount | 以产生收入的原始交易币种计算的交易金额 |
| transactionAmountUSD | 交易金额（美元） |
| lever | 收入的业务规则 |
| earningRate | 产生收入的交易金额的激励率 |
| quantity | 交易计划的计费数量。 根据计划而异 |
| quantityType | 数量类型，例如：计费数量，月度活跃用户 (MAU) |
| earningType | 指示是否是费用、回扣、合作、销售等 |
| earningAmount | 以原始交易币种计算的收入金额 |
| earningAmountUSD | 收入金额（美元） |
| earningDate | 收入日期 |
| calculationDate | 在系统中计算收入的日期 |
| earningExchangeRate | 用于显示相应美元金额的汇率 |
| exchangeRateDate | 用于计算美元 EarningAmount 的汇率日期 |
| paymentAmountWOTax | 按付款币种计算的收入金额（不含税），仅针对“已发送”的付款 |
| paymentCurrency | 合作伙伴在付款配置文件中选择的付款币种。 仅针对已发送的付款 |
| paymentExchangeRate | 用于使用 ExchangeRateDate 以付款币种计算 paymentAmountWOTax 的汇率 |
| paymentId | 付款的唯一标识符。 此数字在银行报表中可见 |
| paymentStatus | 付款状态 |
| paymentStatusDescription | 付款状态的说明 |
| customerId | 始终为空 |
| customerName | 始终为空 |
| partNumber | 始终为空 |
| productName | 与交易关联的产品名称 |
| productId | 唯一的产品标识符 |
| parentProductId | 唯一父产品标识符。 如果交易没有父产品，则父产品 ID = 产品 ID。 |
| parentProductName | 父产品名称。 如果交易没有父产品，则父产品名称 = 产品名称。 |
| productType | 产品类型（例如应用、附加产品和游戏） |
| invoiceNumber | 发票编号（仅适用于企业协议） |
| resellerId | 经销商标识符 |
| resellerName | 经销商名称 |
| transactionType | 交易类型（如购买、退款、冲销） |
| localProviderSeller | 记录的本地提供商/卖家 |
| taxRemitted | 减免税额（销售税、使用税或增值税/消费税）。 |
| taxRemitModel | 免税方（销售税、使用税或增值税/消费税）。 |
| storeFee | Microsoft 保留金额，用作在商业市场上提供应用或附加产品的费用。 |
| transactionPaymentMethod | 用于交易的客户付款方式（如卡、移动运营商账单和 PayPal） |
| tpan | 第三方广告网络 |
| customerCountry | 客户所在国家/地区 |
| customerCity | 客户所在城市 |
| customerState | 客户所在的州/省 |
| customerZip | 客户邮政编码 |
| TenantID | 租户的 ID |
| externalReferenceId | 计划的唯一标识符 |
| externalReferenceIdLabel | 唯一标识符标签 |
| transactionCountryCode | 发生交易的国家/地区代码 |
| taxCountry | 客户所在国家/地区 |
| taxState | 客户所在的州/省 |
| taxCity | 客户所在的城市 |
| taxZipCode | 客户的邮政编码 |
| LicensingProgramName | 许可计划的名称 |
| Program Code | 要与计划名称一起映射的字符串 |
| earningAmountInLastPaymentCurrency | 上一付款币种的收入金额（如果之前没有付款，则字段将为空） |
| lastPaymentCurrency | 上一付款币种（如果之前没有付款，则字段将为空） |
| AssetId | 市场服务客户订单的唯一标识符。 它表示购买行项。 可以有多个资产。 |
| OrderId | 与客户的发票相关 |
| LineItemId | 客户发票中的单个行 |
| 客户所在国家/地区 | 客户提供的国家/地区名称。 这可能不同于客户 Azure 订阅中的国家/地区。 |
| Customer EmailAddress | 客户提供的电子邮件地址。 这可能不同于客户 Azure 订阅中的电子邮件地址。 |
| SkuId | 发布期间定义的 SKU ID。 一个产品/服务可能有许多 SKU，但一个 SKU 只能与一个产品/服务关联。 |

> [!NOTE]
> 在合作伙伴中心的分析部分可以找到交易发布选项的所有报告和见解。

## <a name="billing-questions-and-support"></a>计费问题和支持
有关计费支持，请联系商业市场[发布者支持](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="next-step"></a>后续步骤

- [付款摘要](./payout-summary.md)
