---
title: "教程：在 Azure 中为自动用户预配配置 Google Apps | Microsoft Docs"
description: "了解如何将用户帐户从 Azure AD 自动预配到 Google Apps 及如何取消预配。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: b061f0ddad70be4a5ca48d48d1a737d6af8afa8d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="tutorial-configuring-google-apps-for-automatic-user-provisioning"></a>教程：为自动用户预配配置 Google Apps

本教程的目的是说明从 Azure AD 自动将用户帐户预配到 Google Apps 和取消其预配所需在 Google Apps 和 Azure 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

*   Azure Active Directory 租户。
*   必须拥有 Google Apps for Work 或者 Google Apps for Education 的有效租户。 免费试用帐户可用于任一服务。
*   具有团队管理员权限的 Google Apps 用户帐户。

## <a name="assigning-users-to-google-apps"></a>将用户分配到 Google Apps

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 Google Apps 应用的用户。 确定后，可按照此处的说明将这些用户分配到 Google Apps 应用：[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

> [!IMPORTANT]
>*   建议将单个 Azure AD 用户分配到 Google Apps 以测试预配配置。 其他用户和/或组可以稍后分配。
>*   将用户分配到 Google Apps 时，必须在分配对话框中选择“用户”或“组”角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-automated-user-provisioning"></a>启用自动化用户预配

本部分指导完成将 Azure AD 连接到 Google Apps 的用户帐户预配 API 和配置预配服务，以便在 Google Apps 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

>[!Tip]
>还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Google Apps 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-account-provisioning"></a>配置用户帐户自动预配

> [!NOTE]
> Google Apps 的自动化用户设置的另一种可行方法是使用 [Google Apps 目录同步 (GADS)](https://support.google.com/a/answer/106368?hl=en)，这将在 Google Apps 上设置本地 Active Directory 标识。 与此相反，本教程中的解决方案将在 Google Apps 上设置 Azure Active Directory（云）用户和启用邮件的组。 

1. 使用管理员帐户登录到 [Google Apps 管理员控制台](http://admin.google.com/)，并单击“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。
   
    ![单击“安全”。][10]

2. 在“安全”页上，单击“API 参考”。
   
    ![单击“API 参考”。][15]

3. 选择“启用 API 访问”。
   
    ![单击“API 参考”。][16]

    > [!IMPORTANT]
    > 对于要设置到 Google Apps 的每个用户，他们在 Azure Active Directory 中的用户名必须绑定到自定义域。 例如，Google Apps 不会接受诸如 bob@contoso.onmicrosoft.com 此类的用户名，而会接受 bob@contoso.com。 可以通过在 Azure AD 中编辑属性来更改现有用户的域。 在以下步骤中说明了如何为 Azure Active Directory 和 Google Apps 设置自定义域。
      
4. 如果尚未向 Azure Active Directory 添加自定义域名，请按照以下步骤操作：
  
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Active Directory”。 在目录列表中，选择你的目录。 

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在左侧导航窗格中单击“域名”，然后单击“添加”。
     
     ![域](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_1.png)

     ![添加域](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_2.png)

    c. 在“域名”字段键入你的域名。 此域名应与要为 Google Apps 使用的域名相同。 完成后，单击“添加域”按钮。
     
     ![域名](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_3.png)

    d. 单击“下一步”转到验证页。 要验证你拥有该域，必须根据此页所提供的值编辑域的 DNS 记录。 可以选择使用“MX 记录”验证，或使用“TXT 记录”验证，具体取决于“记录类型”选项的选择。 有关如何验证域名与 Azure AD 的更完整的说明，请参考[将自己的域名添加到 Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)。
     
     ![域](./media/active-directory-saas-google-apps-provisioning-tutorial/domain_4.png)

    e. 对所有要添加到目录的域重复上述步骤。

5. 完成所有域与 Azure AD 的验证后，现在必须与 Google Apps 再次进行验证。 对于每个尚未注册 Google Apps 的域，请执行以下步骤：
   
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在 [Google Apps 管理员控制台](http://admin.google.com/)中单击“域”。
     
     ![单击“域”][20]

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 单击“添加域或域别名”。
     
     ![添加新域][21]

    c. 选择“添加另一个域”，然后键入想要添加的域名。
     
     ![键入域名][22]

    d. 单击“继续验证域所有权”。 然后按步骤验证所拥有的域名。 有关如何验证域与 Google Apps 的完整说明，请参阅 [使用 Google Apps 验证站点所有权](https://support.google.com/webmasters/answer/35179)。

    e. 对所有要添加到 Google Apps 的其他域重复上述步骤。
     
     > [!WARNING]
     > 如果更改了 Google Apps 租户的主域并且已配置 Azure AD 的单一登录，则必须重复[步骤二：启用单一登录](#step-two-enable-single-sign-on)下的步骤 3。
       
6. 在[Google Apps 管理员控制台](http://admin.google.com/)中单击“管理员角色”。
   
     ![单击“Google Apps”][26]

7. 确定想要用于管理用户设置的管理员帐户。 对于该帐户的“管理员角色”，编辑该角色的“特权”。 请确保该帐户已启用所有“管理员 API 权限”，使其可用于预配。
   
     ![单击“Google Apps”][27]
   
    > [!NOTE]
    > 如果正配置生产环境，最佳做法是专门为此步骤在 Google Apps 中创建管理员帐户。 这些帐户必须关联具有必要 API 特权的管理员角色。
     
8. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

9. 如果已为 Google Apps 配置单一登录，请使用搜索字段搜索 Google Apps 实例。 否则，请选择“添加”并在应用程序库中搜索“Google Apps”。 从搜索结果中选择 Google Apps，并将其添加到应用程序列表。

10. 选择 Google Apps 实例，并选择“预配”选项卡。

11. 将“预配模式”设置为“自动”。 

     ![预配](./media/active-directory-saas-google-apps-provisioning-tutorial/provisioning.png)

12. 在“管理员凭据”部分下，单击“授权”。 这会在新的浏览器窗口中打开“Google Apps 授权”对话框。

13. 确认想要为 Azure Active Directory 授予的权限，以对 Google Apps 租户进行更改。 单击“接受”。
    
     ![确认权限。][28]

14. 在 Azure 门户中，单击“测试连接”确保 Azure AD 可以连接到 Google Apps 应用。 如果连接失败，请确保 Google Apps 帐户具有团队管理员权限，并重试“授权”步骤。

15. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址，并选中复选框。

16. 单击“保存”。

17. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Google Apps”。

18. 在“属性映射”部分，查看从 Azure AD 同步到 Google Apps 的用户属性。 选为“匹配”属性的特性用于匹配 Google Apps 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

19. 若要为 Google Apps 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

20. 单击“保存”。

此操作会对“用户和组”部分中分配到 Google Apps 的任何用户和/或组启动初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务对 Google Apps 应用执行的所有操作。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-provisioning-tutorial/gapps-auth.png
