---
title: "教程：Azure Active Directory 与 Salesforce 集成 | Microsoft Docs"
description: "了解如何使用 Salesforce 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2016
ms.author: asmalser-msft
translationtype: Human Translation
ms.sourcegitcommit: 0edd2d9b2d71d95260aa122545930aab64612106
ms.openlocfilehash: af84b6b4e4c64155ebf9d6b8aa40f7837661ee41


---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>教程：Azure Active Directory 与 Salesforce 集成
本教程将演示如何将 Salesforce 环境连接到 Azure Active Directory。 你将了解如何配置对 Salesforce 的单一登录、如何启用自动化用户预配，以及如何分配用户以访问 Salesforce。

## <a name="prerequisites"></a>先决条件
1. 若要通过 [Azure 经典门户](https://manage.windowsazure.com)访问 Azure Active Directory，必须先有一个有效的 Azure 订阅。
2. 必须在 [Salesforce.com](https://www.salesforce.com/) 中拥有有效的租户。

> [!IMPORTANT]
> 如果使用 Salesforce.com **试用**帐户，将无法配置自动化的用户预配。 试用帐户没有所需的 API 访问权限，而只有在付费之后才拥有这种权限。
> 
> 可以使用[免费的开发人员帐户](https://developer.salesforce.com/signup)克服这项限制，以完成本教程。
> 
> 

如果使用 Salesforce 沙盒环境，请参阅 [Salesforce 沙箱集成教程](https://go.microsoft.com/fwLink/?LinkID=521879)。

## <a name="video-tutorials"></a>视频教程
可以使用以下视频跟进本教程。

**视频教程第一部分：如何启用单一登录**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Integrating-Salesforce-with-Azure-AD-How-to-enable-Single-Sign-On-12/player]
> 
> 

**视频教程第二部分：如何自动化用户设置**

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Integrating-Salesforce-with-Azure-AD-How-to-automate-User-Provisioning-22/player]
> 
> 

## <a name="step-1-add-salesforce-to-your-directory"></a>步骤 1：将 Salesforce 添加到你的目录
1. 在 [Azure 经典门户](https://manage.windowsazure.com)的左侧导航窗格上，单击“Active Directory”。
   
    ![从左侧导航窗格选择“Active Directory”。][0]
2. 从“目录”列表选择想要添加 Salesforce 的目录。
3. 单击顶部菜单中的“应用程序”。
   
    ![单击“应用程序”。][1]
4. 在页面底部单击“添加”。
   
    ![单击“添加”以添加新应用程序。][2]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![单击“从库添加应用程序”。][3]
6. 在“搜索”框中键入“Salesforce”。 然后从结果中选择“Salesforce”，并单击“完成”以添加该应用程序。
   
    ![添加 Salesforce。][4]
7. 你现在应看到 Salesforce 的“快速启动”页：
   
    ![Azure AD 中 Salesforce 的“快速启动”页][5]

## <a name="step-2-enable-single-sign-on"></a>步骤 2：启用单一登录
1. 在配置单一登录之前，必须先为 Salesforce 环境设置并部署自定义域。 有关如何执行此操作的说明，请参阅[设置域名](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US)。
2. 在 Azure AD 中 Salesforce 的“快速启动”页上，单击“配置单一登录”按钮。
   
    ![配置单一登录按钮][6]
3. 将打开一个对话框，其中显示“你希望用户如何登录到 Salesforce?” 选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![选择“Azure AD 单一登录”][7]
   
   > [!NOTE]
   > 若要了解有关不同单一登录选项的详细信息，请[单击此处](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
4. 在“配置应用设置”页上，在“登录 URL”中使用以下格式键入 Salesforce 域 URL：
   
   * 企业帐户：`https://<domain>.my.salesforce.com`
   * 开发人员帐户：`https://<domain>-dev-ed.my.salesforce.com` 
     
     ![键入你的登录 URL][8]
5. 在“在 Salesforce 上配置单一登录”页上，单击“下载证书”，然后将证书文件保存在本地计算机上。
   
    ![下载证书][9]
6. 在浏览器中打开新选项卡并登录到你的 Salesforce 管理员帐户。
7. 在“管理员”导航窗格下，单击“安全控件”以展开相关部分。 然后单击“单一登录设置”。
   
    ![单击“安全控件”下面的“单一登录设置”][10]
8. 在“单一登录设置”页上，单击“编辑”按钮。
   
    ![单击“Edit”按钮][11]
   
   > [!NOTE]
   > 如果无法为 Salesforce 帐户启用单一登录设置，你可能需要联系 Salesforce 支持人员，让他们为你启用该功能。
   > 
   > 
9. 选择“已启用 SAML”，然后单击“保存”。
   
    ![选择“已启用 SAML”][12]
10. 若要配置 SAML 单一登录设置，请单击“新建”。
    
    ![选择“已启用 SAML”][13]
11. 在“SAML 单一登录设置编辑”页上进行以下配置：
    
    ![应使用的配置的屏幕截图][14]
    
    * 在“名称”字段中，为此配置键入友好名称。 提供“名称”值时会自动填充“API 名称”文本框。
    * 在 Azure AD 中，复制“颁发者 URL”值，然后将其粘贴到 Salesforce 中的“颁发者”字段。
    * 在“实体 ID”文本框中，使用以下模式输入 Salesforce 域名：
      
      * 企业帐户：`https://<domain>.my.salesforce.com`
      * 开发人员帐户：`https://<domain>-dev-ed.my.salesforce.com`
    * 单击“浏览”或“选择文件”打开“选择要上传的文件”对话框，选择你的 Salesforce 证书，然后单击“打开”以上传该证书。
    * 对于“SAML 标识类型”，请选择“断言包含用户的 salesforce.com 用户名”。
    * 对于“SAML 标识位置”，请选择“标识位于 Subject 语句的 NameIdentifier 元素中”
    * 在 Azure AD 中，复制“远程登录 URL”值，然后将它其粘贴到 Salesforce 中的“标识提供者登录 URL”字段。
    * 对于“服务提供程序发起的请求绑定”，请选择“HTTP 重定向”。
    * 最后，单击“保存”以应用 SAML 单一登录设置。
12. 在 Salesforce 的左侧导航窗格中，单击“域管理”展开相关部分，然后单击“我的域”。
    
    ![单击“我的域”][15]
13. 向下滚动到“身份验证配置”部分，然后单击“编辑”按钮。
    
    ![单击“Edit”按钮][16]
14. 在“身份验证服务”部分，选择 SAML SSO 配置的友好名称，然后单击“保存”。
    
    ![选择你的 SSO 配置][17]
    
    > [!NOTE]
    > 如果选择了多个身份验证服务，则当用户尝试向 Salesforce 环境发起单一登录时，系统将提示他们选择登录时要使用的身份验证服务。 如果不希望发生这种情况，应该**将其他所有身份验证服务保持未选中状态**。
    > 
    > 
15. 在 Azure AD 中，选中单一登录配置确认复选框以启用上载到 Salesforce 的证书。 。
    
    ![选中确认复选框][18]
16. 如果想要接收与此单一登录配置维护相关的错误和警告电子邮件通知，请在对话框的最后一页上键入电子邮件地址。 
    
    ![键入电子邮件地址。][19]
17. 单击“完成”关闭对话框。 要测试配置，请参阅下面名为[将用户分配到 Salesforce](#step-4-assign-users-to-salesforce) 的部分。

## <a name="step-3-enable-automated-user-provisioning"></a>步骤 3：启用自动化用户预配
1. 在 Azure AD 中 Salesforce 的“快速启动”页上，单击“配置用户预配”按钮。
   
    ![单击“配置用户预配”按钮][20]
2. 在“配置用户预配”对话框中，键入 Salesforce 管理员用户名和密码。
   
    ![输入你的管理员用户名或密码][21]
   
   > [!NOTE]
   > 如果正配置生产环境，最佳做法是专门为此步骤在 Salesforce 中创建新的管理员帐户。 必须在 Salesforce 中为此帐户分配“系统管理员”配置文件。
   > 
   > 
3. 若要获取 Salesforce 安全令牌，请打开新选项卡并登录到同一个 Salesforce 管理员帐户。 在页面右上角单击你的名称，然后单击“我的设置”。
   
    ![单击你的名称，然后单击“我的设置”][22]
4. 在左侧导航窗格中，单击“个人”展开相关部分，然后单击“重置我的安全令牌”。
   
    ![单击你的名称，然后单击“我的设置”][23]
5. 在“重置我的安全令牌”页上，单击“重置安全令牌”按钮。
   
    ![阅读警告。][24]
6. 查看与此管理员帐户关联的电子邮件收件箱。 查找来自 Salesforce.com 的包含新安全令牌的电子邮件。
7. 复制令牌，转到 Azure AD 窗口，然后将令牌粘贴到“用户安全令牌”字段中。 。
   
    ![粘贴安全令牌][25]
8. 在确认页上，可以选择在发生预配失败时接收电子邮件通知。 单击“完成”关闭对话框。
   
    ![键入你的电子邮件地址以接收通知][26]

## <a name="step-4-assign-users-to-salesforce"></a>步骤 4：将用户分配到 Salesforce
1. 若要测试配置，首先在目录中创建新的测试帐户。
2. 在 Salesforce 的“快速启动”页上，单击“分配用户”按钮。
   
    ![单击“分配用户”][27]
3. 选择测试用户，然后单击屏幕底部的“分配”按钮：
   
   * 如果尚未启用自动化用户设置，则系统将提示你确认：
     
        ![确认分配。][28]
   * 如果你已启用自动化用户预配，系统会提示你定义用户应具有的 Salesforce 配置文件类型。 新预配的用户应在几分钟后显示在 Salesforce 环境中。
     
        ![确认分配。][29]
     
     > [!IMPORTANT]
     > 如果预配到 Salesforce“开发人员”环境，每个配置文件的可用许可证数量非常有限。 因此，最好将用户预配到具有 4999 个可用许可证的“Chatter Free 用户”配置文件。
     > 
     > 
4. 若要测试单一登录设置，请打开“访问面板”（网址为 [https://myapps.microsoft.com](https://myapps.microsoft.com/)），登录到测试帐户，然后单击“Salesforce”。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png



<!--HONumber=Nov16_HO4-->


