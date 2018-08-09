---
title: 教程：使用 Azure Active Directory 为 G Suite 配置自动用户预配 | Microsoft Docs
description: 了解如何将用户帐户从 Azure AD 自动预配到 G Suite 及如何取消预配。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 26715c6abb9c2c940090c84b64a30f7fb701d059
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445683"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>教程：为 G Suite 配置自动用户预配

本教程旨在介绍如何从 Azure Active Directory (Azure AD) 自动将用户帐户预配到 G Suite 和取消其预配。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](./../active-directory-saas-app-provisioning.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 G Suite 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 G Suite 单一登录的订阅
- Google Apps 订阅或 Google Cloud Platform 订阅。

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="assign-users-to-g-suite"></a>将用户分配到 G Suite

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中应用程序的用户和组。

配置和启用预配服务前，需确定 Azure AD 中的哪些用户或组需访问应用。 确定后，可按照[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)中的说明将这些用户分配到应用。

> [!IMPORTANT]
> 建议将单个 Azure AD 用户分配到 G Suite 以测试预配配置。 可稍后再分配其他用户和组。

> 将用户分配到 G Suite 时，在分配对话框中选择“用户”或“组”角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-automated-user-provisioning"></a>启用自动化用户预配

本部分将指导你完成将 Azure AD 连接到 G Suite 的用户帐户预配 API 这一过程。 它还帮助你配置预配服务以根据 Azure AD 中的用户和组分配在 G Suite 中创建、更新和禁用分配的用户帐户。

>[!TIP]
>还可以选择按照 [Azure 门户](https://portal.azure.com)中的说明为 G Suite 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-account-provisioning"></a>配置用户帐户自动预配

> [!NOTE]
> 将用户自动预配到 G Suite 的另一种可行方法是使用 [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en)。 GADS 将本地 Active Directory 身份预配到 G Suite。 与此相反，本教程中的解决方案会将 Azure Active Directory（云）用户和启用电子邮件的组预配到 G Suite。 

1. 使用管理员帐户登录到 [Google Apps 管理员控制台](http://admin.google.com/)，然后选择“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。
   
    ![选择“安全”。][10]

1. 在“安全”页上，选择“API 参考”。
   
    ![选择“API 参考”。][15]

1. 选择“启用 API 访问”。
   
    ![选择“API 参考”。][16]

    > [!IMPORTANT]
    > 对于要预配到 G Suite 的每个用户，他们在 Azure Active Directory 中的用户名必须绑定到自定义域。 例如，G Suite 不会接受 bob@contoso.onmicrosoft.com 之类的用户名， 但会接受 bob@contoso.com。 可以通过在 Azure AD 中编辑属性来更改现有用户的域。 在以下步骤中我们介绍了如何为 Azure Active Directory 和 G Suite 设置自定义域。
      
1. 如果尚未向 Azure Active Directory 添加自定义域名，请按照以下步骤操作：
  
    a. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，选择“Active Directory”。 在目录列表中，选择目录。 

    b. 选择左侧导航窗格中的“域名”，然后单击“添加”。
     
     ![域](./media/google-apps-provisioning-tutorial/domain_1.png)

     ![添加域](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. 在“域名”字段键入域名。 此域名应与要为 G Suite 使用的域名相同。 然后选择“添加域”按钮。
     
     ![域名](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. 选择“下一步”转到验证页。 要验证是否拥有该域，请根据此页所提供的值编辑域的 DNS 记录。 可选择使用“MX 记录”验证，或使用“TXT 记录”验证，具体取决于“记录类型”选项的选择。 
    
    有关如何向 Azure AD 验证域名的更全面说明，请参阅[将自己的域名添加到 Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)。
     
     ![域](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. 对所有要添加到目录的域重复上述步骤。

    > [!NOTE]
    若要进行用户预配，自定义域必须与源 Azure AD 的域名匹配。 如果不匹配，可以通过实现属性映射自定义来解决此问题。


1. 向 Azure AD 验证完所有域后，必须再次向 Google Apps 验证这些域。 对于每个尚未向 Google 注册的域，请执行以下步骤：
   
    a. 在 [Google Apps 管理员控制台](http://admin.google.com/)中，选择“域”。
     
     ![选择域][20]

    b. 选择“添加域或域别名”。
     
     ![添加新域][21]

    c. 选择“添加另一个域”，然后键入要添加的域名。
     
     ![键入域名][22]

    d. 选择“继续验证域所有权”。 然后按步骤验证所拥有的域名。 有关如何通过 Google 来验证域的完整说明，请参阅[通过 Google Apps 验证站点所有权](https://support.google.com/webmasters/answer/35179)。

    e. 对所有要添加到 Google Apps 的其他域重复上述步骤。
     
     > [!WARNING]
     > 如果更改了 G Suite 租户的主域并且已配置 Azure AD 单一登录，则必须重复[步骤 2：启用单一登录](#step-two-enable-single-sign-on)下的步骤 3。
       
1. 在 [Google Apps 管理员控制台](http://admin.google.com/)中，选择“管理员角色”。
   
     ![选择 Google Apps][26]

1. 确定想要用于管理用户预配的管理员帐户。 对于该帐户的“管理员角色”，编辑该角色的“特权”。 请确保启用该帐户的所有“管理员 API 权限”，使其可用于预配。
   
     ![选择 Google Apps][27]
   
    > [!NOTE]
    > 如果要配置生产环境，最佳做法是专门为此步骤在 G Suite 中创建管理员帐户。 这些帐户必须关联具有必要 API 特权的管理员角色。
     
1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory” > “企业应用” > “所有应用程序”部分。

1. 如果已为 G Suite 配置单一登录，请使用搜索字段搜索 G Suite 实例。 否则，请选择“添加”，然后在应用程序库中搜索“G Suite”。 从搜索结果中选择你的应用，然后将其添加到应用程序列表。

1. 选择 G Suite 实例，然后选择“预配”选项卡。

1. 将“预配模式”设置为“自动”。 

     ![设置](./media/google-apps-provisioning-tutorial/provisioning.png)

1. 在“管理员凭据”部分，选择“授权”。 随即会在新的浏览器窗口中打开“Google 授权”对话框。

1. 确认想要授权 Azure Active Directory 对你的 G Suite 租户进行更改。 选择“接受”。
    
     ![确认权限。][28]

1. 在 Azure 门户中，选择“测试连接”以确保 Azure AD 可以连接到你的应用。 如果连接失败，请确保 G Suite 帐户具有团队管理员权限。 然后再次重试“授权”步骤。

1. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址。 然后选中复选框。

1. 选择“保存”。

1. 在“映射”部分，选择“将 Azure Active Directory 用户同步到 Google Apps”。

1. 在“属性映射”部分中，查看从 Azure AD 同步到G Suite 的用户属性。 选为“匹配”属性的特性用于匹配 G Suite 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改。

1. 要为 G Suite 启用 Azure AD 预配服务，请在“设置”中将“预配状态”更改为“启用”。

1. 选择“保存”。

此操作会对在“用户和组”部分中分配到 G Suite 的任何用户或组启动初始同步。 初始同步执行的时间比后续同步长，当服务处于运行状态时，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接。 这些日志描述了预配服务对应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
