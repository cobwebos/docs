---
title: 条件性访问 What If 工具-Azure Active Directory
description: 了解如何了解条件访问策略对环境的影响。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeb2e98ad9bbd35f3ec507e36e958c5ce6ad2198
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185883"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>在条件访问中使用 What If 工具进行故障排除

[条件性访问](../active-directory-conditional-access-azure-portal.md)是 Azure Active Directory （Azure AD）的一种功能，可用于控制授权用户访问云应用的方式。 您如何知道您的环境中的条件性访问策略会出现什么情况？ 若要回答此问题，可以使用**条件性访问 What If 工具**。

本文介绍如何使用此工具来测试条件性访问策略。

## <a name="what-it-is"></a>作用

使用 "**条件性访问 What If 策略" 工具**，你可以了解条件访问策略对环境的影响。 通过此工具，可以评估模拟的用户登录，而不是通过手动执行多个登录来驱动策略的测试。 该模拟会估计此登录对策略的影响并生成模拟报表。 报表不仅列出应用的条件访问策略，而且还列出[经典策略](policy-migration.md#classic-policies)（如果存在）。    

**What If**工具提供了一种快速确定应用于特定用户的策略的方法。 如果需要解决问题等，则可以使用此信息。    

## <a name="how-it-works"></a>工作原理

在 "**条件访问 What If" 工具**中，首先需要配置要模拟的登录方案的设置。 这些设置包括：

- 想要测试的用户 
- 用户要尝试访问的云应用
- 访问配置的云应用时存在的条件
     
下一步，可以启动用于评估设置的模拟运行。 评估运行中仅包含启用的策略。

完成评估后，此工具将生成一份受影响策略的报表。

## <a name="running-the-tool"></a>运行此工具

可以在 Azure 门户中的 " **[条件访问-策略](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** " 页上找到**What If**工具。

若要启动该工具，请在策略列表顶部的工具栏中，单击 " **What If**"。

![What If](./media/what-if-tool/01.png)

必须先配置设置，才可以运行评估。

## <a name="settings"></a>设置

本部分介绍有关模拟运行的设置的信息。

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>用户

仅可选择一个用户。 这是唯一的必填字段。

### <a name="cloud-apps"></a>云应用

此设置的默认值为“所有云应用”。 默认设置执行对环境中所有可用策略的评估。 可以将范围缩小到影响特定云应用的策略。

### <a name="ip-address"></a>IP 地址

IP 地址为单个 IPv4 地址，用于模拟[位置条件](location-condition.md)。 地址表示用户用于登录的设备的面向 Internet 的地址。 可以通过导航到 [What is my IP address](https://whatismyipaddress.com)（我的 IP 地址是什么）等来验证设备的 IP 地址。    

### <a name="device-platforms"></a>设备平台

此设置模拟[设备平台条件](concept-conditional-access-conditions.md#device-platforms)及表示所有平台（包括不受支持的平台）的等效项。 

### <a name="client-apps"></a>客户端应用

此设置模拟[客户端应用条件](concept-conditional-access-conditions.md#client-apps-preview)。
默认情况下，此设置会导致对同时选中“浏览器”和“移动应用和桌面客户端”或其中之一的所有策略进行评估。 此外，此设置还检测强制实施“Exchange ActiveSync (EAS)”的策略。 可以通过选择以下内容缩小此设置的范围：

- 浏览器：评估至少选择了“浏览器”的所有策略。 
- 移动应用和桌面客户端：评估至少选择了“移动应用和桌面客户端”的所有策略。 

### <a name="sign-in-risk"></a>登录风险

此设置模拟[登录风险条件](concept-conditional-access-conditions.md#sign-in-risk)。   

## <a name="evaluation"></a>计算 

单击 " **What If**" 开始评估。 评估结果提供包含以下内容的报表： 

![What If](./media/what-if-tool/03.png)

- 一个指示器，指示环境中是否存在经典策略
- 应用于用户的策略
- 不应用于用户的策略

如果对于所选云应用，存在[经典策略](policy-migration.md#classic-policies)，则显示一个指示器。 通过单击该指示器，系统会重定向到经典策略页。 在经典策略页上，可以迁移经典策略或仅禁用该策略。 可以通过关闭此页返回到评估结果。

在应用于所选用户的策略列表中，还可以找到用户必须满足的[授权控件](controls.md#grant-controls)和[会话](controls.md#session-controls)控件列表。

在不应用于用户的策略列表中，还可以找到不应用这些策略的原因。 对于列出的每条策略，所列原因表示不满足的首要条件。 不应用策略的可能原因是策略被禁用，因为未经过进一步评估。   

## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md)。
- 如果已准备好针对环境配置条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](best-practices.md)。 
- 如果想要迁移经典策略，请参阅[在 Azure 门户中迁移经典策略](policy-migration.md)  
