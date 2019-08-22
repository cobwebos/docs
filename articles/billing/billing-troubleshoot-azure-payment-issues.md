---
title: 排查 Azure 付款问题
description: 解决在 Microsoft Azure 门户或帐户中心更新付款信息帐户时遇到的问题。
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657081"
---
# <a name="troubleshoot-azure-payment-issues"></a>排查 Azure 付款问题

尝试更新 Microsoft Azure 门户或 Azure 帐户中心的付款信息帐户时, 可能会遇到问题。 在解决问题之前, 请考虑以下准则:

- 确保为 Azure 帐户配置文件提供的信息 (包括联系人电子邮件地址、街道地址和电话号码) 正确无误。
- 确保信用卡信息正确无误。
- 确保尚未拥有包含相同信息的 Microsoft 帐户。

## <a name="issues"></a>问题

若要解决任何错误, 请选择尝试注册 Azure 时遇到的问题。

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>无法从保存的帐单支付方法中删除信用卡

按照设计, 您不能从活动订阅中删除信用卡。

如果必须删除现有卡, 则必须将新卡片添加到该订阅, 以便可以成功删除旧付款方式, 或者必须取消该订阅。 这会永久删除该订阅, 并删除卡。

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>添加新的付款方式之后, 无法删除旧支付方法

新付款方式可能未与订阅关联。 若要帮助将付款方式与订阅相关联, 请参阅[添加、更新或删除 Azure 的信用卡或借记卡](billing-how-to-change-credit-card.md)。

若要解决与拒绝的卡有关的问题, 请参阅[如何在 Azure 注册时排查拒绝的卡](billing-troubleshoot-declined-card.md)问题。

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>无法删除付款方式, 因为*无法删除付款方式*错误

出现这种情况的原因是未完成平衡。 删除付款方式之前, 请清除所有未完成的余额。

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>无法在 "我的帐户" 下查看订阅以更新付款方式

你可能使用的电子邮件 ID 不同于订阅所用的电子邮件 ID。

若要解决此问题, 请参阅 "[找不到订阅 Azure 门户或 Azure 帐户中心的登录错误](billing-no-subscriptions-found.md)"。

### <a name="unable-to-make-payment-for-a-subscription"></a>无法支付订阅费用

如果收到错误消息:*付款已过期。你的付款方式*存在问题, 或者*我们很抱歉, 无法保存该信息。关闭浏览器, 然后重试。* , 则卡片上有挂起的付款, 因为你的金融机构拒绝了该卡。

验证信用卡是否有足够的余额来支付。 如果不是这样, 请使用另一个卡来支付费用, 或与金融机构联系以解决问题。

请与银行确认以下问题:

- 不启用国际交易。
- 该卡具有信用额度, 必须结算余额。
- 在卡上启用定期支付。

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>无法更改付款方式, 因为浏览器出现问题 (浏览器没有响应、未加载, 等等)

注销所有活动的 Azure 会话, 然后按照[浏览 Microsoft edge 中的 inprivate 一文](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate)中的步骤, 在 microsoft Edge 或 Internet Explorer 中启动 inprivate 会话。

在 "私人会话" 中, 按照[如何更改信用卡](billing-how-to-change-credit-card.md)中的步骤来更新或更改信用卡信息。

还可以尝试执行以下操作:

- 刷新浏览器
- 使用其他浏览器
- 删除缓存的 cookie

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>更新付款方式后, 我的订阅仍处于禁用状态。

发生此问题的原因是未完成平衡。 删除付款方式之前, 请清除所有未完成的余额。

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>无法更改付款方式, 因为出现 XML 错误响应页

如果使用[Azure 门户](https://portal.azure.com/)添加新信用卡, 会收到此消息。

若要添加卡详细信息, 请使用帐户管理员的电子邮件地址登录到 Azure 帐户门户。

## <a name="additional-help-resources"></a>其他帮助资源

Azure 计费和订阅的其他疑难解答文章

- [银行卡被拒绝](billing-troubleshoot-declined-card.md)
- [订阅登录问题](billing-troubleshoot-sign-in-issue.md)
- [找不到任何订阅](billing-no-subscriptions-found.md)
- [企业成本视图已禁用](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- [Azure 计费文档](index.md)
