---
title: "Azure Active Directory Identity Protection 演练手册 | Microsoft 文档"
description: "了解如何使用 Azure AD Identity Protection 来限制攻击者利用已泄露标识或设备的能力，以及保护之前怀疑或已知受到威胁的标识或设备。"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, 管理应用程序, 安全, 风险, 风险级别, 漏洞, 安全策略"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5479981ed4c017e6953ef616588ca0e4060ce3b2
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection 操作手册
此操作手册有助于：

* 通过模拟风险事件和漏洞在 Identity Protection 环境中填充数据
* 设置基于风险的条件访问策略并测试这些策略的影响

## <a name="simulating-risk-events"></a>模拟风险事件
本部分向你提供模拟以下风险事件类型的步骤：

* 从匿名 IP 地址登录（简单）
* 从不熟悉的位置登录（中等）
* 不可能前往异常位置（困难）

不能以安全方式模拟其他风险事件。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>从匿名 IP 地址登录
此风险事件类型可识别已从标识为匿名代理 IP 地址的 IP 地址成功登录的用户。 这些代理由想要隐藏其设备的 IP 地址的用户使用，并且可能用于恶意目的。

**若要模拟从匿名 IP 登录，请执行以下步骤**：

1. 下载 [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)。
2. 使用 Tor 浏览器，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
3. 输入要在**从匿名 IP 地址登录**报告中显示的帐户的凭据。

登录会在 5 分钟内显示在 Identity Protection 仪表板上。 

### <a name="sign-ins-from-unfamiliar-locations"></a>从不熟悉的位置登录
不熟悉的位置风险是一种实时登录评估机制，该机制借助过去的登录位置（IP、纬度/经度和 ASN）来确定新的/不熟悉的位置。 系统可存储用户以前的 IP、纬度/经度和 ASN，并将它们视为熟悉的位置。 如果登录位置与现有的任何熟悉位置不匹配，该登录位置将视为不熟悉的位置。

Azure Active Directory Identity Protection：  

* 具有 14 天的初始学习期限，在此期间不会将任何新位置标记为不熟悉的位置。
* 将忽略从熟悉设备和在地理上接近现有熟悉位置的位置登录。

若要模拟不熟悉的位置，必须从帐户之前未登录过的位置和设备登录。 

**若要模拟从不熟悉的位置登录，请执行以下步骤**：

1. 选择具有最少 14 天登录历史记录的帐户。 
2. 执行以下任一操作：
   
   a. 使用 VPN 时，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)，并输入要模拟风险事件的帐户的凭据。
   
   b. 在其他位置请求同伴使用该帐户的凭据登录（不推荐）。

登录会在 5 分钟内显示在 Identity Protection 仪表板上。

### <a name="impossible-travel-to-atypical-location"></a>不可能前往异常位置
模拟不可能前往条件很难，因为该算法使用机器学习来清除误报，如来自熟悉设备的不可能前往，或从目录中其他用户使用的 VPN 登录。 此外，此算法要求用户 3 到 14 天的登录历史记录，才能开始生成风险事件。

**若要模拟不可能前往异常位置，请执行以下步骤**：

1. 使用标准浏览器，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。  
2. 输入你想要为其生成不可能前往风险事件的帐户的凭据。
3. 更改用户代理。 可以在 Internet Explorer 中通过开发人员工具更改用户代理，或者在 Firefox 或 Chrome 中使用用户代理切换器加载项更改用户代理。
4. 更改 IP 地址。 可以在不同的数据中心中通过使用 VPN、Tor 加载项或在 Azure 中启动新计算机更改 IP 地址。
5. 像往常一样使用相同凭据并在上次登录后的数分钟内登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

登录会在 2-4 小时内显示在 Identity Protection 仪表板中。<br>
由于涉及了复杂的机器学习模型，该登录有可能会失败。<br> 可能想要为多个 Azure AD 帐户复制这些步骤。

## <a name="simulating-vulnerabilities"></a>模拟漏洞
漏洞是 Azure AD 环境中可能由危险分子利用的弱点。 Azure AD Identity Protection 中当前显示了 3 种类型的漏洞，这些漏洞会利用 Azure AD 的其他功能。 设置这些功能后，这些漏洞会自动显示在 Identity Protection 仪表板上。

* Azure AD [多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)。
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)。 

## <a name="user-compromise-risk"></a>用户泄漏风险
**若要测试用户泄漏风险，请执行以下步骤**：

1. 使用租户的全局管理员凭据登录 [https://portal.azure.com](https://portal.azure.com)。
2. 导航到 **Identity Protection**。 
3. 在主“Azure AD Identity Protection”边栏选项卡上，单击“设置”。 
4. 在“门户设置”边栏选项卡上的“安全规则”下，单击“用户泄漏风险”。 
5. 在“登录风险”边栏选项卡上，关闭“启用规则”，并单击“保存”设置。
6. 对于给定的用户帐户，模拟不熟悉的位置或匿名 IP 风险事件。 这会将该用户的用户风险级别提升到“中等”。
7. 等待几分钟，验证用户的用户级别是否为“中等”。
8. 转到“门户设置”边栏选项卡。
9. 在“用户泄漏风险”边栏选项卡上的“启用规则”下，选择“开”。 
10. 选择以下选项之一：
    
    a. 若要阻止，请选择“阻止登录”下的“中等”。
    
    b. 若要强制安全密码更改，请选择“要求多重身份验证”下的“中等”。
11. 单击“保存” 。
12. 现在通过使用具有提升的风险级别的用户登录，可以测试基于风险的条件访问。 如果用户风险为中等，根据策略配置，登录将受阻止或强制你更改密码。 
    <br><br>
    ![演练手册](./media/active-directory-identityprotection-playbook/201.png "演练手册")
    <br>

## <a name="sign-in-risk"></a>登录风险
**若要测试登录风险，请执行以下步骤**：

1. 使用租户的全局管理员凭据登录 [https://portal.azure.com](https://portal.azure.com)。
2. 导航到 **Identity Protection**。
3. 在主“Azure AD Identity Protection”边栏选项卡上，单击“设置”。 
4. 在“门户设置”边栏选项卡上的“安全规则”下，单击“登录风险”。
5. 在“登录风险”边栏选项卡上的“启用规则”下，选择“开”。 
6. 选择以下选项之一：
   
   a. 若要阻止，请选择“阻止登录”下的“中等”。
   
   b. 若要强制安全密码更改，请选择“要求多重身份验证”下的“中等”。
7. 若要阻止，请选择“阻止登录”下的“中等”。
8. 若要强制多重身份验证，请选择“要求多重身份验证”下的“中等”。
9. 单击“保存” 。
10. 现在通过模拟不熟悉的位置或匿名 IP 风险事件，可以测试基于风险的条件访问，因为这些事件都是“中等”风险事件。


![演练手册](./media/active-directory-identityprotection-playbook/200.png "演练手册")


## <a name="see-also"></a>另请参阅
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

