---
title: 在 Azure AD 标识保护中模拟风险检测
description: 了解如何在身份保护中模拟风险检测
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886930"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>模拟身份保护中的风险检测

管理员可能需要模拟其环境中的风险，以便完成以下项：

- 通过模拟风险检测和漏洞来填充身份保护环境中的数据。
- 设置基于风险的条件访问策略，并测试这些策略的影响。

本文为您提供了模拟以下风险检测类型的步骤：

- 匿名 IP 地址（简单）
- 不熟悉的登录属性（中等）
- 非典型旅行（困难）

不能以安全方式模拟其他风险检测。

有关每个风险检测的详细信息，请参阅文章"[什么是风险](concept-identity-protection-risks.md)"。

## <a name="anonymous-ip-address"></a>匿名 IP 地址

完成以下过程需要使用：

- [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)，用于模拟匿名 IP 地址。 如果组织限制使用 Tor 浏览器，则可能需要使用虚拟机。
- 尚未注册 Azure 多重身份验证的测试帐户。

**若要模拟从匿名 IP 登录，请执行以下步骤**：

1. 使用 [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
2. 输入要在**从匿名 IP 地址登录**报告中显示的帐户的凭据。

登录会在 10 - 15 分钟内显示在“标识保护”仪表板上。 

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登录属性

若要模拟不熟悉的位置，必须从测试帐户之前未登录过的位置和设备登录。

下面的过程使用新创建的以下项目：

- VPN 连接，用于模拟新位置。
- 虚拟机，用于模拟新设备。

完成以下过程需要使用具有以下特征的用户帐户：

- 至少 30 天的登录历史记录。
- 启用 Azure 多重身份验证。

**若要模拟从不熟悉的位置登录，请执行以下步骤**：

1. 使用测试帐户登录时，通过未通过 MFA 质询，从而失败多重身份验证 （MFA） 质询。
2. 使用新的 VPN 导航并[https://myapps.microsoft.com](https://myapps.microsoft.com)输入测试帐户的凭据。

登录会在 10 - 15 分钟内显示在“标识保护”仪表板上。

## <a name="atypical-travel"></a>异常位置登录

模拟非典型旅行条件很困难，因为算法使用机器学习来清除误报，例如来自熟悉设备的非典型旅行，或目录中其他用户使用的 VPN 登录。 另外，此算法要求用户 14 天 10 次的登录历史记录，才能开始生成风险检测。 考虑到机器学习模型的复杂性以及上述规则的存在，以下步骤有可能不会导致风险检测。 您可能希望为多个 Azure AD 帐户复制这些步骤以模拟此检测。

**要模拟非典型旅行风险检测，执行以下步骤**：

1. 使用标准浏览器导航到[https://myapps.microsoft.com](https://myapps.microsoft.com)。  
2. 输入要生成非典型旅行风险检测的帐户的凭据。
3. 更改用户代理。 可以从开发人员工具 （F12） 更改 Microsoft Edge 中的用户代理。
4. 更改 IP 地址。 您可以通过使用 VPN、Tor 加载项或在 Azure 中在不同的数据中心创建新虚拟机来更改 IP 地址。
5. 登录以[https://myapps.microsoft.com](https://myapps.microsoft.com)使用与前一次登录后相同的凭据，并在前一次登录后几分钟内登录。

登录会在 2-4 小时内显示在“标识保护”仪表板中。

## <a name="testing-risk-policies"></a>测试风险策略

本节为您提供了测试用户的步骤以及文章"[如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)"中创建的登录风险策略。

### <a name="user-risk-policy"></a>用户风险策略

若要测试用户风险安全策略，请执行以下步骤：

1. 导航到[Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure 活动目录** > **安全** > **概述**。
1. 选择 **"配置用户风险策略**"。
   1. 在**分配**下
      1. **用户**- 如果限制推出，请选择**所有用户**或**选择个人和组**。
         1. 可以选择将用户从策略中排除。
      1. **条件** - **用户风险**微软的建议是将此选项设置为**高**。
   1. 在**控制下**
      1. **访问**- 微软的建议是**允许访问**和**要求密码更改**。
   1. **强制关闭策略** - **Off**
   1. **保存**- 此操作将返回到 **"概述"** 页。
1. 可以通过特定的方式来提升测试帐户的用户风险，例如，可以多次模拟某个风险检测。
1. 等待几分钟，然后验证用户的风险是否升高。 如果否，请为用户模拟更多的风险检测。
1. 返回到风险策略并将 **"强制策略**"设置为 **"打开**"并**保存**策略更改。
1. 现在可以使用风险级别已提升的用户登录，以便测试基于用户风险的条件访问。

### <a name="sign-in-risk-security-policy"></a>登录风险安全策略

若要测试登录风险策略，请执行以下步骤：

1. 导航到[Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure 活动目录** > **安全** > **概述**。
1. 选择**配置登录风险策略**。
   1. 在**分配**下
      1. **用户**- 如果限制推出，请选择**所有用户**或**选择个人和组**。
         1. 可以选择将用户从策略中排除。
      1. **条件** - **登录风险**微软的建议是将此选项设置为**中和以上**。
   1. 在**控制下**
      1. **访问**- 微软的建议是**允许访问**和**要求多重身份验证**。
   1. **执行策略** - **On**
   1. **保存**- 此操作将返回到 **"概述"** 页。
1. 现在可以使用风险会话（例如，使用 Tor 浏览器）登录，以便测试基于登录风险的条件访问。 

## <a name="next-steps"></a>后续步骤

- [什么是风险？](concept-identity-protection-risks.md)

- [如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory 标识保护](overview-identity-protection.md)
