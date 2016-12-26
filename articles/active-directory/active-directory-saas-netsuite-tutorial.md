---
title: "教程：Azure Active Directory 与 NetSuite 集成 | Microsoft Docs"
description: "了解如何使用 NetSuite与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2016
ms.author: asmalser-msft
translationtype: Human Translation
ms.sourcegitcommit: 789ec6f0a230f49ecd935bc85ea4ad984e936da9
ms.openlocfilehash: 74642e0e8e0420bb1226c53713ccf50e0a46b776


---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>教程：Azure Active Directory 与 NetSuite 集成
本教程将演示如何将 NetSuite 环境连接到 Azure Active Directory (Azure AD)。 了解如何配置对 NetSuite 的单一登录、如何启用自动化用户预配，以及如何分配用户以访问 NetSuite。 

## <a name="prerequisites"></a>先决条件
1. 若要通过 [Azure 经典门户](https://manage.windowsazure.com)访问 Azure Active Directory，必须先有一个有效的 Azure 订阅。
2. 必须具有对 [NetSuite](http://www.netsuite.com/portal/home.shtml) 订阅的管理员访问权限。 免费试用帐户可用于任一服务。

## <a name="step-1-add-netsuite-to-your-directory"></a>步骤 1：将 NetSuite 添加到你的目录
1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，在左侧导航窗格上，单击“Active Directory”。
   
    ![从左侧导航窗格选择“Active Directory”。][0]
2. 从“目录”列表选择要将 NetSuite 添加到的目标目录。
3. 单击顶部菜单中的“应用程序”。
   
    ![单击“应用程序”。][1]
4. 在页面底部单击“添加”。
   
    ![单击“添加”以添加新应用程序。][2]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![单击“从库添加应用程序”。][3]
6. 在“搜索”框中键入“NetSuite”。 然后从结果中选择“NetSuite”，并单击“完成”以添加该应用程序。
   
    ![添加 NetSuite][4]
7. 现在应看到 NetSuite 的“快速启动”页：
   
    ![Azure AD 中 NetSuite 的“快速启动”页][5]

## <a name="step-2-enable-single-sign-on"></a>步骤 2：启用单一登录
1. 在 Azure AD 中 NetSuite 的“快速启动”页上，单击“配置单一登录”按钮。
   
    ![配置单一登录按钮][6]
2. 将打开一个对话框，其中显示“你希望用户如何登录到 NetSuite?” 选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![选择“Azure AD 单一登录”][7]
   
   > [!NOTE]
   > 若要了解有关不同单一登录选项的详细信息，请[单击此处](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)
   > 
   > 
3. 在“配置应用设置”页上的“回复 URL”字段中，使用以下格式之一键入 NetSuite 租户 URL：
   
   * `https://<tenant-name>.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na1.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na2.netsuite.com/saml2/acs`
   * `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
   * `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`
     
     ![键入租户 URL][8]
4. 在“在 NetSuite 上配置单一登录”页上，单击“下载元数据”，然后将证书文件保存在本地计算机上。
   
    ![下载元数据。][9]
5. 在浏览器中打开新选项卡，然后以管理员的身分登录 Netsuite 公司站点。
6. 在页面顶部的工具栏上，单击“设置”，然后单击“设置管理员”。
   
    ![转到“设置管理器”][10]
7. 从“设置任务”列表中，选择“集成”。
   
    ![转到“集成”][11]
8. 在“管理身份验证”部分中，单击“SAML 单一登录”。
   
    ![转到“SAML 单一登录”][12]
9. 在“SAML 设置”页上执行以下步骤：
   
   * 在 Azure Active Directory 中，复制“远程登录 URL”值，然后将它粘贴到 NetSuite 中的“标识提供者登录页”字段。
     
       ![“SAML 设置”页。][13]
   * 在 NetSuite 中，选择“主要身份验证方法”。
   * 对于标有“SAMLV2 标识提供者元数据”的字段，请选择“上载 IDP 元数据文件”。 然后单击“浏览”上载在步骤 4 中下载的元数据文件。
     
       ![上载元数据][16]
   * 单击“提交”。
10. 在 Azure AD 中，选中单一登录配置确认复选框以启用上传到 NetSuite 的证书。 。
    
     ![选中确认复选框][14]
11. 如果想要接收与此单一登录配置维护相关的错误和警告电子邮件通知，请在对话框的最后一页上键入电子邮件地址。 
    
    ![键入电子邮件地址。][15]
12. 单击“完成”关闭对话框。 接下来，单击页面顶部的“属性”。
    
    ![单击“属性”。][17]
13. 单击“添加用户属性”。
    
    ![单击“添加用户属性”。][18]
14. 在“属性名称”字段中，键入 `account`。 在“属性值”字段中，键入 NetSuite 帐户 ID。 下面提供了有关如何找出帐户 ID 的说明：
    
    ![添加你的 NetSuite 帐户 ID。][19]
    
    * 在 NetSuite 的顶部导航菜单中单击“设置”。
    * 单击左侧导航菜单中的“设置任务”部分，选择“集成”部分，然后单击“Web 服务首选项”。
    * 复制 NetSuite 帐户 ID 并将它粘贴到 Azure AD 中的“属性值”字段。
      
        ![获取你的帐户 ID][20]
15. 在 Azure AD 中，单击“完成”以完成添加 SAML 属性。 然后单击底部菜单上的“应用更改”。
    
    ![保存所做更改。][21]
16. 在用户能够单一登录到 NetSuite 之前，必须先在 NetSuite 中为他们分配适当的权限。 请按以下说明来分配这些权限。
    
    * 在顶部导航菜单中单击“设置”，然后单击“设置管理员”。
      
        ![转到“设置管理器”][10]
    * 在左侧导航菜单中，选择“用户/角色”，然后单击“管理角色”。
      
        ![转到“管理角色”][22]
    * 单击“新建角色”。
    * 输入新角色的“名称”，然后选中“仅限单一登录”复选框。
      
        ![为新角色命名。][23]
    * 单击“保存” 。
    * 在顶部菜单中，单击“权限”。 然后单击“设置”。
      
        ![转到“权限”][24]
    * 选择“设置 SAM 单一登录”，然后单击“添加”。
    * 单击“保存” 。
    * 在顶部导航菜单中单击“设置”，然后单击“设置管理员”。
      
        ![转到“设置管理器”][10]
    * 在左侧导航菜单中，选择“用户/角色”，然后单击“管理用户”。
      
        ![转到“管理用户”][25]
    * 选择一个测试用户。 然后单击“编辑”。
      
        ![转到“管理用户”][26]
    * 在“角色”对话框中，选择创建的角色，然后单击“添加”。
      
        ![转到“管理用户”][27]
    * 单击“保存” 。
17. 若要测试配置，请参阅以下标题为[将用户分配到 NetSuite](#step-4-assign-users-to-netsuite) 的部分。

## <a name="step-3-enable-automated-user-provisioning"></a>步骤 3：启用自动化用户设置
> [!NOTE]
> 默认情况下，预配的用户将添加到 NetSuite 环境的根分支。
> 
> 

1. 在 Azure Active Directory 中 NetSuite 的“快速启动”页上，单击“配置用户预配”。
   
    ![配置用户设置][28]
2. 在打开的对话框中，键入 NetSuite 的管理员凭据，然后单击“下一步”。
   
    ![键入你的 NetSuite 管理员凭据。][29]
3. 在确认页上，键入用于接收预配失败通知的电子邮件地址。
   
    ![确认。][30]
4. 单击“完成”关闭对话框。

## <a name="step-4-assign-users-to-netsuite"></a>步骤 4：将用户分配到 NetSuite
1. 若要测试配置，首先在目录中创建新的测试帐户。
2. 在 NetSuite 的“快速启动”页上，单击“分配用户”按钮。
   
    ![单击“分配用户”][31]
3. 选择测试用户，然后单击屏幕底部的“分配”按钮：
   
   * 如果尚未启用自动化用户设置，则系统将提示你确认：
     
        ![确认分配。][32]
   * 如果已启用自动化用户预配，系统会提示你定义用户应在 NetSuite 中具有的角色类型。 新预配的用户应在几分钟后显示在 NetSuite 环境中。
4. 若要测试单一登录设置，请打开“访问面板”（网址为 [https://myapps.microsoft.com](https://myapps.microsoft.com/)），登录到测试帐户，然后单击“NetSuite”。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [有关如何集成 SaaS 应用的教程列表](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png



<!--HONumber=Nov16_HO4-->


