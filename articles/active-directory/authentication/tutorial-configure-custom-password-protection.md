---
title: 配置自定义 Azure Active Directory 密码保护列表
description: 本教程介绍如何为 Azure Active Directory 配置自定义的受禁密码保护列表，以限制环境中的常见字词。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abb15462689470c87e9cf5ba8d5be8af2e45bfd
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253117"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>教程：为 Azure Active Directory 密码保护配置自定义受禁密码

用户经常创建包含本地常见字词（例如学校、运动团队或名人）的密码。 这些密码很容易猜出，对基于字典的攻击的抵御能力很弱。 若要在组织中实施强密码，可以通过 Azure Active Directory (Azure AD) 自定义受禁密码列表来添加要评估和阻止的特定字符串。 如果与自定义受禁密码列表中的某个密码匹配，则密码更改请求将会失败。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 启用自定义受禁密码
> * 在自定义受禁密码列表中添加条目
> * 使用受禁密码测试密码更改

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个至少启用了试用版许可证的有效 Azure AD 租户。
    * 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个拥有全局管理员特权的帐户。 
* 你知道其密码的非管理员测试用户，例如 *testuser*。 在本教程中，你将使用此帐户测试密码更改事件。
    * 如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../add-users-azure-active-directory.md)。
    * 若要使用受禁密码测试密码更改操作，必须为 Azure AD 租户[配置自助式密码重置](tutorial-enable-sspr.md)。

## <a name="what-are-banned-password-lists"></a>什么是受禁密码列表？

Azure AD 包含全局受禁密码列表。 全局受禁密码列表的内容不基于任何外部数据源。 全局受禁密码列表基于不断更新的 Azure AD 安全遥测和分析结果。 当用户或管理员尝试更改或重置其凭据时，会根据受禁密码列表检查所需的密码。 如果与全局受禁密码列表中的某个密码匹配，则密码更改请求将会失败。

为了灵活控制允许的密码，还可以自定义受禁密码列表。 自定义受禁密码列表与全局受禁密码列表一同应用，可在组织中实施强密码。 可将组织特定的字词添加到自定义受禁密码列表，如以下示例所示：

* 品牌名称
* 产品名称
* 地点，例如公司总部
* 公司特定的内部字词
* 具有特定公司含义的缩写

当用户尝试将某个密码重置为全局或自定义受禁密码列表中的密码时，会看到以下错误消息之一：

* “很遗憾，密码中包含的单词、短语或模式使密码很容易被猜出。  请使用其他密码重试。”
* “很遗憾，不能使用该密码，因为它包含管理员已禁止的字词或字符。  请使用其他密码重试。”

自定义受禁密码列表限制为最多 1000 个字词。 它并非用于阻止包含大量密码的列表。 若要最大程度地发挥自定义受禁密码列表的优势，请查看[自定义受禁密码列表的概念](concept-password-ban-bad.md#custom-banned-password-list)和[密码评估算法概述](concept-password-ban-bad.md#how-are-passwords-evaluated)。

## <a name="configure-custom-banned-passwords"></a>配置自定义受禁密码

让我们启用自定义受禁密码列表并添加一些条目。 随时可以将更多条目添加到自定义受禁密码列表。

若要启用自定义受禁密码列表并在其中添加条目，请完成以下步骤：

1. 使用拥有全局管理员权限的帐户登录到 [Azure 门户](https://portal.azure.com)。 
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“安全性”。  
1. 在“管理”菜单标题下，依次选择“身份验证方法”、“密码保护”。   
1. 将“强制实施自定义列表”选项设置为“是”。  
1. 将字符串添加到“自定义受禁密码列表”，每行添加一个字符串。  以下注意事项和限制适用于自定义受禁密码列表：

    * 自定义受禁密码列表最多可以包含 1000 个字词。
    * 自定义受禁密码列表不区分大小写。
    * 自定义受禁密码列表考虑常见的似形字符，例如“o”和“0”，或者“a”和“@”。
    * 最小字符串长度为 4 个字符，最大字符串长度为 16 个字符。

    按以下示例所示指定自己的自定义受禁密码

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. 将“对 Windows Server Active Directory 启用密码保护”选项保留设置为“否”。  
1. 若要启用自定义受禁密码和条目，请选择“保存”。 

自定义受禁密码列表更新可能需要几个小时才能应用。

对于混合环境，还可以[将 Azure AD 密码保护部署到本地环境](howto-password-ban-bad-on-premises-deploy.md)。 相同的全局和自定义受禁密码列表将同时用于云和本地密码更改请求。

## <a name="test-custom-banned-password-list"></a>测试自定义受禁密码列表

若要查看自定义受禁密码列表的运作方式，请尝试将密码更改为在上一部分添加的某个受禁密码。 当 Azure AD 尝试处理密码更改时，该密码将与自定义受禁密码列表中的某个条目相匹配。 然后向用户显示错误。

> [!NOTE]
> 必须先为 Azure AD 租户[配置自助式密码重置](tutorial-enable-sspr.md)，用户才能在基于 Web 的门户中重置其密码。

1. 在 [https://myapps.microsoft.com](https://myapps.microsoft.com) 上转到“我的应用”页。 
1. 在右上角选择自己的姓名，然后从下拉菜单中选择“个人资料”。 

    ![选择配置文件](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. 在“个人资料”页上，选择“更改密码”。  
1. 在“更改密码”页上，输入现有的（旧）密码。  输入并确认包含在上一部分定义的自定义受禁密码列表中的新密码，然后选择“提交”。 
1. 此时将返回一条错误消息，指出管理员已禁止该密码，如以下示例所示：

    ![尝试使用包含在自定义受禁密码列表中的密码时显示的错误消息](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>清理资源

如果你不再想要使用本教程中配置的自定义受禁密码列表，请完成以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“安全性”。  
1. 在“管理”菜单标题下，依次选择“身份验证方法”、“密码保护”。   
1. 将“强制实施自定义列表”选项设置为“否”。  
1. 若要更新自定义受禁密码配置，请选择“保存”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你为 Azure AD 启用并配置了自定义密码保护列表。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 启用自定义受禁密码
> * 在自定义受禁密码列表中添加条目
> * 使用受禁密码测试密码更改

> [!div class="nextstepaction"]
> [启用基于风险的 Azure 多重身份验证](tutorial-mfa-applications.md)
