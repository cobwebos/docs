---
title: "教程：为入站同步配置 Workday | Microsoft Docs"
description: "了解如何将 Workday 用作 Azure Active Directory 的标识数据源。"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a7bf2a8d093b5d314fdd08f5012f0517a45e3579


---
# <a name="tutorial-configuring-workday-for-inbound-synchronization"></a>教程：为入站同步配置 Workday
本教程旨在说明将 Workday 中的人员导入到 Azure AD 时需要在 Workday 和 Azure AD 中执行的步骤。 

在本教程中概述的方案假定您已具有以下各项：

* 有效 Azure AD Premium 订阅
* Workday 中的租户

在本教程中概述的方案由以下构建基块组成：

1. 为 Workday 启用应用程序集成 
2. 创建集成系统用户 
3. 添加安全组 
4. 将集成系统用户分配给安全组 
5. 配置安全组选项 
6. 激活安全策略更改 
7. 在 Azure AD 中配置用户导入 

## <a name="enabling-the-application-integration-for-workday"></a>为 Workday 启用应用程序集成
本部分旨在概述如何启用 Workday 的应用程序集成。

### <a name="steps"></a>步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Add application")
5. 在搜索框中，键入 **Workday**。
   
    ![从库中添加应用程序](./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Add an application from gallerry")
6. 在结果窗格中，选择“Workday”，然后单击“完成”以添加该应用程序。
   
    ![应用程序库](./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Application gallery")

## <a name="creating-an-integration-system-user"></a>创建集成系统用户
### <a name="steps"></a>步骤：
1. 在“Workday 工作台”的搜索框中，输入“创建用户”，然后单击“创建集成系统用户”。 
   
    ![创建用户](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Create user")
2. 通过为新的集成系统用户提供用户名和密码，完成“创建集成系统用户”任务。  使“下次登录时需要新密码”选项处于未选中状态，因为此用户将以编程方式登录。 将“会话超时(分钟)”保留为其默认值 0，这将阻止用户会话过早超时。 
   
    ![创建集成系统用户](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Create Integration System User")

## <a name="creating-a-security-group"></a>添加安全组
对于本教程中所述的方案，需要创建不受约束的集成系统安全组并为其分配用户。

### <a name="steps"></a>步骤：
1. 在搜索框中输入“创建安全组”，然后单击“创建安全组”。 
   
    ![创建安全组](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity Group")
2. 完成“创建安全组”任务。  从“租用安全组类型”下拉列表中选择“集成系统安全组(不受约束)”，以创建将显式添加成员的安全组。 
   
    ![创建安全组](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity Group")

## <a name="assigning-the-integration-system-user-to-the-security-group"></a>将集成系统用户分配给安全组
### <a name="steps"></a>步骤：
1. 在搜索框中输入“编辑安全组”，然后单击“编辑安全组”。 
   
    ![编辑安全组](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Edit Security Group")
2. 按名称搜索新的集成安全组并选中。 
   
    ![编辑安全组](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Edit Security Group")
3. 将新的集成系统用户添加到新的安全组。 
   
    ![系统安全组](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "System Security Group")  

## <a name="configuring-security-group-options"></a>配置安全组选项
在此步骤中，将授予新安全组对对象执行 **Get** 和 **Put** 操作的权限，对象受以下域安全策略保护：

* 外部帐户预配
* 员工数据：公职人员报告
* 员工数据：所有职位
* 员工数据：当前人员配备信息
* 员工数据：员工个人资料中的职衔

### <a name="steps"></a>步骤：
1. 在搜索框中输入域安全策略，然后单击链接“功能区域的域安全策略”。  
   
    ![域安全策略](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Domain Security Policies")  
2. 搜索“系统”并选择“系统”功能区域。  单击 **“确定”**。  
   
    ![域安全策略](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Domain Security Policies")  
3. 在系统功能区域的安全策略列表中，展开“安全管理”并选择域安全策略“外部帐户预配”。  
   
    ![域安全策略](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Domain Security Policies")  
4. 单击“编辑权限”，然后在“编辑权限”对话框页上将新安全组添加到具有 **Get** 和 **Put** 集成权限的安全组列表中。 
   
    ![编辑权限](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Edit Permission")  
5. 重复上方的步骤 1，返回到用于选择功能区域的屏幕，这次搜索人员配备，选择“人员配备”功能区域，然后单击“确定”。
   
    ![域安全策略](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Domain Security Policies")  
6. 在“人员配备”功能区域的安全策略列表中，展开“员工数据: 人员配备”，并对剩余的每个安全策略重复执行上面的步骤 4：
   
   * 员工数据：公职人员报告
   * 员工数据：所有职位
   * 员工数据：当前人员配备信息
   * 员工数据：员工个人资料中的职衔

    ![域安全策略](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Domain Security Policies")  







## <a name="activating-security-policy-changes"></a>激活安全策略更改
### <a name="steps"></a>步骤：
1. 在搜索框中输入“激活”，然后单击链接“激活挂起的安全策略更改”。 
   
    ![激活](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activate") 
2. 通过输入用于审核的注释，然后单击“确定”，开始“激活挂起的安全策略更改”任务。 
   
    ![激活挂起的安全性](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activate Pending Security")   
3. 选中标有“确定”的复选框，然后单击“确定”按钮，完成下一屏幕上的任务。 
   
    ![激活挂起的安全性](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activate Pending Security")  

## <a name="configuring-user-import-in-azure-ad"></a>在 Azure AD 中配置用户导入
本部分旨在概述如何配置 Azure AD 从 Workday 导入人员。

### <a name="steps"></a>步骤：
1. 在“Workday”应用程序集成页上，单击“配置用户导入”，打开“配置预配”对话框。
2. 在“设置和管理员凭据”页上，执行以下步骤，然后单击“下一步”： 
   
    ![设置和管理员凭据](./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Settings and admin credentials")  
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Workday 管理员用户名”文本框中，键入在“创建集成系统用户”部分创建的用户的名称。
   
    b. 在“Workday 管理员密码”文本框中，键入在“创建集成系统用户”部分创建的用户的密码。
   
    c. 在“Workday 租户 URL”文本框中，键入 URL 或 Workday 租户。
3. 在“测试连接”页上，单击“开始测试”以确认连接，然后单击“下一步”。 
   
    ![测试连接](./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Test connection")  
4. 在“预配”选项页上，单击“下一步”。 
   
    ![预配选项](./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Provisioning options")
5. 在“开始预配”对话框中，单击“完成”。 
   
    ![开始预配](./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Start provisioning")

现在可以转到“用户”部分，检查 Workday 用户是否已导入。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


