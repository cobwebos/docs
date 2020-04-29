---
title: 为 Windows 虚拟桌面设置 Azure 多重身份验证-Azure
description: 如何设置 Azure 多重身份验证以提高 Windows 虚拟桌面的安全性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998479"
---
# <a name="set-up-azure-multi-factor-authentication"></a>设置 Azure 多重身份验证

适用于 windows 虚拟桌面的 Windows 客户端是将 Windows 虚拟桌面与本地计算机集成的最佳选项。 但是，当你将 Windows 虚拟桌面帐户配置到 Windows 客户端时，你需要采取某些措施来保护你的用户和用户安全。

首次登录时，客户端会要求提供用户名、密码和 Azure MFA。 此后，下次登录时，客户端将记住 Azure Active Directory （AD）企业应用程序的令牌。 当你选择 "**记住我**" 时，用户可以在重新启动客户端后登录，而不需要重新输入其凭据。

尽管记住凭据很方便，但它也可以使部署在企业方案或个人设备上不太安全。 若要保护用户，你需要确保客户端始终询问 Azure 多重身份验证（MFA）凭据。 本文将演示如何配置 Windows 虚拟桌面的条件性访问策略，以便启用此设置。

## <a name="prerequisites"></a>必备条件

下面是你需要入门的内容：

- 向所有用户分配以下许可证之一：
  - Microsoft 365 E3 或 E5
  - Azure Active Directory Premium P1 或 P2
  - 企业移动性 + 安全性 E3 或 E5
- 用户分配为组成员的 Azure Active Directory 组。
- 为所有用户启用 Azure MFA。 有关如何执行此操作的详细信息，请参阅[如何要求用户进行双重验证](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)。

>[!NOTE]
>以下设置也适用于[Windows 虚拟桌面 web 客户端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

## <a name="opt-in-to-the-conditional-access-policy"></a>选择条件性访问策略

1. 打开“Azure Active Directory”。****

2. 中转到 "**所有应用程序**" 选项卡。在 "应用程序类型" 下拉菜单中，选择 "**企业应用程序**"，然后搜索 " **Windows 虚拟桌面客户端**"。

    !["所有应用程序" 选项卡的屏幕截图。用户已在搜索栏中输入 "windows 虚拟桌面客户端"，应用已显示在搜索结果中。](media/all-applications-search.png)

3. 选择 "**条件访问**"。

    ![一个屏幕截图，显示用户将鼠标光标悬停在 "条件访问" 选项卡上。](media/conditional-access-location.png)

4. 选择 " **+ 新建策略**"。

   !["条件访问" 页的屏幕截图。 用户将鼠标光标悬停在 "新建策略" 按钮上。](media/new-policy-button.png)

5. 输入**规则**的**名称**，然后**选择**"先决条件" 中创建的**组**的名称。

6. 选择 "**选择**"，然后选择 "**完成**"。

7. 接下来，打开 "**云应用或操作**"。

8. 在**选择**面板上，选择**Windows 虚拟桌面**企业应用。

    ![云应用或操作页的屏幕截图。 用户通过选择 Windows 虚拟桌面应用旁边的复选标记进行了选择。 选定的应用以红色突出显示。](media/cloud-apps-select.png)
    
    >[!NOTE]
    >还应看到在屏幕左侧选择的 "Windows 虚拟桌面客户端应用"，如下图所示。 需要使用 Windows 虚拟桌面和 Windows 虚拟桌面客户端企业应用程序才能使用该策略。
    >
    > ![云应用或操作页的屏幕截图。 Windows 虚拟桌面和 Windows 虚拟桌面客户端应用以红色突出显示。](media/cloud-apps-enterprise-selected.png)

9. 选择 "**选择**"

10. 接下来，打开**Grant** 

11. 选择 "**需要多重身份验证**"，然后选择 **"需要一个选定的控件"**。
   
    ![授权页的屏幕截图。 已选择 "需要多重身份验证"。](media/grant-page.png)

    >[!NOTE]
    >如果组织中已注册 MDM 的设备，并且不希望它们显示 MFA 提示符，还可以选择 "**要求设备标记为符合**"。

12. 选择 "**会话**"。

13. 将 "**登录频率**" 设置为 "**活动**"，然后将其值更改为 " **1 小时**"。

    ![会话页的屏幕截图。 "会话" 菜单显示 "登录频率" 下拉菜单已更改为 "1" 和 "小时"。](media/sign-in-frequency.png)
   
    >[!NOTE]
    >当你更改策略时，Windows 虚拟桌面环境中的活动会话将继续工作。 但是，如果断开连接或注销，则需要在60分钟后再次提供凭据。 更改设置时，你可以根据需要扩展超时期限（前提是它与组织的安全策略一致）。
    >
    >默认设置为90天的滚动窗口，这意味着客户端将要求用户在其计算机上的非活动状态为90天或更长时间后尝试访问该资源时再次登录。

14. 启用策略。

15. 选择 "**创建**" 以确认策略。

本教程到此结束！ 随意测试该策略，以确保允许列表按预期方式工作。
