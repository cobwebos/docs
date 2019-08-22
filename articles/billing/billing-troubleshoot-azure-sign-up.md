---
title: 排查 Azure 注册问题
description: 解决在 Microsoft Azure 门户帐户中心尝试注册新帐户时出现的问题。
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
ms.author: v-miegge
ms.openlocfilehash: 57dc9c05778563bc0c5df1a8ac8895b51008b01f
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657068"
---
# <a name="troubleshoot-azure-sign-up"></a>排查 Azure 注册问题

尝试在 Microsoft Azure 门户或 Azure 帐户中心注册新帐户时, 可能会遇到问题。 在解决问题之前, 请先验证以下内容:

- 你为 Azure 帐户配置文件提供的信息 (包括联系人电子邮件地址、街道地址和电话号码) 正确无误。
- 信用卡信息正确。
- 你还没有 Microsoft 帐户具有相同的信息。

## <a name="resolutions"></a>解决方法

若要解决任何错误, 请选择尝试注册 Azure 时遇到的问题。

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>错误：*由于你的帐户存在问题, 我们无法继续注册。请联系计费支持人员。*

若要解决该问题，请执行以下步骤：

1. 使用帐户管理员凭据登录到 [Azure 帐户中心](https://account.azure.com/Profile)。

2. 选择“编辑详细信息”。

3. 确保所有地址字段都已完成并且有效。

4. 注册 Azure 订阅时, 请确保信用卡注册的帐单地址与银行记录匹配。

如果继续收到错误消息, 请尝试使用其他浏览器进行注册。

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>进度栏在 "*按卡身份验证*" 部分中挂起。

要通过卡完成身份验证，则浏览器必须允许第三方 cookie。

![通过卡进行身份验证](./media/billing-troubleshoot-azure-sign-up/identify-verification-by-card.png)
 
使用以下步骤更新浏览器的 cookie 设置。

1. 如果使用的是 Chrome, 请选择 "**设置** > " "**显示高级设置** > "**隐私** > **内容设置**"。 清除 "**阻止第三方 cookie 和站点数据**"。

2. 如果使用的是 Microsoft Edge, 请选择 "**设置** > " "**查看高级设置** > " "**cookie** > **不阻止 cookie**"。

3. 刷新 Azure 注册页, 然后检查问题是否已解决。

4. 如果刷新未解决此问题, 请退出并重新启动浏览器, 然后重试。

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>信用卡窗体不支持我的帐单地址

你的帐单地址必须位于你在 "**关于**" 部分中选择的国家/地区。 请确保选择了正确的国家/地区。

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>注册帐户验证过程中没有收到短信或电话

尽管该过程通常很简单, 但要交付验证码最多可能需要四分钟。 你为验证输入的电话号码不会存储为帐户的联系号码。

下面是一些其他提示：

- 电话语音 (VoiP) 电话号码不能用于电话验证过程。
- 仔细检查输入的电话号码, 包括下拉菜单中所选的国家/地区代码。
- 如果你的手机未收到短信 (SMS)，请尝试使用“呼叫我”选项。
- 确保电话可以接收来自基于美国电话号码的呼叫或短信。

当你获取短信或电话呼叫时, 请在文本框中输入你收到的代码。

### <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒绝或不被接受

对于 Azure 订阅, 不接受虚拟或预付信用卡或借记卡支付。 若要查看其他可能导致卡被拒绝的原因, 请参阅在[Azure 注册中拒绝](https://support.microsoft.com/help/4042960)借记卡或信用卡。

### <a name="free-trial-is-not-available"></a>免费试用版不可用

是否曾经使用过 Azure 订阅？ Azure 使用条款协议仅向 Azure 新用户授予免费试用版激活权限。 如果已拥有任何其他类型的 Azure 订阅，则无法激活免费试用版。 请考虑注册[即用即付订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>我看到我的免费试用帐户上产生了费用

注册后, 你可能会看到你的信用卡帐户上的小型验证保留。 这会在三到五天内删除。 如果你为如何管理成本而担忧，请阅读有关[防止意外成本](billing-getting-started.md)的详细信息。

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>无法激活 MSDN、BizSpark、BizSparkPlus 或 MPN 等 Azure 权益计划

请确保使用正确的登录凭据。 然后, 查看福利计划, 确保你有资格。

- MSDN 
  - 在[MSDN 帐户页](https://msdn.microsoft.com/subscriptions/manage/default.aspx)中验证资格状态。
  - 如果你无法验证你的状态, 请与[MSDN 订阅客户服务中心](https://msdn.microsoft.com/library/aa493452.aspx)联系。
- 适用于初创企业的 Microsoft
  - 登录到[microsoft for 启动门户](https://startups.microsoft.com/#start-two), 验证 microsoft 的资格状态以进行启动。
  - 如果你无法验证状态, 则可以在[Microsoft for 启动论坛](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)上获取帮助。
- MPN 
  - 登录到[MPN 门户](https://mspartner.microsoft.com/Pages/Locale.aspx), 验证资格状态。 如果拥有相应的[云平台能力](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)，可能会符合其他权益的资格。
  - 如果无法验证状态, 请联系[MPN 支持](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)。

### <a name="cant-activate-new-azure-in-open-subscription"></a>无法激活新的 Azure 开放订阅

若要创建 Azure 开放订阅, 你必须有一个有效的联机服务激活 (OSA) 密钥, 该密钥至少具有一个与之关联的 Azure 开放服务。 如果没有 OSA 密钥, 请联系[microsoft](http://pinpoint.microsoft.com/)确定中列出的 microsoft 合作伙伴之一。

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>错误：*你不具备 Azure 订阅的资格*

若要解决此问题, 请仔细检查以下各项是否成立:

- 你为 Azure 帐户配置文件提供的信息 (包括联系人电子邮件地址、街道地址和电话号码) 正确无误。
- 信用卡信息正确。
- 你还没有使用相同信息的 Microsoft 帐户。

### <a name="error-your-current-account-type-is-not-supported"></a>错误：*当前的帐户类型不受支持*

如果帐户在[非托管 Azure AD 目录](../active-directory/users-groups-roles/directory-self-service-signup.md)中注册, 并且它不在组织的 Azure AD 目录中, 则可能出现此问题。 

若要解决此问题, 请使用其他帐户注册 Azure 帐户, 或接管非托管的 AD 目录。 有关详细信息, 请参阅[在 Azure Active Directory 中以管理员身份接管非托管目录](../active-directory/users-groups-roles/domains-admin-takeover.md)。
 
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
