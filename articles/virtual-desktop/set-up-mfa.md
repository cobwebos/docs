---
title: 为 Windows 虚拟桌面设置 Azure 多重身份验证-Azure
description: 如何设置 Azure 多重身份验证以提高 Windows 虚拟桌面的安全性。
author: Heidilohr
ms.topic: how-to
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e8e723aa26ab08c8a09e75f506802101dc07f7e8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017765"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>为 Windows 虚拟桌面启用 Azure 多重身份验证

>[!IMPORTANT]
> 如果要从 Windows 虚拟桌面 (经典) 文档访问此页面，请确保在完成后 [返回到 Windows 虚拟桌面 (经典) 文档](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) 。

适用于 windows 虚拟桌面的 Windows 客户端是将 Windows 虚拟桌面与本地计算机集成的最佳选项。 但是，当你将 Windows 虚拟桌面帐户配置到 Windows 客户端时，你需要采取某些措施来保护你的用户和用户安全。

首次登录时，客户端会要求提供用户名、密码和 Azure MFA。 此后，下次登录时，客户端将记住 Azure Active Directory (AD) 企业应用程序的令牌。 当你选择 " **记住我**" 时，用户可以在重新启动客户端后登录，而不需要重新输入其凭据。

尽管记住凭据很方便，但它也可以使部署在企业方案或个人设备上不太安全。 若要保护用户，你需要确保客户端继续请求 Azure 多重身份验证 (MFA) 凭据。 本文将演示如何配置 Windows 虚拟桌面的条件性访问策略，以便启用此设置。

## <a name="prerequisites"></a>先决条件

下面是你需要入门的内容：

- 为用户分配包含 Azure Active Directory Premium P1 或 P2 的许可证。
- 用户分配为组成员的 Azure Active Directory 组。
- 为所有用户启用 Azure MFA。 有关如何执行此操作的详细信息，请参阅 [如何要求用户进行双重验证](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)。

> [!NOTE]
> 以下设置也适用于 [Windows 虚拟桌面 web 客户端](https://rdweb.wvd.microsoft.com/webclient/index.html)。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

下面介绍如何创建在连接到 Windows 虚拟桌面时需要多重身份验证的条件性访问策略：

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
2. 浏览到“Azure Active Directory” > “安全性” > “条件访问”  。
3. 选择“新策略”。
4. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
5. 在“分配”  下，选择“用户和组”  。
6. 在 "**包括**" 下，选择 "**选择用户和组**  >  **用户和组**" > 选择你在 "[先决条件](#prerequisites)" 阶段创建的组。
7. 选择“完成”  。
8. 在 "**云应用或操作**  >  **包括**" 下，选择 "**选择应用**"。
9. 根据所使用的 Windows 虚拟桌面版本选择以下应用之一。
   - 如果使用的是 Windows 虚拟桌面 (经典) ，请选择此应用：
       - **Windows 虚拟桌面** (应用 ID 5a0aa725-4958-4b0c-80a9-34562e23f3b7) 
   - 如果使用的是 Windows 虚拟桌面，请改为选择此应用：
       -  **Windows 虚拟桌面** (应用 ID 9cdead84-a844-4324-93f2-b2e6bb768d07) 

   >[!IMPORTANT]
   > 请勿选择名为 "Windows 虚拟桌面 Azure 资源管理器提供程序" 的应用 (50e95039-b200-4007-bc97-8d5790743a63) 。 此应用仅用于检索用户源，而不应具有 MFA。

10. 前往 "**条件**  >  " "**客户端应用**"，然后选择要将策略应用到的位置：
    
    - 如果要将策略应用到 web 客户端，请选择 " **浏览器** "。
    - 如果要将策略应用到其他客户端，请选择 " **移动应用和桌面客户端** "。
    - 如果要将策略应用于所有客户端，请选中这两个复选框。
   
    > [!div class="mx-imgBorder"]
    > ![客户端应用页的屏幕截图。 用户已选中 "移动应用和桌面客户端" 复选框。](media/select-apply.png)

11. 选择应用后，选择 " **选择**"，然后选择 " **完成**"。

    > [!div class="mx-imgBorder"]
    > ![云应用或操作页的屏幕截图。 Windows 虚拟桌面和 Windows 虚拟桌面客户端应用以红色突出显示。](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >若要查找要选择的应用的应用 ID，请在 "应用程序类型" 下拉菜单中，单击 " **企业应用程序** "，然后选择 " **Microsoft 应用程序** "。

12. 在 "**访问控制**" "  >  **授权**" 下，选择 "**授予访问权限**，**需要多重身份验证**"，然后**选择**。
13. 在 **"访问控制**"  >  **会话**下，选择 "**登录频率**"，将 "值" 设置为**1** ，将 "单位" 设置为 "**小时**"，然后选择 "**选择**"。
14. 确认设置，然后将“启用策略”设置为“打开”。 
15. 选择 " **创建** " 以启用策略。

## <a name="next-steps"></a>后续步骤

- [了解有关条件性访问策略的详细信息](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [了解有关用户登录频率的详细信息](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
