---
title: 如何为 Azure AD 应用配置密码单一登录 |微软文档
description: 如何在 Microsoft 标识平台 （Azure AD） 中配置密码单一登录 （SSO） 到 Azure AD 企业应用程序
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063520"
---
# <a name="configure-password-single-sign-on"></a>配置密码单一登录

将[库应用](add-gallery-app.md)[或非库 Web 应用](add-non-gallery-app.md)添加到 Azure AD 企业应用程序时，可用的单一登录选项之一是基于[密码的单一登录](what-is-single-sign-on.md#password-based-sso)。 此选项可用于任何具有 HTML 登录页的 Web。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 对于多个用户需要共享单个帐户（例如，到组织的社交媒体应用帐户）的方案，这非常有用。 

基于密码的 SSO 是快速开始将应用程序集成到 Azure AD 中的绝佳方式，并允许您：

-   通过安全地存储和重播已集成到 Azure AD 的应用程序用户名和密码，启用“用户单一登录”****

-   **支持需要多个登录字段的应用程序**，这适用于不只需要用户名和密码字段才能登录的应用程序

-   **自定义标签**，即指用户输入其凭据时，会在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上看到的用户名和密码输入字段的标签

-   允许**用户**为正在[应用程序访问面板](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)上手动键入的任何现有应用程序帐户提供自己的用户名和密码

-   允许**业务组的成员**使用[自助应用程序访问](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)功能指定分配给用户的用户名和密码

-   允许**管理员**使用"更新凭据"功能指定个人或组在登录应用程序时使用的用户名和密码 

## <a name="before-you-begin"></a>开始之前

如果尚未将应用程序添加到 Azure AD 租户，请参阅[添加库应用](add-gallery-app.md)或[添加非库应用](add-non-gallery-app.md)。

## <a name="open-the-app-and-select-password-single-sign-on"></a>打开应用并选择密码单一登录

1. 以 Azure AD 租户的云应用程序管理员或应用程序管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

2. 导航到**Azure 活动目录** > **企业应用程序**。 此时会显示 Azure AD 租户中应用程序的随机示例。 

3. 在“应用程序类型”菜单中选择“所有应用程序”，然后选择“应用”************。

4. 在“搜索”框中输入应用程序的名称，然后从结果中选择该应用程序。

5. 在“管理”部分选择“单一登录”。******** 

6. 选择**基于密码**的 。

7. 输入应用程序的基于 Web 的登录页的 URL。 此字符串必须是包含用户名输入字段的页面。

   ![基于密码的单一登录](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. 选择“保存”。**** Azure AD 尝试分析用户名输入和密码输入的登录页。 如果尝试成功，您就完成了。 
 
> [!NOTE]
> 下一步是[将用户或组分配给应用程序](methods-for-assigning-users-and-groups.md)。 分配用户和组后，可以提供在用户登录到应用程序时代表用户使用的凭据。 选择 **"用户和组**"，选择用户或组行的复选框，然后单击"**更新凭据**"。 然后，输入代表用户或组使用的用户名和密码。 否则，系统将提示用户在启动时自己输入凭据。
 

## <a name="manual-configuration"></a>手动配置

如果 Azure AD 的解析尝试失败，您可以手动配置登录。

1. 在**\<"配置>"下**，**选择\<"配置应用程序名称>密码单一登录设置**"以显示 **"配置登录"** 页。 

2. 选择 **"手动检测登录字段**"。 将显示描述手动检测登录字段的其他说明。

   ![手动配置基于密码的单一登录](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. 选择 **"捕获登录字段**"。 捕获状态页将在新选项卡中打开，显示消息**元数据捕获当前正在进行**。

4. 如果 **"访问面板扩展必需"** 框出现在新选项卡中，请选择"**立即安装**"以安装 **"我的应用安全登录扩展"浏览器扩展**。 （浏览器扩展需要微软边缘、Chrome 或 Firefox。然后安装、启动和启用扩展，然后刷新捕获状态页。

   然后，浏览器扩展将打开另一个显示输入 URL 的选项卡。
5. 在包含输入 URL 的选项卡中，完成登录过程。 填写用户名和密码字段，并尝试登录。 （您不必提供正确的密码。

   提示要求您保存捕获的登录字段。
6. 选择“确定”。 浏览器扩展更新捕获状态页与消息**元数据已更新的应用程序**。 浏览器选项卡关闭。

7. 在 Azure AD**配置登录页上**，选择 **"确定"，我能够成功登录到应用**。

8. 选择“确定”。

捕获登录页后，您可以分配用户和组，也可以设置凭据策略，就像常规[密码 SSO 应用程序](what-is-single-sign-on.md)一样。

> [!NOTE]
> 可以在应用程序的“配置”选项卡中使用“上传徽标”按钮来上传应用程序的磁贴徽标。********

## <a name="next-steps"></a>后续步骤

- [将用户或组分配到应用程序](methods-for-assigning-users-and-groups.md)
- [配置自动用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
