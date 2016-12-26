---
title: "教程：Azure Active Directory 与 Google Apps 集成 | Microsoft Docs"
description: "了解如何使用 Google Apps 与 Azure Active Directory 以启用单一登录、自动化设置及更多内容！"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2016
ms.author: asmalser-msft
translationtype: Human Translation
ms.sourcegitcommit: 0edd2d9b2d71d95260aa122545930aab64612106
ms.openlocfilehash: cc7348b933b1ed4a79bf282c1d9a8e073b1f3ba4


---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>教程：Azure Active Directory 与 Google Apps 集成
本教程将演示如何将 Google Apps 环境连接到 Azure Active Directory (Azure AD)。 你将了解如何配置对 Google Apps 的单一登录、如何启用自动化用户设置，以及如何分配用户以访问 Google Apps 。 

## <a name="prerequisites"></a>先决条件
1. 若要通过 [Azure 经典门户](https://manage.windowsazure.com)访问 Azure Active Directory，必须先有一个有效的 Azure 订阅。
2. 必须拥有 [Google Apps for Work](https://www.google.com/work/apps/) 或者 [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/) 的有效租户。 免费试用帐户可用于任一服务。

## <a name="video-tutorial"></a>视频教程
如何在 2 分钟内启用 Google Apps 单一登录：

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]
> 
> 

## <a name="frequently-asked-questions"></a>常见问题
1. **问：Chromebook 和其他 Chrome 设备是否与 Azure AD 单一登录兼容？**
   
    答：是的，用户能够使用 Azure AD 凭据登录到 Chromebook 设备。 若要了解为何有时用户会两次收到输入凭据的提示，请参阅此 [Google Apps 支持文章](https://support.google.com/chrome/a/answer/6060880)。
2. **问：如果我启用单一登录，用户能够使用他们的 Azure AD 凭据登录到任何 Google 产品（例如 Google Classroom、GMail、Google Drive、YouTube 等）吗？**
   
    答：可以，具体取决于你选择为组织启用或禁用的 [Google 应用](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)。
3. **问：能否仅为 Google Apps 用户子集启用单一登录？**
   
    答：不可以，启用单一登录会立即要求所有 Google Apps 用户使用其 Azure AD 凭据进行身份验证。 由于 Google Apps 不支持具有多个标识提供者，因此，Google Apps 环境的标识提供者可以是 Azure AD 或 Google - 但不能同时为两者。
4. **问：如果用户通过 Windows 登录，他们是否会自动进行 Google Apps 身份验证而不会收到输入密码的提示？**
   
    答：有两种用于启用此方案的选项。 第一种，用户可通过 [Azure Active Directory Join](active-directory-azureadjoin-overview.md) 登录到 Windows 10 设备。 或者，用户可以登录到通过域加入的方式加入到一个本地 Active Directory（已通加 [Active Directory 联合身份验证服务 (AD FS)](active-directory-aadconnect-user-signin.md) 部署启用 Azure AD 单一登录）的 Windows 设备。 当然，两种选项都要求按照以下教程操作，以在 Azure AD 和 Google Apps 之间启用单一登录。

## <a name="step-1-add-google-apps-to-your-directory"></a>步骤 1：将 Google Apps 添加到你的目录
1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，在左侧导航窗格上，单击“Active Directory”。
   
    ![从左侧导航窗格选择“Active Directory”。][0]
2. 从“目录”列表选择想要添加 Google Apps 的目录。
3. 单击顶部菜单中的“应用程序”。
   
    ![单击“应用程序”。][1]
4. 在页面底部单击“添加”。
   
    ![单击“添加”以添加新应用程序。][2]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![单击“从库添加应用程序”。][3]
6. 在“搜索”框中键入“Google Apps”。 然后从结果中选择“Google Apps”，并单击“完成”以添加该应用程序。
   
    ![添加 Google Apps。][4]
7. 你现在应看到 Google Apps 的“快速启动”页：
   
    ![Azure AD 中 Google Apps 的“快速启动”页][5]

## <a name="step-2-enable-single-sign-on"></a>步骤 2：启用单一登录
1. 在 Azure AD 中 Google Apps 的“快速启动”页上，单击“配置单一登录”按钮。
   
    ![配置单一登录按钮][6]
2. 将打开一个对话框，其中显示“你希望用户如何登录到 Google Apps？” 选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![选择“Azure AD 单一登录”][7]
   
   > [!NOTE]
   > 若要了解有关不同单一登录选项的详细信息，请[单击此处](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. 在“配置应用设置”页上的“登录 URL”字段中，键入 Google Apps 租户 URL，格式如下：`https://mail.google.com/a/<yourdomain>`
   
    ![键入租户 URL][8]
4. 在“自动配置单一登录”页上，键入 Google Apps 租户的域。 然后按“配置”按钮。
   
    ![键入你的域名并按“配置”。](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)
   
   > [!NOTE]
   > 如果想要手动配置单一登录，请参阅[可选步骤：手动配置单一登录](#optional-step-manually-configure-single-sign-on)
   > 
   > 
5. 登录到你的 Google Apps 管理员帐户。 然后单击“允许”以允许 Azure Active Directory 在 Google Apps 订阅中进行配置更改。
   
    ![键入你的域名并按“配置”。](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)
6. 等待几秒钟，让 Azure Active Directory 配置你的 Google Apps 租户。 完成后，单击“下一步”。
7. 如果想要接收与此单一登录配置维护相关的错误和警告电子邮件通知，请在对话框的最后一页上键入电子邮件地址。
   
   ![键入电子邮件地址。][14]
8. 单击“完成”关闭对话框。 要测试配置，请参阅下面名为[将用户分配到 Google Apps](#step-4-assign-users-to-google-apps) 的部分。

## <a name="optional-step-manually-configure-single-sign-on"></a>可选步骤：手动配置单一登录
如果你想要手动设置单一登录，请完成以下步骤：

1. 在 Azure AD 中 Google Apps 的“快速启动”页上，单击“配置单一登录”按钮。
   
    ![配置单一登录按钮][6]
2. 将打开一个对话框，其中显示“你希望用户如何登录到 Google Apps？” 选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![选择“Azure AD 单一登录”][7]
   
   > [!NOTE]
   > 若要了解有关不同单一登录选项的详细信息，请[单击此处](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. 在“配置应用设置”页上的“登录 URL”字段中，键入 Google Apps 租户 URL，格式如下：`https://mail.google.com/a/<yourdomain>`
   
    ![键入租户 URL][8]
4. 在“自动配置单一登录”页上，选中标有“手动配置此应用程序进行单一登录”的复选框。 。
   
    ![选择手动配置。](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)
5. 在“在 Google Apps 上配置单一登录”页上，单击“下载证书”，然后将证书文件保存在本地计算机上。
   
    ![下载证书。][9]
6. 在浏览器中打开新选项卡并使用管理员帐户登录到 [Google Apps 管理员控制台](http://admin.google.com/)。
7. 单击“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。
   
    ![单击“安全”。][10]
8. 在“安全”页上单击“设置单一登录 (SSO)”。
   
    ![单击“SSO”。][11]
9. 执行以下配置更改：
   
    ![配置 SSO][12]
   
   * 选择“使用第三方标识提供者设置 SSO”。
   * 在 Azure AD 中，复制“单一登录服务 URL”，并将其粘贴到 Google Apps 中的“登录页 URL”字段。
   * 在 Azure AD 中，复制“单一注销服务 URL”，并将其粘贴到 Google Apps 中的“注销页 URL”字段。
   * 在 Azure AD 中，复制“更改密码 URL”，并将其粘贴到 Google Apps 中的“更改密码 URL”字段。
   * 在 Google Apps 中，为“验证证书”上传步骤 4 下载的证书。
   * 单击“保存更改”。
10. 在 Azure AD 中，选中单一登录配置确认复选框以启用上载到 Google Apps 的证书。 。
    
     ![选中确认复选框][13]
11. 如果想要接收与此单一登录配置维护相关的错误和警告电子邮件通知，请在对话框的最后一页上键入电子邮件地址。 
    
    ![键入电子邮件地址。][14]
12. 单击“完成”关闭对话框。 要测试配置，请参阅下面名为[将用户分配到 Google Apps](#step-4-assign-users-to-google-apps) 的部分。

## <a name="step-3-enable-automated-user-provisioning"></a>步骤 3：启用自动化用户设置
> [!NOTE]
> Google Apps 的自动化用户设置的另一种可行方法是使用 [Google Apps 目录同步 (GADS)](https://support.google.com/a/answer/106368?hl=en)，这将在 Google Apps 上设置本地 Active Directory 标识。 与此相反，本教程中的解决方案将在 Google Apps 上设置 Azure Active Directory（云）用户和启用邮件的组。
> 
> 

1. 使用管理员帐户登录到 [Google Apps 管理员控制台](http://admin.google.com/)，并单击“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。
   
    ![单击“安全”。][10]
2. 在“安全”页上，单击“API 参考”。
   
    ![单击“API 参考”。][15]
3. 选择“启用 API 访问”。
   
    ![单击“API 参考”。][16]
   
   > [!IMPORTANT]
   > 对于要设置到 Google Apps 的每个用户，他们在 Azure Active Directory 中的用户名必须绑定到自定义域。 例如，Google Apps 不会接受诸如 bob@contoso.onmicrosoft.com 此类的用户名，而会接受 bob@contoso.com。 可以通过在 Azure AD 中编辑属性来更改现有用户的域。 下面提供了有关如何为 Azure Active Directory 和 Google Apps 设置自定义域的说明。
   > 
   > 
4. 如果尚未向 Azure Active Directory 添加自定义域名，则按照以下步骤操作：
   
   * 在 [Azure 经典门户](https://manage.windowsazure.com)中，在左侧导航窗格上，单击“Active Directory”。 在目录列表中，选择你的目录。 
   * 从顶部菜单中单击“域”，然后单击“添加自定义域”。
     
       ![添加自定义域][17]
   * 在“域名”字段键入你的域名。 此域名应与你要为 Google Apps 使用的域名相同。 完成后，单击“添加”按钮。
     
       ![键入域名。][18]
   * 单击“下一步”转到验证页。 要验证你拥有该域，必须根据此页所提供的值编辑域的 DNS 记录。 可以选择使用“MX 记录”验证，或使用“TXT 记录”验证，具体取决于“记录类型”选项的选择。 有关如何验证域名与 Azure AD 的更全面说明，请参阅[将自己的域名添加到 Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)。
     
       ![验证域名。][19]
   * 对所有要添加到目录的域重复上述步骤。
5. 完成所有域与 Azure AD 的验证后，现在必须与 Google Apps 再次进行验证。 对于每个尚未注册 Google Apps 的域，请执行以下步骤：
   
   * 在 [Google Apps 管理员控制台](http://admin.google.com/)中单击“域”。
     
       ![单击“域”][20]
   * 单击“添加域或域别名”。
     
       ![添加新域][21]
   * 选择“添加另一个域”，然后键入想要添加的域名。
     
       ![键入域名][22]
   * 单击“继续验证域所有权”。 然后按步骤验证所拥有的域名。 有关如何验证域与 Google Apps 的完整说明，请参阅[验证站点所有权与 Google Apps ](https://support.google.com/webmasters/answer/35179)。
   * 对所有要添加到 Google Apps 的其他域重复上述步骤。
     
     > [!WARNING]
     > 如果更改了 Google Apps 租户的主域并且已配置 Azure AD 的单一登录，则必须重复[步骤二：启用单一登录](#step-two-enable-single-sign-on)下的步骤 3。
     > 
     > 
6. 在[Google Apps 管理员控制台](http://admin.google.com/)中单击“管理员角色”。
   
    ![单击“Google Apps”][26]
7. 确定想要用于管理用户设置的管理员帐户。 对于该帐户的“管理员角色”，编辑该角色的“特权”。 请确保该帐户已启用所有“管理员 API 权限”，以便其可用于设置。
   
    ![单击“Google Apps”][27]
   
   > [!NOTE]
   > 如果正配置生产环境，最佳做法是专门为此步骤在 Google Apps 中创建新的管理员帐户。 这些帐户必须关联具有必要 API 特权的管理员角色。
   > 
   > 
8. 在 Azure Active Directory 中，单击顶部菜单中的“应用程序”，然后单击“Google Apps”。
   
    ![单击“Google Apps”][23]
9. 单击 Google Apps“快速启动”页上的“配置用户设置”。
   
    ![配置用户设置][24]
10. 在打开的对话框中，单击“启用用户设置”对想要用于管理设置的 Google Apps 管理员帐户进行身份验证。
    
    ![启用设置][25]
11. 确认想要为 Azure Active Directory 授予的权限，以对 Google Apps 租户进行更改。
    
    ![确认权限。][28]
12. 单击“完成”关闭对话框。

## <a name="step-4-assign-users-to-google-apps"></a>步骤 4：将用户分配到 Google Apps
1. 若要测试配置，首先在目录中创建新的测试帐户。
2. 在 Google Apps“快速启动”页上，单击“分配用户”按钮。
   
    ![单击“分配用户”][29]
3. 选择测试用户，然后单击屏幕底部的“分配”按钮：
   
   * 如果尚未启用自动化用户设置，则系统将提示你确认：
     
        ![确认分配。][30]
   * 如果已启用自动化用户设置，则将显示定义用户在 Google Apps 中应具有的角色类型的提示。 新设置的用户应在几分钟后显示在 Google Apps 环境中。
4. 若要测试单一登录设置，打开“访问面板”（网址为 [https://myapps.microsoft.com](https://myapps.microsoft.com/)），然后登录到测试帐户，并单击“Google Apps”。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png



<!--HONumber=Nov16_HO4-->


