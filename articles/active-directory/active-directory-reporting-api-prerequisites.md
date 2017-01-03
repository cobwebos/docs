---
title: "访问 Azure AD 报告 API 的先决条件。 | Microsoft 文档"
description: "了解有关访问 Azure AD 报告 API 的先决条件"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0f23cc578fb946247d492b9dee92cf7a9daafa1


---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>访问 Azure AD 报告 API 的先决条件
[Azure AD 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API，可向你提供对该数据的编程访问权限。 可从各种编程语言和工具中调用这些 API。

报告 API 使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授权访问 Web API。 

若要准备访问报告 API，你必须执行以下操作：

1. 在你的 Azure AD 租户中创建应用程序 
2. 授予应用程序相应权限来访问 Azure AD 数据
3. 从你的目录中收集配置设置

有关疑问、问题或反馈，请联系 [AAD 报告帮助](mailto:aadreportinghelp@microsoft.com)。

## <a name="create-an-azure-ad-application"></a>创建 Azure AD 应用程序
若要配置你的目录以访问 Azure AD 报告 API，必须使用同为 Azure AD 租户中“全局管理员”目录角色成员的 Azure 订阅管理员帐户登录到 Azure 经典门户。

> [!IMPORTANT]
> 在具有此类“管理”特权的凭据下运行的应用程序可以具有非常强大的功能，因此请务必保证应用程序 ID/机密凭据的安全。
> 
> 

1. 在 [Azure 经典门户](https://manage.windowsazure.com)的左侧导航窗格上，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 从“Active Directory”列表中，选择你的目录。
3. 在顶部菜单中，单击“应用程序”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/02.png) 
4. 在底部栏上，单击“添加”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/03.png) 
5. 在“要执行什么操作？”对话框上，单击“添加我的组织正在开发的应用程序”。 
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/04.png) 
6. 在“向我们说明你的应用程序”对话框中，执行以下步骤： 
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入一个名称（例如：报告 API 应用程序）。
   
    b. 选择“Web 应用程序和/或 Web API”。
   
    c. 单击“下一步”。
7. 在“应用属性”对话框中，执行以下步骤： 
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，键入 `https://localhost`。
   
    b. 在“应用 ID URI”文本框中，键入 ```https://localhost/ReportingApiApp```。
   
    c. 单击“完成”。

## <a name="grant-your-application-permission-to-use-the-api"></a>授予应用程序使用 API 的权限
1. 在 [Azure 经典门户](https://manage.windowsazure.com/)的左侧导航窗格上，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 从“Active Directory”列表中，选择你的目录。
3. 在顶部菜单中，单击“应用程序”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/02.png)
4. 在应用程序列表中，选择新创建的应用程序。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/07.png)
5. 在顶部菜单中，单击“配置”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/08.png)
6. 在“其他应用程序权限”部分中，针对“Azure Active Directory”资源，单击“应用程序权限”下拉列表，然后选择“读取目录数据”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/09.png)
7. 在底部栏上，单击“保存”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/10.png)

## <a name="gather-configuration-settings-from-your-directory"></a>从你的目录中收集配置设置
本部分介绍了如何从你的目录中获取以下设置：

* 域名
* 客户端 ID
* 客户端机密

在配置对报告 API 的调用时需要这些值。 

### <a name="get-your-domain-name"></a>获取域名
1. 在 [Azure 经典门户](https://manage.windowsazure.com)的左侧导航窗格上，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 从“Active Directory”列表中，选择你的目录。
3. 在顶部菜单中，单击“域”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/11.png) 
4. 在“域名”列中，复制你的域名。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/12.png) 

### <a name="get-the-applications-client-id"></a>获取应用程序的客户端 ID
1. 在 [Azure 经典门户](https://manage.windowsazure.com)的左侧导航窗格上，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 从“Active Directory”列表中，选择你的目录。
3. 在顶部菜单中，单击“应用程序”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/02.png) 
4. 在应用程序列表中，选择新创建的应用程序。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/07.png)
5. 在顶部菜单中，单击“配置”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/08.png)
6. 复制你的“客户端 ID”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/13.png)

### <a name="get-the-applications-client-secret"></a>获取应用程序的客户端密码
若要获取应用程序的客户端密码，需要创建新密钥并在保存新密钥时保存其值，因为它在以后无法再检索此值。

1. 在 [Azure 经典门户](https://manage.windowsazure.com)的左侧导航窗格上，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/01.png) 
2. 从“Active Directory”列表中，选择你的目录。
3. 在顶部菜单中，单击“应用程序”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/02.png) 
4. 在应用程序列表中，选择新创建的应用程序。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/07.png)
5. 在顶部菜单中，单击“配置”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/08.png)
6. 在“密钥”部分中，执行以下步骤： 
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/14.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 从持续时间列表中，选择持续时间
   
    b. 在底部栏上，单击“保存”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites/10.png)
   
    c. 复制密钥值。

## <a name="next-steps"></a>后续步骤
* 是否要以编程方式从 Azure AD 报告 API 中访问数据？ 请查看 [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)。
* 如果你要查找有关 Azure Active Directory 报告的详细信息，请参阅 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)。  




<!--HONumber=Dec16_HO5-->


