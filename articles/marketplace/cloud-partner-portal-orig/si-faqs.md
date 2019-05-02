---
title: Seller Insights FAQ
description: 有关云合作伙伴门户的 Seller Insights 功能的常见问题解答。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 2719b6b47225576f2eadeb5e5b40b3aa7b39444d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943093"
---
<a name="seller-insights-faq"></a>Seller Insights FAQ
===================

本文介绍了如何在 Seller Insights 内执行常见用户过程，以及 Seller Insights 常见问题解答。


<a name="find-definitions-for-the-values-in-the-downloaded-transaction-file"></a>查找已下载交易文件中值的定义
------------------------------------------------------------------

有关交易文件中指标值的定义，请参阅 [Seller Insights 定义](./si-insights-definitions-v4.md)一文。


<a name="see-customer-details-of-transactions-for-which-ive-been-paid"></a>查看已付款交易的客户详细信息
-------------------------------------------------------------

从“付款”模块下载交易后，找到“付款状态”列，再应用筛选器，以筛选出“已付款”值。 以下各列将出现包含客户详细信息：**公司名称**，**客户电子邮件**，**客户所在国家/地区**，**客户状态**，并且**客户邮政编码**。


<a name="calculate-my-open-accounts-receivable"></a>计算我的未结应收帐款
-------------------------------------

从“付款”模块下载交易后，找到“付款状态”列，再应用筛选器，以筛选出“即将付款”和“未准备好付款”值。 然后，对“付款金额(PC)”列进行求和。


<a name="calculate-revenue-by-customer-usage-period"></a>按客户使用期计算收入
------------------------------------------

下载后您的交易记录付款模块中，找到标记为的列 **事务状态**，并筛选"付费"的值。   为列出的每个事务，标记为的列 **付款金额 (PC)** 表示已支付的金额。  若要估计与事务相关联的使用期限，请使用该列 **收费日期**，这是近似的事务应用于段内的最后一天的使用情况。


<a name="calculate-your-bad-debt"></a>计算呆帐
---------------------

从“付款”模块下载交易后，找到“最终收款状态”列，再应用筛选器，以筛选出“勾销”值。 然后，对“付款金额(PC)”列进行求和。


<a name="view-payout-or-customer-contact-information"></a>查看付款或客户联系信息
-------------------------------------------

以担任“所有者”角色（而不是“参与者”角色）的用户身份登录。 只有所有者角色，才能查看付款和客户信息。 若要详细了解用户角色，请参阅[管理用户](./cloud-partner-portal-manage-users.md)一文。


<a name="calculate-my-advance-payouts"></a>计算我的预付款
----------------------------

从“付款”模块下载交易后，找到“交易类型”列，再应用筛选器，以筛选出“收费”值。 接下来，找到“最终收款状态”列，再应用筛选器，以筛选出“付款中”值。 最后，对“付款金额(PC)”列进行求和，以计算在从客户处收款前的所有预付款。


<a name="calculate-customer-refunds"></a>计算客户退款
--------------------------

从“付款”模块下载交易后，找到“最终收款状态”列，再应用筛选器，以筛选出“退款”值。 对“费用金额(PC)”列进行求和，以计算已处理的所有客户退款。


<a name="identify-which-transactions-involved-a-microsoft-channel-partner"></a>确定哪些交易涉及 Microsoft 渠道合作伙伴
----------------------------------------------------------------

“Azure 许可证类型”列中筛选出的所有“通过经销商的企业”和“云解决方案提供商”交易值都涉及 Microsoft 渠道合作伙伴。 若要获取合作伙伴的更多详情，可查看“付款”模块下载内容和“客户”模块下载内容中的“经销商名称”和“经销商电子邮件地址”。


<a name="identify-trial-usage-and-trial-conversions"></a>确定试用情况和试用转化情况
------------------------------------------

“订单”、“使用情况”和“付款”模块下载内容中现包含“试用结束日期”，有助于了解特定订单的试用期结束时间（如果适用）。 若要查看试用情况和订单，请在下载内容中找到“SKU 计费类型”列，再应用筛选器，以筛选出“试用”值。 若要查看试用转化情况，请在下载内容中找到“试用结束日期”列，再应用筛选器，以筛选出“试用结束日期”晚于今天日期且“取消日期”列为空或晚于“试用结束日期”的订单。


<a name="when-is-my-monthly-payout-calculated"></a>何时计算我的每月付款
------------------------------------

每月 15 号前向你支付上月最后一个日历日前可付款的所有金额。 在每月的第三天，Microsoft 计算上月的付款金额，并将下载内容中所有适用费用交易的“付款状态”列值更新为“即将付款”。 这些交易会一直保持此状态，直到付款请求发送至银行帐户，届时“付款状态”将更新为“已付款”，并且“付款日期”将更新为显示我们向银行提交付款请求的日期。


<a name="calculate-customer-acquisition-and-loss"></a>计算客户获取率和流失率
---------------------------------------

通过在“客户”模块下载内容中找到“获取日期”列，可查看客户首次购买你的任一服务/产品的日期。 同样，通过在“客户”模块下载内容中找到“流失日期”列，可查看客户不再拥有你发布的任何产品/服务的日期。


<a name="finding-more-help"></a>寻求更多帮助
-----------------

- [Seller Insights 定义](./si-insights-definitions-v4.md) - 查找指标和数据的定义

- [Seller Insights 入门](./si-getting-started.md) - Seller Insights 功能简介。

