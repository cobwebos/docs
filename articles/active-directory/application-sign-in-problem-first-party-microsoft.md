---
title: 登录 Microsoft 应用程序时出现的问题 | Microsoft Docs
description: 对使用 Azure AD 登录到第一方 Microsoft 应用程序（如 Office 365）时面临的常见问题进行故障排除
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 1dc727f46785d2896544d8ef9098259f9ab994d1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29384204"
---
## <a name="problems-signing-in-to-a-microsoft-application"></a>登录 Microsoft 应用程序时出现的问题

Microsoft 应用程序（如 Office 365 Exchange、SharePoint、Yammer 等）的分配和管理与集成到 Azure AD 以进行单一登录的第三方 SaaS 应用程序或其他应用程序略有不同。

用户访问 Microsoft 发布的应用程序的方法主要有三种。

-   对于 Office 365 或其他付费套件中的应用程序，可以通过**许可证分配**直接向用户的用户帐户授予访问权限，也可以使用基于组的许可证分配功能通过组授予用户访问权限。

-   对于 Microsoft 或第三方发布的可供任何人免费使用的应用程序，可以通过**用户许可**授予用户访问权限。 这意味着他们使用其 Azure AD 工作或学校帐户登录到应用程序，并允许它访问其帐户上一些受限制的数据集。

-   对于 Microsoft 或第三方发布的可供任何人免费使用的应用程序，还可以通过**管理员许可**授予用户访问权限。 这意味着管理员已确定组织中的所有人都可以使用此应用程序，因此他们使用全局管理员帐户可以登录到应用程序并向组织中所有人授予访问权限。

要解决问题，请从[对于应用程序访问要考虑的常规问题区域](#general-problem-areas-with-application-access-to-consider)开始，并阅读[演练：对 Microsoft 应用程序访问进行故障排除的步骤](#walkthrough-steps-to-troubleshoot-microsoft-application-access)了解详细信息。

## <a name="general-problem-areas-with-application-access-to-consider"></a>对于应用程序访问要考虑的常规问题区域

如果知道从何处着手，以下列表提供了需要深入了解的常规问题区域，但我们建议阅读以下演练以便快速开始操作：[演练：对 Microsoft 应用程序访问进行故障排除的步骤](#walkthrough-steps-to-troubleshoot-microsoft-application-access)。

-   [用户帐户问题](#problems-with-the-users-account)

-   [组问题](#problems-with-groups)

-   [条件访问策略问题](#problems-with-conditional-access-policies)

-   [应用程序许可问题](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>对 Microsoft 应用程序访问进行故障排除的步骤

下面是当用户无法登录到 Microsoft 应用程序时遇到的一些常见问题。

-   首先要检查的常规问题

  * 确保用户尝试登录到**正确的 URL**，而不是本地应用程序 URL。

  * 确保用户帐户**未锁定**。

  * 请确保在 Azure Active Directory 中**用户的帐户存在**。 [检查在 Azure Active Directory 中是否存在用户帐户](#problems-with-the-users-account)

  * 确保用户帐户**已启用**进行登录。[检查用户的帐户状态](#problems-with-the-users-account)

  * 确保用户的**密码未过期或遗忘**。 [重置用户的密码](#reset-a-users-password)或[启用自助服务密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

   * 确保**多重身份验证**不会阻止用户访问。 [检查用户的多重身份验证状态](#check-a-users-multi-factor-authentication-status)或[检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)

   * 确保**条件访问策略**或 **Identity Protection** 策略不会阻止用户访问。 [检查特定的条件访问策略](#problems-with-conditional-access-policies)或[检查特定应用程序的条件访问策略](#check-a-specific-applications-conditional-access-policy)或者[禁用特定的条件访问策略](#disable-a-specific-conditional-access-policy)

   * 确保用户的**身份验证联系信息**是最新信息以允许强制执行多重身份验证或条件访问策略。 [检查用户的多重身份验证状态](#check-a-users-multi-factor-authentication-status)或[检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)

-   对于**需要许可证的 Microsoft** **应用程序**（如 Office365），以下是排除上述常规问题后需要检查的一些特定问题：

   * 确保已为用户**分配许可证**。 [检查用户的已分配许可证](#check-a-users-assigned-licenses)或[检查组的已分配许可证](#check-a-groups-assigned-licenses)

   * 如果许可证**分配到****静态组**，请确保**用户是该组的成员**。 [检查用户的组成员身份](#check-a-users-group-memberships)

   * 如果许可证**分配到****动态组**，请确保**动态组规则设置正确**。 [检查动态组的成员身份条件](#check-a-dynamic-groups-membership-criteria)

   * 如果许可证**已分配到****动态组**，请确保动态组的成员身份已**处理完毕**，并确保**用户是该组的成员**（这可能需要一些时间）。 [检查用户的组成员身份](#check-a-users-group-memberships)

   *  一旦确保已分配许可证，请确保许可证**未过期**。

   *  确保许可证与他们访问的**应用程序相对应**。

-   对于**不需要许可证的 Microsoft** **应用程序**，以下是一些需要检查的其他事项：

   * 如果应用程序请求**用户级权限**（例如“访问此用户的邮箱”），请确保用户已登录到应用程序，并且已执行**用户级许可操作**，从而允许应用程序访问其数据。

   * 如果应用程序请求**管理员级权限**（例如“访问所有用户的邮箱”），请确保全局管理员已**代表组织中所有用户执行管理员级许可操作**。

## <a name="problems-with-the-users-account"></a>用户帐户问题

由于已分配到应用程序的用户出现问题，应用程序访问可能会遭到阻止。 下面是可以用来排除和解决用户及其帐户设置存在的问题的一些方法：

-   [检查在 Azure Active Directory 中是否存在用户帐户](#check-if-a-user-account-exists-in-azure-active-directory)

-   [检查用户帐户的状态](#check-a-users-account-status)

-   [重置用户的密码](#reset-a-users-password)

-   [启用自助服务密码重置](#enable-self-service-password-reset)

-   [检查用户的多重身份验证状态](#check-a-users-multi-factor-authentication-status)

-   [检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)

-   [检查用户的组成员身份](#check-a-users-group-memberships)

-   [检查用户的已分配许可证](#check-a-users-assigned-licenses)

-   [为用户分配许可证](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>检查在 Azure Active Directory 中是否存在用户帐户

若要检查是否存在某个用户帐户，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  检查用户对象的属性，确保它们看上去与预期一致并且未丢失任何数据。

### <a name="check-a-users-account-status"></a>检查用户帐户的状态

若要检查用户帐户的状态，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“配置文件”。

8.  在“设置”下，确保“阻止登录”设置为“否”。

### <a name="reset-a-users-password"></a>重置用户的密码

若要重置用户的密码，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击用户窗格顶部的“重置密码”按钮。

8.  在出现的“重置密码”窗格上，单击“重置密码”按钮。

9.  为用户复制**临时密码**或**输入新密码**。

10. 告知用户此新密码，在他们下一次登录到 Azure Active Directory 时，需要更改此密码。

### <a name="enable-self-service-password-reset"></a>启用自助服务密码重置

若要启用自助服务密码重置，请执行以下部署步骤：

-   [使用户能够重置其 Azure Active Directory 密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [使用户能够重置或更改其 Active Directory 本地密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>检查用户的多重身份验证状态

若要检查用户的多重身份验证状态，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  单击窗格顶部的“多重身份验证”按钮。

7.  在**多重身份验证管理门户**加载后，确保位于“用户”选项卡上。

8.  通过搜索、筛选或排序在用户列表中找到用户。

9.  从用户列表中选择用户，根据需要**启用**、**禁用**或**强制执行**多重身份验证。

  * **注意**：如果用户处于**已强制执行**状态，可暂时将其设置为**已禁用**以允许用户重新登录到其帐户。 一旦他们重新登录到帐户，便可以再次将其状态更改为**已启用**来要求他们在下次登录期间重新注册联系信息。 此外，也可以按照[检查用户的身份验证联系信息](#check-a-users-authentication-contact-info)中的步骤为其验证或设置此数据。

### <a name="check-a-users-authentication-contact-info"></a>检查用户的身份验证联系信息

若要检查用于多重身份验证、条件访问、Identity Protection 和密码重置的用户身份验证联系信息，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“配置文件”。

8.  向下滚动到“身份验证联系信息”。

9.  **查看**为用户注册的数据并根据需要进行更新。

### <a name="check-a-users-group-memberships"></a>检查用户的组成员身份

若要检查用户的组成员身份，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“组”查看用户所属的组。

### <a name="check-a-users-assigned-licenses"></a>检查用户的已分配许可证

若要检查用户的已分配许可证，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给用户的许可证。

### <a name="assign-a-user-a-license"></a>为用户分配许可证 

若要将许可证分配给用户，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给用户的许可证。

8.  单击“分配”按钮。

9.  从可用产品列表中选择**一个或多个产品**。

10. **可选**单击“分配选项”项精确分配产品。 完成此操作后，单击“确定”。

11. 单击“分配”按钮，将这些许可证分配给此用户。

## <a name="problems-with-groups"></a>组问题

由于已分配到应用程序的组出现问题，应用程序访问可能会遭到阻止。 下面是可以用来进行故障排除并解决组和组成员身份问题的一些方法：

-   [检查组的成员身份](#check-a-groups-membership)

-   [检查动态组的成员身份条件](#check-a-dynamic-groups-membership-criteria)

-   [检查组的已分配许可证](#check-a-groups-assigned-licenses)

-   [重新处理组的许可证](#reprocess-a-groups-licenses)

-   [为组分配许可证](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>检查组的成员身份

若要检查组的成员身份，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有组”。

6.  **搜索**感兴趣的组，并**单击对应的行**进行选择。

7.  单击“成员”查看分配到此组的用户列表。

### <a name="check-a-dynamic-groups-membership-criteria"></a>检查动态组的成员身份条件 

若要检查动态组的成员身份条件，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有组”。

6.  **搜索**感兴趣的组，并**单击对应的行**进行选择。

7.  单击“动态成员身份规则”。

8.  查看为此组定义的**简单**或**高级**规则，并确保希望使其成为此组成员的用户满足这些条件。

### <a name="check-a-groups-assigned-licenses"></a>检查组的已分配许可证

若要检查组的已分配许可证，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有组”。

6.  **搜索**感兴趣的组，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给组的许可证。

### <a name="reprocess-a-groups-licenses"></a>重新处理组的许可证

若要重新处理组的已分配许可证，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有组”。

6.  **搜索**感兴趣的组，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给组的许可证。

8.  单击“重新处理”按钮，确保分配给此组成员的许可证是最新许可证。 这可能需要较长时间，具体取决于组的大小和复杂程度。

   >[!NOTE]
   >要更快地执行此操作，可考虑暂时会许可证直接分配给用户。 [为用户分配许可证](#problems-with-application-consent)。
   >
   >

### <a name="assign-a-group-a-license"></a>为组分配许可证

若要将许可证分配给组，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有组”。

6.  **搜索**感兴趣的组，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给组的许可证。

8.  单击“分配”按钮。

9.  从可用产品列表中选择**一个或多个产品**。

10. **可选**单击“分配选项”项精确分配产品。 完成此操作后，单击“确定”。

11. 单击“分配”按钮，将这些许可证分配给此组。 这可能需要较长时间，具体取决于组的大小和复杂程度。

   >[!NOTE]
   >要更快地执行此操作，可考虑暂时会许可证直接分配给用户。 [为用户分配许可证](#problems-with-application-consent)。
   > 
   >

## <a name="problems-with-conditional-access-policies"></a>条件访问策略问题

### <a name="check-a-specific-conditional-access-policy"></a>检查特定的条件访问策略

若要检查或验证单个条件访问策略：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“企业应用程序”。

5.  单击“条件访问”导航项。

6.  单击要检查的策略。

7.  检查不存在任何可能会阻止用户访问的特定条件、分配或其他设置。

   >[!NOTE]
   >可能需要暂时禁用此策略，确保它不影响登录。为此，请将“启用策略”切换按钮设置为“否”，然后单击“保存”按钮。
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>检查特定应用程序的条件访问策略

若要检查或验证单个应用程序当前配置的条件访问策略：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”。

6.  按应用程序显示名称或应用程序 ID 搜索感兴趣的或用户尝试登录到的应用程序。

     >[!NOTE]
     >如果看不到要查找的应用程序，请单击“筛选器”按钮并将列表的范围扩展到“所有应用程序”。 如果要查看更多列，请单击“列”按钮为应用程序添加其他详细信息。
     >
     >

7.  单击“条件访问”导航项。

8.  单击要检查的策略。

9.  检查不存在任何可能会阻止用户访问的特定条件、分配或其他设置。

     >[!NOTE]
     >可能需要暂时禁用此策略，确保它不影响登录。为此，请将“启用策略”切换按钮设置为“否”，然后单击“保存”按钮。
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>禁用特定的条件访问策略

若要检查或验证单个条件访问策略：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“企业应用程序”。

5.  单击“条件访问”导航项。

6.  单击要检查的策略。

7.  通过将“启用策略”切换按钮设置为“否”来禁用策略，然后单击“保存”按钮。

## <a name="problems-with-application-consent"></a>应用程序许可问题

由于相应的权限许可操作并未发生，应用程序访问可能会遭到阻止。 下面是可以用来进行故障排除并解决应用程序许可问题的一些方法：

-   [执行用户级许可操作](#perform-a-user-level-consent-operation)

-   [为任何应用程序执行管理员级许可操作](#perform-administrator-level-consent-operation-for-any-application)

-   [为单租户应用程序执行管理员级许可](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [为多租户应用程序执行管理员级许可](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>执行用户级许可操作

-   对于需要权限的任何已启用 Open ID Connect 的应用程序，导航到应用程序的登录屏幕都会面向已登录用户为应用程序执行用户级许可。

-   如果要以编程方式执行此操作，请参阅[请求单个用户的同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent)。

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>为任何应用程序执行管理员级许可操作

-   对于**仅使用 V1 应用程序模型开发的应用程序**，可通过在应用程序的登录 URL 末尾添加“?prompt=admin\_consent”强制执行此管理员级许可。

-   对于**使用 V2 应用程序模型开发的任何应用程序**，可以按照[使用管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)的“向目录管理员请求权限”部分中的说明强制执行此管理员级许可。

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>为单租户应用程序执行管理员级许可

-   对于请求权限的**单租户应用程序**（如组织内正在开发或组织拥有的应用程序），可以通过以全局管理员”身份登录，并单击“应用程序注册表”-&gt;“所有应用程序”-&gt; 选择应用 -&gt;“所需权限”窗格顶部的“授予权限”按钮，代表所有用户执行“管理级许可”操作。

-   对于**使用 V1 或 V2 应用程序模型开发的任何应用程序**，可以按照[使用管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)的“向目录管理员请求权限”部分下的说明强制执行此管理员级许可。

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>为多租户应用程序执行管理员级许可

-   对于请求权限的**多租户应用程序**（如第三方或 Microsoft 开发的应用程序），可以执行“管理级许可”操作。 以“全局管理员”身份登录，并在“企业应用程序”-&gt;“所有应用程序”-&gt; 选择应用 -&gt;“权限”窗格（即将可用）下单击“授予权限”按钮。

-   还可以按照[使用管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)的“向目录管理员请求权限”部分下的说明强制执行此管理员级许可。

## <a name="next-steps"></a>后续步骤
[使用管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

