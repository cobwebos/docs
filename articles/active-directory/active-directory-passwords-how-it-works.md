---
title: "深入探讨：Azure AD SSPR | Microsoft Docs"
description: "Azure AD 自助密码重置深入探讨"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 44426571e3fd8aed090ccccc0dcc46dca8098906
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017

---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Azure AD 中的自助密码重置深入探讨

SSPR 的工作原理 该选项在界面中意味着什么？ 请继续阅读，详细了解 Azure AD 自助密码重置。

## <a name="how-does-the-password-reset-portal-work"></a>密码重置门户的工作原理

当用户导航到密码重置门户时，将启动一个工作流来确定：

   * 如何本地化该页面？
   * 用户帐户是否有效？
   * 该用户属于哪个组织？
   * 该用户的密码在哪个位置管理？
   * 是否已授权该用户使用该功能？


阅读以下步骤，了解有关密码重置页面背后的逻辑。

1. 用户单击“无法访问你的帐户”链接或直接转到 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)。
2. 根据浏览器的区域设置以相应的语言呈现体验内容。 密码重置体验已本地化为 Office 365 支持的相同语言。
3. 用户输入用户 ID 并传递验证码。
4. Azure AD 通过执行以下操作来验证用户是否能够使用此功能：
   * 检查用户是否启用了此功能并分配有 Azure AD 许可证。
     * 如果用户未启用此功能或未分配有许可证，则要求用户联系其管理员重置其密码。
   * 检查用户是否具有针对其帐户定义且符合管理员策略的正确质询数据。
     * 如果策略仅要求一个质询，则确保用户具有针对由管理员策略启用的至少一个质询定义的适当数据。
       * 如果未配置用户，则建议用户联系其管理员重置其密码。
     * 如果策略要求两个质询，则确保用户具有针对由管理员策略启用的至少两个质询定义的适当数据。
       * 如果未配置用户，则我们建议用户联系其管理员重置其密码。
   * 检查是否在本地管理用户密码（联合或密码哈希同步）。
     * 如果已部署写回且在本地管理用户密码，则允许用户继续进行身份验证并重置其密码。
     * 如果未部署写回且在本地管理用户密码，则要求用户联系其管理员重置其密码。
5. 如果确定用户能够成功重置其密码，则将指导用户完成重置过程。

## <a name="authentication-methods"></a>身份验证方法

如果已启用自助密码重置 (SSPR)，则必须选择以下至少一个选项作为身份验证方法。 我们强烈建议至少选择两个身份验证方法，以便为用户提供更大的灵活性。

* 电子邮件
* 移动电话
* 办公电话
* 安全提问

### <a name="what-fields-are-used-in-the-directory-for-authentication-data"></a>在用于身份验证数据的目录中使用哪些字段

* 办公电话对应于办公电话
    * 用户无法自行设置此字段，必须由管理员定义此字段
* 移动电话对应于身份验证电话（不公开）或移动电话（公开）
    * 服务首先查找身份验证电话，然后回退到移动电话（如果不存在）
* 备用电子邮件地址对应于身份验证电子邮件（不公开）或备用电子邮件
    * 服务首先查找身份验证电子邮件，然后回退到备用电子邮件

默认情况下，只有“办公电话”和“移动电话”两个云属性才会从用于身份验证数据的本地目录同步到云目录。

仅当用户在管理员已启用并要求使用的身份验证方法中输入了数据时，他们才能重置其密码。

如果用户不希望在目录中公开其移动电话号码，但仍想使用该号码来重置密码，则管理员不应在目录中填充该号码，而用户必须通过[密码重置注册门户](http://aka.ms/ssprsetup)填充其“身份验证电话”属性。 管理员可在用户的配置文件中看到此信息，但此信息不会发布到其他位置。 如果 Azure 管理员帐户注册了用户的身份验证电话号码，该号码将填充到移动电话字段并且会公开。

### <a name="number-of-authentication-methods-required"></a>所需身份验证方法的数量

此选项确定用户在执行重置或解锁密码过程时必须完成的可用身份验证方法数下限，可设置为 1 或 2。

用户可以选择提供更多的身份验证方法（如果管理员已启用这些方法）。

如果没有为用户注册最少数目的所需方法，他们将看到一个错误页面，让他们请求管理员重置其密码。

### <a name="how-secure-are-my-security-questions"></a>如何保护安全问题

如果你使用安全问题，我们建议结合另一种方法使用这些问题，因为某些人可能知道其他用户的问题的答案，因此这种方法可能比其他方法更不安全。

> [!NOTE] 
> 安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。
>

### <a name="security-question-localization"></a>安全问题本地化

下面的所有预定义问题都已根据用户的浏览器区域设置本地化为 Office 365 的完整语言集。

* 你在哪个城市遇到了你的第一任配偶/伴侣？
* 你的父母在哪个城市相识？
* 与你年龄最近的兄弟姐妹居住在哪个城市？
* 你的父亲在哪个城市出生？
* 你的第一份工作是在哪个城市？
* 你的母亲在哪个城市出生？
* 2000 年元旦你在哪个城市？
* 中学时你最喜欢的老师姓什么？
* 你填报了志愿，但未能入学的大学名称是什么？
* 你举办第一次婚宴的地名是什么？
* 你父亲的中间名是什么？
* 你最喜欢的食品是什么？
* 你外婆的姓氏和名字是什么？
* 你母亲的中间名是什么？
* 你最年长的兄弟姐妹的生日年份和月份是什么？ （例如 1985 年 11 月）
* 你最年长的兄弟姐妹的中间名是什么？
* 你爷爷的姓氏和名字是什么？
* 你最年幼的兄弟姐妹的中间名是什么？
* 六年级时你就读哪所学校？
* 你童年时最好的朋友的名字和姓氏是什么？
* 你的第一位伴侣的名字和姓氏是什么？
* 你最喜欢的小学老师姓什么？
* 你的第一辆汽车或摩托车是哪个品牌和型号？
* 你就读的第一所学校的校名是什么？
* 你在哪家医院出生？
* 你童年时第一个家庭地址的街道名称是什么？
* 你童年时崇拜的人是谁？
* 你最喜欢哪种填充动物玩具？
* 你的第一个宠物叫什么？
* 你童年时的绰号是什么？
* 你在中学最喜欢的运动是什么？
* 你的第一份工作是什么？
* 你童年时的电话号码最后四位数是什么？
* 你年少时，希望长大后当什么？
* 你遇到过的最有名的人是谁？

### <a name="custom-security-questions"></a>自定义安全问题

自定义安全问题尚未根据不同的区域设置本地化。 所有自定义问题的显示语言是在管理用户界面中输入这些问题时所用的语言，即使用户浏览器的区域设置与此不同。 如果需要本地化的问题，请使用预定义的问题。

自定义安全问题的最大长度为 200 个字符。

### <a name="security-question-requirements"></a>安全问题要求

* 答案包含的字符数下限为 3 个字符
* 答案包含的字符数上限为 40 个字符
* 用户不能多次回答同一问题
* 用户不能为多个问题提供相同的答案
* 可以使用任何字符集来定义问题和答案（包括 Unicode 字符）
* 所定义问题的数量必须大于或等于注册所需问题数量

## <a name="registration"></a>注册

### <a name="require-users-to-register-when-signing-in"></a>要求用户在登录时注册

如果启用此选项，将要求已启用密码重置的用户在使用 Azure AD 登录到如下所述的应用程序时完成密码重置注册：

* Office 365
* Azure 门户
* 访问面板
* 联合应用程序
* 使用 Azure AD 的自定义应用程序

如果禁用此功能，用户仍可通过访问 [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) 或单击访问面板中配置文件选项卡下的“注册密码重置”链接来手动注册其联系信息。

> [!NOTE]
> 用户可以通过单击取消或关闭窗口的控件来隐藏密码重置注册门户，但在完成注册之前，当他们每次登录时，系统都会提示他们注册。
>

### <a name="number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>用户必须在几天后重新确认其身份验证信息

此选项确定设置和重新确认身份验证信息之间的时间段，仅当已启用“要求用户在登录时注册”选项时才可用。

有效值为 0-730 天，0 表示永远不要求用户重新确认其身份验证信息

## <a name="notifications"></a>通知

### <a name="notify-users-on-password-resets"></a>重置密码时通知用户

如果此选项设置为“是”，则重置其密码的用户将收到一封电子邮件，告知已通过 SSPR 门户将其密码更改为 Azure AD 中登记的主要和备用电子邮件地址。 不会向其他任何人告知已发生此重置事件。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>当其他管理员重置其密码时通知所有管理员

如果此选项设置为“是”，则**所有管理员**的、在 Azure AD 中登记的主要电子邮件地址都会收到一封电子邮件，告知另一位管理员已使用 SSPR 更改其密码。

示例：某个环境中有四名管理员。 管理员“A”使用 SSPR 重置了其密码。 管理员 B、C 和 D 将收到一封电子邮件，告知已发生此事件。

## <a name="on-premises-integration"></a>本地集成

如果已安装、配置并启用 Azure AD Connect，则可以使用更多的选项进行本地集成。

### <a name="write-back-passwords-to-your-on-premises-directory"></a>将密码写回到本地目录

控制是否为此目录启用密码写回功能，如果启用写回，该项将指示本地写回服务的状态。 如果你想要暂时禁用密码写回而不重新配置 Azure AD Connect，此设置将很有用。

* 如果此开关设置为“是”，则将启用写回，因此联合用户和已进行密码哈希同步的用户将能够重置其密码。
* 如果此开关设置为“否”，则将禁用写回，因此联合用户和已进行密码哈希同步的用户无法重置其密码。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>允许用户在不重置密码的情况下解锁帐户

指定是否应为浏览密码重置门户的用户提供选项，让他们在不重置密码的情况下解锁本地 Active Directory 帐户。 默认情况下，Azure AD 在执行密码重置时始终会解锁帐户，此设置可让你区分这两项操作。 

* 如果设置为“是”，将提供用户重置其密码以解锁帐户的选项，或者在不重置密码的情况下解锁的选项。
* 如果设置为“否”，用户只能同时执行密码重置和帐户解锁的操作。

## <a name="network-requirements"></a>网络要求

### <a name="firewall-rules"></a>防火墙规则

[Microsoft Office URL 和 IP 地址列表](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)

对于 Azure AD Connect 1.1.443.0 和更高版本，需要对以下站点进行出站 HTTPS 访问
* passwordreset.microsoftonline.com
* servicebus.windows.net

若要进行更精细的访问，可在[此处](https://www.microsoft.com/download/details.aspx?id=41653)找到更新的 Microsoft Azure 数据中心 IP 范围列表，该列表在每个星期三更新，在下一个星期一生效。

### <a name="idle-connection-timeouts"></a>空闲连接超时

Azure AD Connect 工具定期将 ping/keepalive 发送到服务总线终结点，确保连接始终处于活动状态。 如果该工具检测到丢失的连接过多，则会自动增加发送到终结点的 ping 的频率。 最低的“ping 间隔”将降至每 60 秒 ping 1 次，但是，我们强烈建议将代理/防火墙设置为允许空闲连接保持至少 2-3 分钟。 *对于较旧的版本，我们建议让空闲连接保持 4 分钟或更长时间。

## <a name="active-directory-permissions"></a>Active Directory 权限

在 Azure AD Connect 实用工具中指定的帐户必须对该林中**或者**要纳入 SSPR 范围的用户 OU 中的**每个域**的根对象拥有“重置密码”、“更改密码”、“对‘lockoutTime’的写权限”和“对‘pwdLastSet’的写权限”扩展权限。

如果你不确定上面指的是哪个帐户，请打开 Azure Active Directory Connect 配置 UI，然后单击“查看解决方案”选项。 需要将权限添加到的帐户列在“已同步的目录”下面

设置这些权限允许每个林的 MA 服务帐户代表该林中的用户帐户管理密码。 **如果你忘记分配这些权限，即使写回看上去配置正确，用户在尝试从云中管理本地密码时也会遇到错误。**

> [!NOTE]
> 将这些权限复制到目录中的所有对象可能需要一小时或更久。
>

设置适当的权限以便执行密码写回

1. 使用具有适当域管理权限的帐户打开“Active Directory 用户和计算机”
2. 在“视图”中，确保打开“高级功能”
3. 在左侧面板中，右键单击表示域根的对象，然后选择属性
    * 单击“安全性”选项卡
    * 然后单击“高级”
4. 在“权限”选项卡中，单击“添加”
5. 选择要将权限应用到的帐户（通过 Azure AD Connect 安装程序）
6. 在“应用到”下拉框中，选择“下级用户对象”
7. 在“权限”下选中对应于以下内容的框
    * 使密码不过期
    * 重置密码
    * 更改密码
    * 写入 lockoutTime
    * 写入 pwdLastSet
8. 单击“应用”/“确定”，直到已应用并退出所有打开的对话框。

## <a name="how-does-password-reset-work-for-b2b-users"></a>B2B 用户如何使用密码重置？
所有 B2B 配置完全支持密码重置和更改。  请在下面阅读密码重置支持的三个具体的 B2B 案例。

1. **已有 Azure AD 租户的合作伙伴组织中的用户** - 如果与你合作的组织已有 Azure AD 租户，我们将**遵守该租户中已启用的任何密码重置策略**。 要使密码重置正常工作，合作伙伴组织只需确保启用 Azure AD SSPR（这不会给 O365 客户造成额外的费用）。可以遵照[密码管理入门](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)指南中的步骤启用 SSPR。
2. **已注册使用[自助注册的用户](active-directory-self-service-signup.md)** - 如果与你合作的组织使用[自助注册](active-directory-self-service-signup.md)功能来访问租户，我们将允许他们使用已注册的电子邮件来重置密码。
3. **B2B 用户** - 使用新的 [Azure AD B2B 功能](active-directory-b2b-what-is-azure-ad-b2b.md)创建的任何新 B2B 用户也可以使用他们在邀请过程中注册的电子邮件来重置其密码。

若要测试此功能，请让上述合作伙伴用户之一转到 http://passwordreset.microsoftonline.com。 只要他们定义了备用电子邮件或身份验证电子邮件，密码重置就能按预期方式工作。

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助服务密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**策略**](active-directory-passwords-policy.md) - 了解并设置 Azure AD 密码策略
* [**密码写回**](active-directory-passwords-writeback.md) - 如何对本地目录使用密码写回
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能、在何时在何处访问
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 常见问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题


