---
title: Azure Active Directory 条件访问中的模拟工具是什么？ - 预览版 | Microsoft Docs
description: 了解如何理解条件访问策略对环境的影响。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 661ada8de8821d489732e1f36dc2406eaa0ee4a7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231849"
---
# <a name="what-is-the-what-if-tool-in-azure-active-directory-conditional-access---preview"></a>Azure Active Directory 条件访问中的模拟工具是什么？ - 预览版

[条件访问](active-directory-conditional-access-azure-portal.md)是 Azure Active Directory (Azure AD) 中的一项功能，用于控制已获授权的用户如何访问云应用。 如何知道环境中的条件访问策略的构成成分？ 若要回答此问题，可以使用“条件访问模拟工具”。

本文介绍如何使用此工具来测试条件访问策略。

## <a name="what-it-is"></a>作用

通过“条件访问模拟策略工具”，可了解条件访问策略对环境的影响。 通过此工具，可以评估模拟的用户登录，而不是通过手动执行多个登录来驱动策略的测试。 该模拟会估计此登录对策略的影响并生成模拟报表。 报表不仅列出应用的条件访问策略，还列出[经典策略](active-directory-conditional-access-migration.md#classic-policies)（如果存在）。    

此外，模拟工具还提供一种方法，用于快速确定应用于特定用户的策略。 如果需要解决问题等，则可以使用此信息。  

## <a name="how-it-works"></a>工作原理

在“条件访问模拟工具”中，首先需要配置想要模拟的登录方案的设置。 这些设置包括：

- 想要测试的用户 

- 用户要尝试访问的云应用

- 访问配置的云应用时存在的条件
     
下一步，可以启动用于评估设置的模拟运行。 评估运行中仅包含启用的策略。


完成评估后，此工具将生成一份受影响策略的报表。


## <a name="running-the-tool"></a>运行此工具

可以在 Azure 门户中的“[条件访问 - 策略](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)”页上找到“模拟”工具。

若要启动此工具，在策略列表上方的工具栏中单击“模拟”。

![模拟](./media/active-directory-conditional-access-whatif/01.png)

必须先配置设置，才可以运行评估。

## <a name="settings"></a>设置

本部分介绍有关模拟运行的设置的信息。

![模拟](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>用户

仅可选择一个用户。 这是唯一的必填字段。

### <a name="cloud-apps"></a>云应用

此设置的默认值为“所有云应用”。 默认设置执行对环境中所有可用策略的评估。 可以将范围缩小到影响特定云应用的策略。


### <a name="ip-address"></a>IP 地址

IP 地址为单个 IPv4 地址，用于模拟[位置条件](active-directory-conditional-access-locations.md)。 地址表示用户用于登录的设备的面向 Internet 的地址。 可以通过导航到 [What is my IP address](https://whatismyipaddress.com)（我的 IP 地址是什么）等来验证设备的 IP 地址。    

### <a name="device-platforms"></a>设备平台

此设置模拟[设备平台条件](active-directory-conditional-access-conditions.md#device-platforms)及表示所有平台（包括不受支持的平台）的等效项。 
### <a name="client-apps"></a>客户端应用

此设置模拟[客户端应用条件](active-directory-conditional-access-conditions.md#client-apps)。
默认情况下，此设置会导致对同时选中“浏览器”和“移动应用和桌面客户端”或其中之一的所有策略进行评估。 此外，此设置还检测强制实施“Exchange ActiveSync (EAS)”的策略。 可以通过选择以下内容缩小此设置的范围：

- 浏览器：评估至少选择了“浏览器”的所有策略。 

- 移动应用和桌面客户端：评估至少选择了“移动应用和桌面客户端”的所有策略。 


### <a name="sign-in-risk"></a>登录风险

此设置模拟[登录风险条件](active-directory-conditional-access-conditions.md#sign-in-risk)。   


## <a name="evaluation"></a>计算 

通过单击“模拟”启动评估。 评估结果提供包含以下内容的报表： 

![模拟](./media/active-directory-conditional-access-whatif/03.png)

- 一个指示器，指示环境中是否存在经典策略
- 应用于用户的策略
- 不应用于用户的策略


如果对于所选云应用，存在[经典策略](active-directory-conditional-access-migration.md#classic-policies)，则显示一个指示器。 通过单击该指示器，系统会重定向到经典策略页。 在经典策略页上，可以迁移经典策略或仅禁用该策略。 可以通过关闭此页返回到评估结果。

在应用于所选用户的策略列表中，还可以找到用户必须满足的[授权控件](active-directory-conditional-access-controls.md#grant-controls)和[会话](active-directory-conditional-access-controls.md#session-controls)控件列表。

在不应用于用户的策略列表中，还可以找到不应用这些策略的原因。 对于列出的每条策略，所列原因表示不满足的首要条件。 不应用策略的可能原因是策略被禁用，因为未经过进一步评估。   



## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](active-directory-conditional-access-app-based-mfa.md)。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 

- 如果想要迁移经典策略，请参阅[在 Azure 门户中迁移经典策略](active-directory-conditional-access-migration.md)  
