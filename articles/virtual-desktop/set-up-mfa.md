---
title: 为 Windows 虚拟桌面设置 Azure 多重身份验证 - Azure
description: 如何设置 Azure 多重身份验证，提高 Windows 虚拟桌面的安全性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998479"
---
# <a name="set-up-azure-multi-factor-authentication"></a>设置 Azure 多重身份验证

Windows 虚拟桌面的 Windows 客户端是将 Windows 虚拟桌面与本地计算机集成的绝佳选项。 但是，当您将 Windows 虚拟桌面帐户配置为 Windows 客户端时，您需要采取某些措施来保护自己和用户的安全。

首次登录时，客户端会要求您提供用户名、密码和 Azure MFA。 之后，下次登录时，客户端将从 Azure 活动目录 （AD） 企业应用程序中记住令牌。 当您选择 **"记住我"** 时，您的用户可以在重新启动客户端后登录，而无需重新输入其凭据。

虽然记住凭据很方便，但它也会使企业方案或个人设备上的部署的安全性降低。 为了保护用户，您需要确保客户端不断要求 Azure 多重身份验证 （MFA） 凭据。 本文将介绍如何为 Windows 虚拟桌面配置条件访问策略以启用此设置。

## <a name="prerequisites"></a>先决条件

以下是入门所需的内容：

- 为所有用户分配以下许可证之一：
  - 微软 365 E3 或 E5
  - Azure 活动目录高级 P1 或 P2
  - 企业移动性 + 安全 E3 或 E5
- 将用户分配为组成员的 Azure 活动目录组。
- 为所有用户启用 Azure MFA。 有关如何执行此操作的详细信息，请参阅[如何要求用户进行两步验证](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)。

>[!NOTE]
>以下设置也适用于[Windows 虚拟桌面 Web 客户端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

## <a name="opt-in-to-the-conditional-access-policy"></a>选择加入条件访问策略

1. 打开“Azure Active Directory”。****

2. 转到"**所有应用程序**"选项卡。在"应用程序类型"下拉菜单中，选择**企业应用程序**，然后搜索**Windows 虚拟桌面客户端**。

    !["所有应用程序"选项卡的屏幕截图。用户将"窗口虚拟桌面客户端"输入到搜索栏中，并且应用已显示在搜索结果中。](media/all-applications-search.png)

3. 选择**条件访问**。

    ![显示用户将鼠标光标悬停在"条件访问"选项卡上的屏幕截图。](media/conditional-access-location.png)

4. 选择 **= 新策略**。

   ![条件访问页面的屏幕截图。 用户将鼠标光标悬停在新策略按钮上。](media/new-policy-button.png)

5. 输入规则**的名称**，然后**rule****选择**在先决条件中创建的**组的**的 @name。

6. **选择 "选择**"，然后选择 **"完成**"。

7. 接下来，打开**云应用或操作**。

8. 在 **"选择**"面板上，选择**Windows 虚拟桌面**企业应用。

    ![云应用或操作页面的屏幕截图。 用户已选择 Windows 虚拟桌面应用，选择其旁边的复选标记。 所选应用以红色突出显示。](media/cloud-apps-select.png)
    
    >[!NOTE]
    >您还应在屏幕左侧看到选择的 Windows 虚拟桌面客户端应用，如下图所示。 您需要 Windows 虚拟桌面和 Windows 虚拟桌面客户端企业应用才能使策略正常工作。
    >
    > ![云应用或操作页面的屏幕截图。 Windows 虚拟桌面和 Windows 虚拟桌面客户端应用以红色突出显示。](media/cloud-apps-enterprise-selected.png)

9. **选择**

10. 接下来，打开**授予** 

11. 选择 **"需要多重身份验证**"，然后选择 **"需要其中一个选定的控件**"。
   
    ![授予页面的屏幕截图。 选择"需要多重身份验证"。](media/grant-page.png)

    >[!NOTE]
    >如果您的组织中有 MDM 注册的设备，并且不希望它们显示 MFA 提示符，也可以选择 **"要求设备标记为符合"。**

12. 选择**会话**。

13. 将**登录频率**设置为 **"活动**"，然后将其值更改为**1 小时**。

    ![会话页的屏幕截图。 会话菜单显示登录频率下拉菜单已更改为"1"和"小时"。](media/sign-in-frequency.png)
   
    >[!NOTE]
    >当您更改策略时，Windows 虚拟桌面环境中的活动会话将继续工作。 但是，如果断开连接或注销，则需要在 60 分钟后再次提供凭据。 更改设置时，可以根据需要延长超时期限（只要它与组织的安全策略保持一致）。
    >
    >默认设置为 90 天的滚动窗口，这意味着客户端将请求用户在计算机上处于 90 天或更长时间的非活动状态后尝试访问资源时再次登录。

14. 启用策略。

15. 选择 **"创建**"以确认策略。

本教程到此结束！ 请随意测试策略，以确保允许列表按预期工作。
