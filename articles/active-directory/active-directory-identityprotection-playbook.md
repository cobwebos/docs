---
title: "Azure Active Directory Identity Protection 演练手册 | Microsoft 文档"
description: "了解 Azure AD Identity Protection 如何使你能够限制攻击者利用已泄露标识或设备的能力，以及保护之前可疑或已知受到威胁的标识或设备。"
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
ms.date: 07/12/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 77031b3bbe2372bf2cac34bac45368ac40911641
ms.openlocfilehash: 87070d7680a6d48e94099b47f9dc336a75332f6c
ms.contentlocale: zh-cn
ms.lasthandoff: 12/29/2016

---
<a id="azure-active-directory-identity-protection-playbook" class="xliff"></a>

# Azure Active Directory Identity Protection 操作手册
此操作手册有助于：

* 通过模拟风险事件和漏洞在 Identity Protection 环境中填充数据
* 设置基于风险的条件访问策略并测试这些策略的影响

<a id="simulating-risk-events" class="xliff"></a>

## 模拟风险事件
本部分向你提供模拟以下风险事件类型的步骤：

* 从匿名 IP 地址登录（简单）
* 从不熟悉的位置登录（中等）
* 不可能前往异常位置（困难）

不能以安全方式模拟其他风险事件。

<a id="sign-ins-from-anonymous-ip-addresses" class="xliff"></a>

### 从匿名 IP 地址登录
此风险事件类型可标识已从标识为匿名代理 IP 地址的 IP 地址成功登录的用户。 这些代理由想要隐藏其设备的 IP 地址的用户使用，并且可能用于恶意目的。

**若要模拟从匿名 IP 登录，请执行以下步骤**：

1. 下载 [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)。
2. 使用 Tor 浏览器，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
3. 输入要在**从匿名 IP 地址登录**报告中显示的帐户的凭据。

登录将在 5 分钟内显示在 Identity Protection 仪表板上。 

<a id="sign-ins-from-unfamiliar-locations" class="xliff"></a>

### 从不熟悉的位置登录
不熟悉的位置风险是视为过去登录位置（IP、纬度/经度和 ASN）的实时登录评估机制，可确定新的/不熟悉的位置。 系统可存储用户以前的 IP、纬度/经度和 ASN，并将它们视为熟悉的位置。 如果登录位置与现有的任何熟悉位置不匹配，该登录位置将视为不熟悉的位置。

Azure Active Directory Identity Protection：  

* 具有 14 天的初始学习期限，在此期间不会将任何新位置标记为不熟悉的位置。
* 将忽略从熟悉设备和在地理上接近现有熟悉位置的位置登录。

若要模拟不熟悉的位置，必须从帐户之前未登录过的位置和设备登录。 

**若要模拟从不熟悉的位置登录，请执行以下步骤**：

1. 选择具有最少 14 天登录历史记录的帐户。 
2. 执行以下任一操作：
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 使用 VPN 时，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)，并输入要模拟风险事件的帐户的凭据。
   
   b. 在其他位置请求同伴使用该帐户的凭据登录（不推荐）。

登录将在 5 分钟内显示在 Identity Protection 仪表板上。

<a id="impossible-travel-to-atypical-location" class="xliff"></a>

### 不可能前往异常位置
模拟不可能前往条件很难，因为该算法使用机器学习清除从熟悉设备的不可能前往等误报，或从目录中其他用户使用的 VPN 登录。 此外，此算法要求用户 3 到 14 天的登录历史记录，然后才能开始生成风险事件。

**若要模拟不可能前往异常位置，请执行以下步骤**：

1. 使用标准浏览器，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。  
2. 输入你想要生成一个不可能前往风险事件的帐户的凭据。
3. 更改你的用户代理。 可以在 Internet Explorer 中通过开发人员工具更改用户代理，或者在 Firefox 或 Chrome 中使用用户代理切换器加载项更改用户代理。
4. 更改你的 IP 地址。 可以在不同的数据中心中通过使用 VPN、Tor 加载项或在 Azure 中启动新计算机更改 IP 地址。
5. 像往常一样使用相同凭据并在上次登录后的数分钟内登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

登录将在 2-4 小时内显示在 Identity Protection 仪表板中。<br>
由于涉及了复杂的机器学习模型，该登录有可能会失败。<br> 可能想要为多个 Azure AD 帐户复制这些步骤。

<a id="simulating-vulnerabilities" class="xliff"></a>

## 模拟漏洞
漏洞是 Azure AD 环境中可能由危险分子利用的弱点。 Azure AD Identity Protection 中当前显示了 3 种类型的漏洞，这些漏洞会利用 Azure AD 的其他功能。 设置这些功能后，这些漏洞将自动显示在 Identity Protection 仪表板上。

* Azure AD [多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)。
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)。 

<a id="user-compromise-risk" class="xliff"></a>

## 用户泄漏风险
**若要测试用户泄漏风险，请执行以下步骤**：

1. 使用你的租户的全局管理员凭据登录 [https://portal.azure.com](https://portal.azure.com)。
2. 导航到 **Identity Protection**。 
3. 在主“Azure AD Identity Protection”边栏选项卡上，单击“设置”。 
4. 在“门户设置”边栏选项卡上的“安全规则”下，单击“用户泄漏风险”。 
5. 在“登录风险”边栏选项卡上，关闭“启用规则”，然后单击“保存”设置。
6. 对于给定的用户帐户，模拟不熟悉的位置或匿名 IP 风险事件。 这会将该用户的用户风险级别提升到“中等”。
7. 等待几分钟，然后验证用户的用户级别是否为“中等”。
8. 转到“门户设置”边栏选项卡。
9. 在“用户泄漏风险”边栏选项卡上的“启用规则”下，选择“开”。 
10. 选择以下选项之一：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 若要阻止，请选择“阻止登录”下的“中等”。
    
    b. 若要强制安全密码更改，请选择“要求多重身份验证”下的“中等”。
11. 单击“保存” 。
12. 现在通过使用具有提升的风险级别的用户登录，可以测试基于风险的条件访问。 如果用户风险为中等，根据你的策略配置，登录将受阻止或强制你更改你的密码。 
    <br><br>
    ![演练手册](./media/active-directory-identityprotection-playbook/201.png "Playbook")
    <br>

<a id="sign-in-risk" class="xliff"></a>

## 登录风险
**若要测试登录风险，请执行以下步骤**：

1. 使用你的租户的全局管理员凭据登录 [https://portal.azure.com](https://portal.azure.com)。
2. 导航到 **Identity Protection**。
3. 在主“Azure AD Identity Protection”边栏选项卡上，单击“设置”。 
4. 在“门户设置”边栏选项卡上的“安全规则”下，单击“登录风险”。
5. 在“登录风险”边栏选项卡上的“启用规则”下，选择“开”。 
6. 选择以下选项之一：
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 若要阻止，请选择“阻止登录”下的“中等”。
   
   b. 若要强制安全密码更改，请选择“要求多重身份验证”下的“中等”。
7. 若要阻止，请选择“阻止登录”下的“中等”。
8. 若要强制多重身份验证，请选择“要求多重身份验证”下的“中等”。
9. 单击“保存” 。
10. 现在通过模拟不熟悉的位置或匿名 IP 风险事件，可以测试基于风险的条件访问，因为这些事件都是“中等”风险事件。


![演练手册](./media/active-directory-identityprotection-playbook/200.png "演练手册")


<a id="see-also" class="xliff"></a>

## 另请参阅
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


