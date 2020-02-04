---
title: 排查 Azure 注册问题
description: 解决尝试在 Microsoft Azure 门户帐户中心注册新帐户时遇到的问题。
services: billing
author: v-miegge
manager: dcscontentpm
editor: v-jesits
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: e13a55143e554a55a2902fc2350f6fde6a8afb09
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "75989288"
---
# <a name="troubleshoot-azure-sign-up"></a>排查 Azure 注册问题

尝试在 Microsoft Azure 门户或 Azure 帐户中心注册新帐户时，可能会遇到问题。 对问题进行故障排除前，请先验证以下内容：

- 为 Azure 帐户个人资料提供的信息（包括联系电子邮件地址、街道地址和电话号码）正确无误。
- 信用卡信息正确无误。
- 尚未拥有包含相同信息的 Microsoft 帐户。

## <a name="resolutions"></a>解决方法

若要解决错误，请选择你尝试注册 Azure 时遇到的问题。

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>错误：“由于你的帐户存在问题，我们无法处理注册。  请联系计费支持人员。”

若要解决该问题，请执行以下步骤：

1. 使用帐户管理员凭据登录到 [Azure 帐户中心](https://account.azure.com/Profile)。

2. 选择“编辑详细信息”  。

3. 确保所有地址字段都已完成并且有效。

4. 注册 Azure 订阅时，请确保信用卡注册的账单地址与银行记录相符。

如果继续收到错误消息，请尝试使用其他浏览器注册。

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>“使用卡进行身份验证”部分中的进度条挂起。 

要通过卡完成身份验证，则浏览器必须允许第三方 cookie。

![通过卡进行身份验证](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

使用以下步骤更新浏览器的 cookie 设置。

1. 如果使用的是 Chrome，请选择“设置”   > “显示高级设置”   > “隐私”   > “内容设置”  。 清除“阻止第三方 Cookie 和站点数据”  。

2. 如果使用的是 Microsoft Edge，请选择“设置”   > “查看高级设置”   > “Cookie”   > “不阻止 Cookie”  。

3. 刷新 Azure 注册页，然后检查问题是否得以解决。

4. 如果刷新后未解决此问题，请退出并重启浏览器，然后重试。

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>信用卡窗体不支持我的帐单地址

你的账单地址必须位于你在“关于你”部分中选择的国家/地区内。  确保选择了正确的国家/地区。

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>注册帐户验证过程中没有收到短信或电话

虽然此过程通常很快，但可能需要花费长达四分钟的时间验证码才会送达。 输入的电话号码仅用于验证，不会存储为帐户的联系号码。

下面是一些其他提示：

- 电话验证过程中不能使用基于 IP 的语音 (VoiP) 电话号码。
- 再次确认输入的电话号码，包括在下拉菜单中选择的国家/地区代码。
- 如果你的手机未收到短信 (SMS)，请尝试使用“呼叫我”  选项。
- 确保你的手机可以收到从基于美国的电话号码打来的电话或发来的短信消息。

收到短信或电话呼叫时，在文本框中输入你收到的代码。

### <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒绝或不被接受

虚拟或预付信用卡或借记卡不能作为 Azure 订阅有效的付款选项。 要查看可能导致卡被拒绝的其他原因，请参阅[借记卡或信用卡在注册 Azure 时被拒绝](https://support.microsoft.com/help/4042960)。

### <a name="free-trial-is-not-available"></a>免费试用版不可用

是否曾经使用过 Azure 订阅？ Azure 使用条款协议仅向 Azure 新用户授予免费试用版激活权限。 如果已拥有任何其他类型的 Azure 订阅，则无法激活免费试用版。 请考虑注册[即用即付订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>我看到我的免费试用帐户上产生了费用

在注册后，你可能会看到你的信用卡帐户上存在少量验证费用， 这在 3 到 5 天内会被删除。 如果你为如何管理成本而担忧，请阅读有关[防止意外成本](getting-started.md)的详细信息。

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>无法激活 MSDN、BizSpark、BizSparkPlus 或 MPN 等 Azure 权益计划

请确保使用正确的登录凭据。 然后检查权益计划，以确保符合资格。

- MSDN
  - 在 [MSDN 帐户页](https://msdn.microsoft.com/subscriptions/manage/default.aspx)上验证资格状态。
  - 如果无法验证资格状态，请联系 [MSDN 订阅客服中心](https://msdn.microsoft.com/library/aa493452.aspx)。
- 适用于初创企业的 Microsoft
  - 登录到[适用于初创企业的 Microsoft 门户](https://startups.microsoft.com/#start-two)，验证你的资格状态，看你是否符合适用于初创企业的 Microsoft 的资格。
  - 如果无法验证你的状态，可以在[适用于初创企业的 Microsoft 论坛](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)上获取帮助。
- MPN
  - 登录到 [MPN 门户](https://mspartner.microsoft.com/Pages/Locale.aspx)，验证资格状态。 如果拥有相应的[云平台能力](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)，可能会符合其他权益的资格。
  - 如果无法验证资格状态，请联系 [MPN 支持](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)。

### <a name="cant-activate-new-azure-in-open-subscription"></a>无法激活新的 Azure 开放许可订阅

若要创建 Azure 开放许可订阅，必须具备有效的在线服务激活 (OSA) 密钥，该密钥至少有一个与之关联的 Azure 开放许可令牌。 如果没有 OSA 密钥，请联系 [Microsoft Pinpoint](https://pinpoint.microsoft.com/) 中列出的其中一个 Microsoft 合作伙伴。

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>错误：你不符合 Azure 订阅资格 

若要解决这一问题，请仔细检查以下项是否属实：

- 为 Azure 帐户个人资料提供的信息（包括联系电子邮件地址、街道地址和电话号码）正确无误。
- 信用卡信息正确无误。
- 尚未拥有使用相同信息的 Microsoft 帐户。

### <a name="error-your-current-account-type-is-not-supported"></a>错误：你当前的帐户类型不受支持 

如果帐户注册在[非托管 Azure AD 目录](../../active-directory/users-groups-roles/directory-self-service-signup.md)中，不在组织的 Azure AD 目录中，则可能出现此问题。

若要解决此问题，请使用另一帐户注册 Azure 帐户，或者接管非托管 AD 目录。 有关详细信息，请参阅[在 Azure Active Directory 中以管理员身份接管非托管目录](../../active-directory/users-groups-roles/domains-admin-takeover.md)。

## <a name="additional-help-resources"></a>其他帮助资源

Azure 计费和订阅的其他疑难解答文章

- [银行卡被拒绝](troubleshoot-declined-card.md)
- [订阅登录问题](troubleshoot-sign-in-issue.md)
- [找不到任何订阅](no-subscriptions-found.md)
- [企业成本视图已禁用](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- [Azure 计费文档](../../billing/index.md)
