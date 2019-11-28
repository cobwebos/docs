---
title: 为 Azure 客户打开银行 (PSD2) 和强客户身份验证 (SCA)
description: 本文解释了某些 Azure 购买项目为何需要多重身份验证，以及如何完成身份验证。
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: db6b78986ae8abfe2c8ae3296d07c327cc041726
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223689"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>为 Azure 客户打开银行 (PSD2) 和强客户身份验证 (SCA)

从 2019 年 9 月 14 日开始，[欧洲经济区](https://en.wikipedia.org/wiki/European_Economic_Area)的 31 个国家的银行需要在处理付款之前验证在线购买者的身份。 此项验证需要通过多重身份验证来帮助确保在线购物安全且受到保护。 在某些国家/地区，此项验证要求的日期将会延期。 有关详细信息，请参阅[有关 PSD2 的 Microsoft 常见问题解答](https://support.microsoft.com/en-us/help/4517854?preview)。

## <a name="what-psd2-means-for-azure-customers"></a>PSD2 对 Azure 客户的意义

如果使用[欧洲经济区](https://en.wikipedia.org/wiki/European_Economic_Area)银行颁发的信用卡支付 Azure 费用，则你可能需要针对帐户的付款方式完成多重身份验证。 注册 Azure 帐户或升级 Azure 帐户时，系统可能会提示你完成多重身份验证质询 — 即使你当时并未购物。 此外，在更改 Azure 帐户的付款方式、消除支出上限或者在 Azure 门户中立即付款（例如，结算未付余款或购买 Azure 额度）时，系统也可能会要求你提供多重身份验证。

如果银行拒绝了你的每月 Azure 费用，Azure 会向你发送一封逾期未付电子邮件，其中包含有关解决此问题的说明。 可以完成多重身份验证质询，并在 Azure 门户中结算未付费用。

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>在 Azure 门户中完成多重身份验证

以下部分介绍如何在 Azure 门户中完成多重身份验证。 使用适用的信息。

### <a name="change-the-active-payment-method-of-your-azure-account"></a>更改 Azure 帐户的现行付款方式

可通过以下步骤更改 Azure 帐户的现行付款方式：

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com)，并导航到“成本管理 + 计费”。 
2. 在“概述”页上，从“我的订阅”网格中选择相应的订阅。  
3. 在“计费”下，选择“付款方式”。  可以添加新的信用卡或设置现有的信用卡作为订阅的现行付款方式。 如果银行要求执行多重身份验证，在此过程中系统会提示你完成身份验证质询。

有关更多详细信息，请参阅[添加、更新或删除用于 Azure 的信用卡](billing-how-to-change-credit-card.md)。

### <a name="settle-outstanding-charges-for-azure-services"></a>结算 Azure 服务的未付费用

如果银行拒绝了费用，Azure 门户中的 Azure 帐户状态将更改为“逾期未付”。  可遵循以下步骤检查帐户的状态：

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“成本管理 + 计费”中进行搜索。 
3. 在“成本管理 + 计费”的“概述”页上，查看“我的订阅”网格中的状态列。   
4. 如果订阅标记为“逾期未付”，请单击“结算余款”。   在此过程中，系统会提示你完成多重身份验证。

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>结算市场和预留购买项目的未付费用

市场和预留购买项目的计费与 Azure 服务是分开的。 如果银行拒绝了市场或预留费用，Azure 门户中的发票状态将显示为“逾期未付”。  可遵循以下步骤检查市场和预留发票的状态：

1. 以帐户管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“成本管理 + 计费”中进行搜索。 
3. 在“计费”下，选择“发票”。 
4. 单击右侧的“Azure 市场和预留”选项卡。 
5. 选择相应的订阅。
6. 在“发票”网格中查看状态列。 如果发票状态为“逾期”或“逾期未付”，请单击“立即支付”。    在此过程中，系统会提示你完成多重身份验证。

## <a name="next-steps"></a>后续步骤
- 如果需要支付 Azure 帐单，请参阅[结算 Azure 订阅的逾期未付余款](billing-azure-subscription-past-due-balance.md)。
