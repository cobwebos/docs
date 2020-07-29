---
title: 付款报告 - Microsoft 商业市场
description: 付款报告详细说明了你通过自己的产品/服务赚取的资金，包括付款金额和你将何时收到付款。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.date: 04/24/2020
ms.openlocfilehash: dc690e29129f5be68456e6a9dc075ba72f11b121
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318226"
---
# <a name="payout-summaries"></a>付款摘要

“付款摘要”详细说明了你通过 Microsoft 赚取的资金。 它还显示了你将何时收到付款以及付款金额。

如果你在 Azure 市场上销售产品，还会在“付款摘要”中看到成功付款的相关信息。 有关详细信息，请参阅 [Microsoft Azure 市场参与策略](https://docs.microsoft.com/legal/marketplace/participation-policy)和 [Microsoft Azure 市场发布者协议](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)。

> [!NOTE]
> 你的收款必须达到 50 美元的[付款阈值](payment-thresholds-methods-timeframes.md)，才符合成为付款的条件。 若要详细了解付款阈值，请参阅此页，并查阅应用开发人员协议。

> [!NOTE]
> 若要寻求付款方面的支持（包括配置付款帐户、付款丢失、暂停付款或其他任何方面），请单击[此处](https://developer.microsoft.com/windows/support)联系支持人员。

## <a name="access-the-payout-summary-pages"></a>访问“付款摘要”页

若要打开“付款摘要”页之一，请执行以下操作：

1. 选择右上角的“付款”图标。
2. 选择“交易历史记录”、“付款”或“导出数据”。

## <a name="transaction-history-page"></a>“交易历史记录”页

此页显示你的所有个人收入，包括每次交易的日期、类型和收入。 可以选择要查看的时间段，也可以按“合约 ID”、“计划”、“付款 ID”、“收入类型”、“控制杆”和“状态”进行筛选。 本会计年度（7 月 1 日至 6 月 30 日）和前两个会计年度的数据都可查看。

若要查看关于收入的更多详情，请选择页面右侧的向下箭头。 这会显示控制杆、收入金额和产品。 如果任何此类数据无法查看，但你又需要访问它，请联系[支持人员](https://developer.microsoft.com/windows/support)。 如果收入是由于调整（而不是交易）生成的，产品字段将不会显示。

若要导出此页上的任何交易数据，请使用“导出数据”页。

## <a name="payments-page"></a>“付款”页

此页上的总计代表你参与的所有计划。 可以按“参与者 ID”、“计划”、“付款 ID”和“收入类型”进行筛选。 金额以美元为单位。 付款值还以付款币种显示。

| 区域                   | 说明                                                                                |
|------------------------|---------------------------------------------------------------------------------------------|
| 今年付款总额   | 你今年通过所有计划收到的合并付款总额（以美元为单位）       |
| 下次估计付款 | 你的下一单笔付款（即使很快会有其他付款）（以美元为单位） |
| 上次付款           | 最近一次付款的金额（以美元为单位）、计划名称和计划           |
| 付款(按来源)     | 按计划显示的过去 12 个月内的付款金额（以美元为单位）           |
| 付款               | 选择“已付款”或“挂起”，然后根据需要进行排序。 如需了解特定付款的更多详情，请选择“查看”。 若要下载付款汇款对帐单的副本，请选择“下载”。 由于交易历史记录数据最长可能需要 24 小时才会显示，因此你可能无法立即看到相关收入。 |
|||

若要导出此页上的任何数据，请选择“导出数据”页上的“导出”，然后按照说明操作。

## <a name="transaction-history-page"></a>“交易历史记录”页

此页显示你的所有个人收入，包括每次交易的日期、类型和收入。 可以选择要查看的时间段，也可以按“合约 ID”、“计划”、“付款 ID”、“收入类型”、“控制杆”和“状态”进行筛选。 本会计年度（7 月 1 日至 6 月 30 日）和前两个会计年度的数据都可查看。

若要查看关于收入的更多详情，请选择页面右侧的向下箭头。 这会显示控制杆、收入金额和产品。 如果任何此类数据无法查看，但你又需要访问它，请联系[支持人员](https://developer.microsoft.com/windows/support)。 如果收入是由于调整（而不是交易）生成的，产品字段将不会显示。

若要导出此页上的任何交易数据，请选择“导出数据”页上的“导出”，然后按照说明操作。 从“交易历史记录”页导出的文件以交易币种显示数据、以交易币种和美元显示收入，并以付款币种显示付款值。

## <a name="payment-status"></a>付款状态

| 收入状态           | 原因                                                                                                                                      | 合作伙伴是否需要采取行动？                                   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| 未处理              | 收入符合成为付款的条件。 按照激励计划的计划指南中的规定，它会在这种状态下度过“冷却”期。 | 否                                                         |
| 即将处理                 | 付款订单已生成，正在等待处理付款前的内部审查                                                               | 否                                                         |
| 等待纳税发票      | 纳税发票不完整或无效                                                                                                  | 必须更新纳税发票，才能收到付款 |
| 审查期间被拒绝   | 付款在审查期间被拒绝了                                                                                                     | 有关详细信息，请联系 [Microsoft 支持人员](https://developer.microsoft.com/windows/support)                      |
| 失败                   | 付款因 Microsoft 系统错误而失败                                                                                         | 有关详细信息，请联系 [Microsoft 支持人员](https://developer.microsoft.com/windows/support)                      |
| 正在进行              | 正在付款                                                                                                                 | 否                                                         |
| 付款不正确        | 正在收回付款                                                                                                       | 否                                                         |
| 已发送                     | 付款已发送到银行                                                                                                     | 否                                                         |
| 正在重新处理             | 付款遇到了 Microsoft 系统错误，正在重新处理                                                                  | 否                                                         |
| Reversed                 | 付款已被银行退回，将在下一个付款周期再次发送                                                     | 否                                                         |
| 纳税发票被拒绝     | 纳税发票在审查期间被拒绝了。 在纳税发票审查完成之前，所有挂起的付款都将处于暂停状态。                 | 有关详细信息，请联系 [Microsoft 支持人员](https://developer.microsoft.com/windows/support)                      |
| 正在审查纳税发票 | 正在审查纳税发票。 一旦纳税发票获准，就会发放付款。                                   | 否                                                         |
| 已拒绝                 | 付款被银行拒绝                                                                                                      | 有关详细信息，请联系银行。                             |
|||

## <a name="export-data-page"></a>“导出数据”页

若要导出数据，请按照下面这些说明操作。

“导出数据”页本身不会刷新。 你可能需要手动刷新此页，才能看到最新数据。

筛选器可能会导致“无可用数据”错误出现。 这可能意味着，你保持默认选择的三个月时间段不变，然后又选择了超出此时间段的收入中的付款 ID。 请延长时间段，然后重试。

## <a name="payments"></a>付款

![导出付款](./media/pc-export-payments.png)

使用此选项，可以下载你的银行收到的给定计划的付款、相关税款和总收入金额。 因为此报告用于许多合作伙伴中心计划，所以你的报告中可能没有某些列。 下面介绍了这些列。

| 列名称              | 说明                                                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------  |
| participantID            | 计划下合作伙伴收入的主要标识                                                                             |
| participantIdType        | 通常为激励计划的计划 ID 和应用商店计划的卖家 ID                                                                |
| participantName          | 收入合作伙伴的名称                                                                                                               |
| programName              | 激励计划/应用商店计划的名称                                                                                                              |
| earned                   | 计划/participantID 赚取的金额（以付款币种为单位）                                                                       |
| earnedUSD                | 计划/participantID 赚取的金额（以美元为单位）                                                                                      |
| withheldTax              | 计划/participantID 的预扣税款金额（以付款币种为单位）                                                               |
| salesTax                 | 计划/participantID 的销售税总金额（以付款币种为单位）（仅适用于激励计划）                   |
| serviceFeeTax            | 计划/participantID 的 serviceFeeTax 总金额（以付款币种为单位）（仅适用于应用商店计划和 Azure 市场） |
| totalPayment             | 计划/participantID 的总付款（以本国币种为单位），不含预扣税款，但包括销售税（若有）   |
| currencyCode             | 付款币种代码                                                                                                                      |
| paymentMethod            | 向合作伙伴付款的方式（例如，电子银行转帐、贷方通知单）                                                     |
| paymentID                | 付款的唯一标识符。 此编号通常显示在银行对帐单中（仅适用于 SAP 付款）。              |
| paymentStatus            | 付款状态                                                                                                                            |
| paymentStatusDescription | 付款状态的易记说明                                                                                                    |
| paymentDate              | Microsoft 发放付款的日期                                                                                                      |
|||

## <a name="transaction-history"></a>交易历史记录

![导出交易历史记录](./media/pc-export-transaction.png)

使用此选项，可以下载“交易历史记录”页中的每个收入行项，即收入类型、日期、相关交易金额、客户、产品和其他适用于计划的交易详细信息。

| 列名称                    | 说明                                                                                                                              | 是否适用于激励/应用商店/Azure 市场           |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------|
| earningId                      | 每笔收入的唯一标识符                                                                                                       | All                                                            |
| participantID                  | 计划下合作伙伴收入的主要标识                                                                            | All                                                            |
| participantIdType              | 主要是激励计划的计划 ID，以及应用商店计划和 Azure 市场的卖家 ID                                          | All                                                            |
| participantName                | 收入合作伙伴的名称                                                                                                              | All                                                            |
| partnerCountryCode             | 收入合作伙伴所在的位置/国家/地区                                                                                                  | All                                                            |
| programName                    | 激励计划/应用商店计划的名称                                                                                                             | All                                                            |
| transactionId                  | 交易的唯一标识符                                                                                                    | All                                                            |
| transactionCurrency            | 原始客户交易发生时使用的币种（这不是合作伙伴所在位置的币种）                                     | All                                                            |
| transactionDate                | 交易日期。 适用于多次交易促成一笔收入的计划                                           | All                                                            |
| transactionExchangeRate        | 用于显示相应交易金额（以美元为单位）的汇率日期                                                                 | All                                                            |
| transactionAmount              | 交易金额（以产生收入的原始交易币种为单位）                                              | All                                                            |
| transactionAmountUSD           | 交易金额（以美元为单位）                                                                                                                | All                                                            |
| lever                          | 表示收入的业务规则                                                                                                  | All                                                            |
| earningRate                    | 用于交易金额以生成收入的激励率                                                                      | All                                                            |
| quantity                       | 因计划而异。 表示交易计划的已计费数量                                                            | All                                                            |
| quantityType                   | 表示数量类型（例如，“已计费数量”、“MAU”）                                                                             | All                                                            |
| earningType                    | 表示是否是费用、返点、合作收入、销售收入等                                                                                          | All                                                            |
| earningAmount                  | 收入金额（以原始交易币种为单位）                                                                                      | All                                                            |
| earningAmountUSD               | 收入金额（以美元为单位）                                                                                                                    | All                                                            |
| earningDate                    | 收入日期                                                                                                                      | All                                                            |
| calculationDate                | 在系统中计算收入的日期                                                                                            | All                                                            |
| earningExchangeRate            | 用于显示相应金额（以美元为单位）的汇率                                                                                  | All                                                            |
| exchangeRateDate               | 用于计算 EarningAmount（以美元为单位）的汇率日期                                                                                   | All                                                            |
| paymentAmountWOTax             | 收入金额（不含税）（以付款币种为单位），仅针对状态为“已发送”的付款显示                                                                 | All                                                            |
| paymentCurrency                | 合作伙伴在付款配置文件中选择的付款币种。 仅针对状态为“已发送”的付款显示                                                   | All                                                            |
| paymentExchangeRate            | 用于使用 ExchangeRateDate 计算 paymentAmountWOTax（以付款币种为单位）的汇率                                            | All                                                            |
| claimId                        | 索赔的唯一标识符                                                                                                              | 激励 - 仅某些计划                                |
| planId                         | 计划的唯一标识符                                                                                                               | 激励 - 仅某些计划                                |
| paymentId                      | 付款的唯一标识符。 此编号通常显示在银行对帐单中                                                 | 仅适用于 SAP 付款                                              |
| paymentStatus                  | 付款状态                                                                                                                           | All                                                            |
| paymentStatusDescription       | 付款状态的易记说明                                                                                                   | All                                                            |
| customerId                     | 始终为空白                                                                                                                     | 仅激励计划（例外：OEM）和 Azure 市场 |
| customerName                   | 始终为空白                                                                                                                     | 仅激励计划（例外：OEM）和 Azure 市场 |
| partNumber                     | 始终为空白                                                                                                                     | 某些激励计划和应用商店计划以及 Azure 市场        |
| productName                    | 关联到交易的产品名称                                                                                                       | All                                                            |
| productId                      | 唯一产品标识符                                                                                                                | 应用商店和 Azure 市场                                    |
| parentProductId                | 唯一父产品标识符。 如果交易没有父产品，则父产品 ID = 产品 ID。 | 应用商店和 Azure 市场                                    |
| parentProductName              | 父产品名称。 如果交易没有父产品，则父产品名称 = 产品名称。   | 应用商店和 Azure 市场                                    |
| productType                    | 产品类型（如应用、加载项和游戏）                                                                                        | 应用商店和 Azure 市场                                    |
| invoiceNumber                  | 发票编号（仅适用于 EA）                                                                                                  | 激励和 Azure 市场 - 仅某些计划           |
| subscriptionId                 | 与客户关联的订阅标识符                                                                                         | 激励 - 仅某些计划                                 |
| subscriptionStartDate          | 订阅开始日期                                                                                                                  | 激励 - 仅某些计划                                 |
| subscriptionEndDate            | 订阅结束日期                                                                                                                    | 激励 - 仅某些计划                                 |
| resellerId                     | 经销商标识符                                                                                                                      | 激励 - 仅某些计划                                 |
| resellerName                   | 经销商名称                                                                                                                            | 激励 - 仅某些计划                                 |
| distributorId                  | 分销商标识符                                                                                                                   | 激励 - 仅某些计划                                 |
| distributorName                | 分销商名称                                                                                                                         | 激励 - 仅某些计划                                 |
| agreementNumber                | 协议编号                                                                                                                         | 激励 - 仅某些计划                                 |
| AgreementStartDate             | 协议开始日期                                                                                                                     | 激励 - 仅某些计划                                 |
| AgreementEndDate               | 协议结束日期                                                                                                                       | 激励 - 仅某些计划                                 |
| workload                       | 工作负荷                                                                                                                                 | 激励 - 仅某些计划                                 |
| TransactionType                | 交易类型（如购买、退款、冲销或退单）                                                               | 应用商店和 Azure 市场                                    |
| localProviderSeller            | 记录的本地提供商/卖家                                                                                                          | 仅应用商店                                                     |
| taxRemitted                    | 汇出的税款金额（销售税、使用税或 VAT/GST 税）                                                                                   | 应用商店和 Azure 市场                                    |
| taxRemitModel                  | 汇出税款（销售税、使用税或 VAT/GST 税）的责任方                                                                    | 仅应用商店                                                     |
| storeFee                       | Microsoft 保留金额，用于支付在应用商店中提供应用或加载项的费用                                           | 仅应用商店                                                     |
| transactionPaymentMethod       | 用于交易的客户付款方式（如银行卡、移动运营商计费或 PayPal）                                | 应用商店和 Azure 市场                                    |
| tpan                           | 表示第三方广告网络                                                                                                     | 应用商店 - 仅广告                                               |
| customerCountry                | 客户所在国家/地区                                                                                                                         | 应用商店和 Azure 市场                                    |
| customerCity                   | 客户所在城市                                                                                                                            | 应用商店和 Azure 市场                                    |
| customerState                  | 客户所在省/自治区/直辖市                                                                                                                           | 应用商店和 Azure 市场                                    |
| customerZip                    | 客户邮政编码                                                                                                                 | 应用商店和 Azure 市场                                    |
| purchaseTypeCode               | 始终为空白                                                                                                                     | 激励计划 - CRI                                        |
| purchaseOrderType              | 始终为空白                                                                                                                     | 激励计划 - CRI                                        |
| purchaseOrderCoverageStartDate | 始终为空白                                                                                                                     | 激励计划 - CRI                                        |
| purchaseOrderCoverageEndDate   | 始终为空白                                                                                                                     | 激励计划 - CRI                                        |
| programOfferingLevel           |                                                                                                                                          | 激励计划 - CRI                                        |
| TenantID                       |                                                                                                                                          | 激励计划                                             |
| externalReferenceId            | 计划的唯一标识符                                                                                                        | 直接付款计划（激励和应用商店）                      |
| externalReferenceIdLabel       | 唯一标识符标签                                                                                                                  | 直接付款计划（激励和应用商店）                      |
|||

## <a name="historical-statements"></a>历史对帐单

![导出历史对帐单](./media/pc-export-statements.png)

2019 年 7 月 1 日之前的交易历史记录另行处理。 对帐单将使用以下字段，而不是当前字段。

> [!NOTE]
> 旧式交易历史记录中有一个名为“预留”的列，它对应于新式历史记录中的“收入”列，区别在于它不包括状态为“付款已发送”的所有收入。

> [!NOTE]
> 3M、6M 或 12M 等筛选器不适用于“历史对帐单”部分。

| 字段名              | 说明                                                                                                                                                             |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 收入来源          | 收入来源，取决于发生交易的位置，如 Microsoft Store、Windows Phone 应用商店、Microsoft Store 8 或广告                  |
| 订单 ID                | 唯一订单标识符。 使用此 ID，可以识别购买交易及其各自的非购买交易（如退款或退单）。 两者具有相同的订单 ID。 此外，对于在一次购买中使用多种付款方式的拆分费用，它可便于关联购买交易。 |
| Transaction ID          | 唯一交易标识符。                                                                                                                                          |
| 交易日期/时间   | 交易发生的日期和时间 (UTC)。                                                                                                                       |
| 父产品 ID       | 唯一父产品标识符。 如果交易没有父产品，则父产品 ID = 产品 ID。                                |
| 产品 ID              | 唯一产品标识符。                                                                                                                                              |
| 父产品名称     | 父产品名称。 如果交易没有父产品，则父产品名称 = 产品名称。                                  |
| 产品名称            | 产品名称                                                                                                                                                    |
| 产品类型            | 产品类型（如应用、加载项和游戏）                                                                                                                       |
| 数量                | 当“收入来源”为“适用于企业的 Microsoft Store”时，“数量”表示购买的许可证数量。 对于其他所有收入来源，“数量”始终为“1”。 即使因为使用了两种不同的付款方式而将一项交易拆分为两个行项，每个行项也都显示值为“1”的“数量”。 |
| 事务类型        | 交易类型（如购买、退款、冲销或退单）                                                                                              |
| 付款方式          | 用于交易的客户付款方式（如银行卡、移动运营商计费或 PayPal）                                                               |
| 国家/地区        | 发生交易的国家/地区                                                                                                                          |
| 本地提供商/卖家 | 记录的本地提供商/卖家                                                                                                                                        |
| 交易币种    | 交易币种                                                                                                                                            |
| 交易金额      | 交易金额                                                                                                                                              |
| 汇出税款            | 汇出的税款金额（销售税、使用税或 VAT/GST 税）                                                                                                                  |
| 净收入            | 交易金额减去汇出的税款金额                                                                                                                                   |
| 应用商店费用               | Microsoft 保留的净收入百分比，用于支付在应用商店中提供应用或加载项的费用                                                      |
| 应用收款            | 净收入减去应用商店费用                                                                                                                                       |
| 预扣税款          | 预扣的所得税金额（不包含在“预留”CSV 文件中）                                                                                                |
| 付款                 | 应用收款减去任何相应所得税预扣金额（金额以交易币种显示）。 不包含在“预留”CSV 文件中。                               |
| 外汇汇率                 | 用于将交易币种转换为付款币种的外汇汇率                                                                                         |
| 付款币种        | 付款使用的币种                                                                                                                                       |
| 转换后的付款       | 使用外汇汇率转换为付款币种的付款金额                                                                                                         |
| 税款汇出模型         | 汇出税款（销售税、使用税或 VAT/GST 税）的责任方                                                                                                   |
| 资格日期/时间   | 交易收款符合成为付款的条件的日期和时间 (UTC)。 在付款创建后，它包括“资格日期/时间”先于付款创建日期的交易收款（只包含在“预留”CSV 文件中）。 |
| Charges                 | 显示“交易金额”列中聚合的所有费用详细信息的细目（只对 Azure 市场包括，不包括在“预留”CSV 文件中）。 |
|||

## <a name="next-step"></a>下一步

- [付款策略详细信息](./payout-policy-details.md)
