---
title: "教程：配置 Workday 以实现入站同步 | Microsoft Docs"
description: "了解如何使用入站同步与 Azure Active Directory 来启用单一登录、自动化设置及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8fe96f0a-f142-4d66-b53d-3ac3eb41a661
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 85375fc872794e50d40190e7be9013bccd3062a2
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-configure-workday-for-inbound-synchronization"></a>教程：配置 Workday 以实现入站同步

本教程旨在说明要将 Workday 中的人员导入到 Microsoft Azure AD 需要在 Workday 和 Microsoft Azure AD 中执行的步骤。    

>[!NOTE]
>在中国，使用 Azure AD 全球实例的客户可以使用 Azure Active Directory (AD) 高级版。 由中国的 21Vianet 运营的 Microsoft Azure 服务目前不支持 Azure AD 高级版。    
> 
> 

在本教程中概述的方案假定已具有以下各项：  

* 一个有效的 Azure 订阅  
* Workday 中的租户  

在本教程中概述的方案由以下构建基块组成：  

1. 为 Workday 启用应用程序集成  
2. 创建集成系统用户  
3. 添加安全组  
4. 将集成系统用户分配给安全组  
5. 配置安全组选项  
6. 激活安全策略更改  
7. 在 Microsoft Azure AD 中配置用户导入  

## <a name="enable-the-application-integration-for-workday"></a>为 Workday 启用应用程序集成
本部分旨在概述如何为 Salesforce 启用应用程序集成。    

**若要为 Workday 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Active Directory”。    
   
   ![Active Directory](./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  
2. 在“目录”列表中，选择要启用目录集成的目录。    
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。    
   
   ![应用程序](./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "应用程序")  
4. 若要打开**应用程序库**，请单击“添加应用”，然后单击“为组织添加要使用的应用程序”。    
   
   ![想要做什么？](./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "想要做什么？")  
5. 在搜索框中，键入“Workday”。    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Workday")  
6. 在结果窗格中，选择“Workday”，然后单击“完成”以添加该应用程序。    
   
   ![Workday](./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Workday")  

## <a name="create-an-integration-system-user"></a>创建集成系统用户
1. 在 **Workday 工作台**的搜索框中，输入“创建用户”，然后单击链接“创建集成系统用户”。     
   
   ![创建用户](./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "创建用户")  
2. 通过为新的集成系统用户提供用户名和密码来完成“创建集成系统用户”任务。  
 * 使“下次登录时需要新密码”选项处于未选中状态，因为此用户将以编程方式登录。    
 * 将“会话超时(分钟)”保留为其默认值 0，这将阻止用户会话过早超时。    
   
   ![创建集成系统用户](./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "创建集成系统用户")  

## <a name="create-a-security-group"></a>创建安全组
对于本教程中所述的方案，需要创建不受约束的集成系统安全组并为其分配用户。    

1. 在搜索框中输入“创建安全组”，然后单击链接“创建安全组”。     
   
   ![创建安全组](./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "创建安全组")  
2. 完成“创建安全组”任务。  从“租用安全组类型”下拉列表中选择“集成系统安全组(不受约束)”，以创建将显式添加成员的安全组。     
   
   ![创建安全组](./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "创建安全组")  

## <a name="assign-the-integration-system-user-to-the-security-group"></a>将集成系统用户分配给安全组
1. 在搜索框中输入“编辑安全组”，然后单击链接“编辑安全组”。     
   
   ![编辑安全组](./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "编辑安全组")  
2. 按名称搜索新的集成安全组并选中。    
   
   ![编辑安全组](./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "编辑安全组")  
3. 将新的集成系统用户添加到新的安全组。       
   
   ![系统安全组](./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "系统安全组")  

## <a name="configure-security-group-options"></a>配置安全组选项
在此步骤中，你将授予新安全组对受以下域安全策略保护的对象执行 Get 和 Put 操作的权限：  

* 外部帐户预配  
* 员工数据：公职人员报告  
* 员工数据：所有职位  
* 员工数据：当前人员配备信息  
* 员工数据：员工个人资料中的职衔  

1. 在搜索框中输入域安全策略，然后单击链接“功能区域的域安全策略”。     
   
   ![域安全策略](./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "域安全策略")  
2. 搜索“系统”并选择“系统功能区域”。  单击标有“确定”的按钮。     
   
   ![域安全策略](./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "域安全策略")  
3. 在系统功能区域的安全策略列表中，展开“安全管理”并选择域安全策略“外部帐户预配”。     
   
   ![域安全策略](./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "域安全策略")  
4. 单击“编辑权限”按钮，然后在“编辑权限”屏幕上将新安全组添加到具有 Get 和 Put 集成权限的安全组列表中。     
   
   ![编辑权限](./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "编辑权限")  
5. 重复执行上面的步骤 1，返回到用于选择功能区域的屏幕，这次搜索人员配备信息，选择“人员配备”功能区域，然后单击标有“确定”的按钮。    
   
   ![域安全策略](./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "域安全策略")  
6. 在“人员配备”功能区域的安全策略列表中，展开“员工数据: 人员配备”，并对剩余的每个安全策略重复执行上面的步骤 4：    
   
   * 员工数据：公职人员报告  
   * 员工数据：所有职位  
   * 员工数据：当前人员配备信息  
   * 员工数据：员工个人资料中的职衔    
   
   ![域安全策略](./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "域安全策略")  

## <a name="activate-security-policy-changes"></a>激活安全策略更改
1. 在搜索框中输入“激活”，然后单击链接“激活挂起的安全策略更改”。    
   
   ![激活](./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "激活")  
2. 通过输入用于审核目的的注释，然后单击标有“确定”的按钮，开始“激活挂起的安全策略更改”任务。      
   
   ![激活挂起的安全性](./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "激活挂起的安全性")  
3. 通过选中标有“确认”的复选框并单击标有“确定”的按钮，完成下一屏幕上的任务。     
   
   ![激活挂起的安全性](./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "激活挂起的安全性")  

## <a name="configure-user-import-in-microsoft-azure-ad"></a>在 Microsoft Azure AD 中配置用户导入
本部分旨在概述 Microsoft Azure AD 如何从 Workday 导入人员。    

**若要在 Microsoft Azure AD 中配置用户导入，请执行以下步骤：**

1. 在“Workday”应用程序集成页上，单击“配置用户导入”，打开“配置预配”对话框。    
2. 在“设置和管理员凭据”页上，执行以下步骤，然后单击“下一步”：    
   
   ![设置和管理员凭据](./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "设置和管理员凭据")    
   
 * 在“Workday 管理员用户名”文本框中，键入在[创建集成系统用户](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser)部分中创建的用户的名称。    
 * 在“Workday 管理员密码”文本框中，键入在[创建集成系统用户](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser)部分中创建的用户的密码。    
 * 在“Workday 租户 URL”文本框中，键入 URL 或 Workday 租户。    
3. 在“测试连接”页上，单击“开始测试”以确认连接，然后单击“下一步”。    
   
   ![测试连接](./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "测试连接")  
4. 在“预配选项”页上，单击“下一步”。    
   
   ![预配选项](./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "预配选项")  
5. 在“开始预配”对话框中，单击“完成”。    
   
   ![开始预配](./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "开始预配")  

现在可以转到“用户”部分，检查 Workday 用户是否已导入。    


