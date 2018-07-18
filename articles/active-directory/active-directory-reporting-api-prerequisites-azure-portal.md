---
title: 访问 Azure Active Directory 报告 API 的先决条件 | Microsoft 文档
description: 了解有关访问 Azure AD 报告 API 的先决条件
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 6d842b1af74c1b276f367e0ff15703880f7560aa
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224780"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>访问 Azure Active Directory 报告 API 的先决条件

[Azure Active Directory (Azure AD) 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。

报告 API 使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授权访问 Web API。

若要准备访问报告 API，需要执行以下操作：

1. 分配角色
2. 注册应用程序
3. 授予权限
4. 收集配置设置



## <a name="assign-roles"></a>分配角色

若要通过 API 访问报告数据，需分配以下角色之一：

- 安全读取者

- 安全管理员

- 全局管理员




## <a name="register-an-application"></a>注册应用程序

即使是在使用脚本来访问报告 API，也需要注册一个应用。 该操作会提供一个**应用程序 ID**，该 ID 是进行授权调用所必需的，代码也通过该 ID 来接收令牌。

若要配置目录以访问 Azure AD 报告 API，必须使用同为 Azure AD 租户中“全局管理员”目录角色成员的 Azure 管理员帐户登录到 Azure 门户。

> [!IMPORTANT]
> 在具有此类“管理”特权的凭据下运行的应用程序可以具有非常强大的功能，因此请务必保证应用程序 ID/机密凭据的安全。
> 


**若要注册 Azure Active Directory 应用程序，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“Azure Active Directory”页中，单击“应用注册”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. 在“应用注册”页顶部的工具栏中，单击“新建应用程序注册”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. 在“创建”页中，执行以下步骤：

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. 在“名称”文本框中，键入 `Reporting API application`。

    b. 选择“Web 应用/API”作为应用程序类型。

    c. 在“登录 URL”文本框中，键入 `https://localhost`。

    d. 单击“创建”。 


## <a name="grant-permissions"></a>授予权限 

根据想要访问的 API，你需要向应用授予以下权限：  

| API | 权限 |
| --- | --- |
| Microsoft Azure Active Directory | 读取目录数据 |
| Microsoft Graph | 读取所有审核日志数据 |


![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


以下部分列出适用于这两个 API 的步骤。 如果你不想访问其中一个 API，可以跳过相关步骤。
 

**若要授予应用程序使用 API 的权限，请执行以下操作：**

1. 在“应用注册”页的应用列表中，单击“报告 API 应用程序”。

2. 在“报告 API 应用程序”页顶部的工具栏中，单击“设置”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. 在“设置”页中，单击“所需权限”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. 在“所需的权限”页的“API”列表中，单击“Windows Azure Active Directory”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. 在“启用访问”页上，选择“读取目录数据”并取消选择“登录并读取用户个人资料”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. 在顶部工具栏中，单击“保存”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. 在“所需权限”页顶部的工具栏中，单击“添加”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. 在“添加 API 访问权限”页中，单击“选择 API”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. 在“选择 API”页上，单击“Microsoft Graph”，然后单击“选择”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. 在“启用访问”页上，选择“读取所有审核日志数据”，然后单击“选择”。  

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. 在“添加 API 访问权限”页中，单击“完成”。  

12. 在“所需权限”页顶部的工具栏中， 单击“授予权限”，然后单击“是”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>收集配置设置 

本部分介绍了如何从目录中获取以下设置：

- 域名
- 客户端 ID
- 客户端机密

在配置对报告 API 的调用时需要这些值。 

### <a name="get-your-domain-name"></a>获取域名

**若要获取域名，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“Azure Active Directory”页上，单击“自定义域名”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. 从域列表中复制域名。


### <a name="get-your-applications-client-id"></a>获取应用程序的客户端 ID

**若要获取应用程序的客户端 ID，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“应用注册”页的应用列表中，单击“报告 API 应用程序”。

3. 在“报告 API 应用程序”页中，指向“应用程序 ID”，并单击“单击复制”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>获取应用程序的客户端机密
若要获取应用程序的客户端密码，需要创建新密钥并在保存新密钥时保存其值，因为它在以后无法再检索此值。

**若要获取应用程序的客户端机密，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“应用注册”页的应用列表中，单击“报告 API 应用程序”。


3. 在“报告 API 应用程序”页顶部的工具栏中，单击“设置”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. 在“设置”页的“APIR 访问”部分，单击“密钥”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. 在“密钥”页中执行以下步骤：

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. 在“说明”文本框中，键入 `Reporting API`。

    b. 选择“2 年内”作为“过期时间”。

    c. 单击“ **保存**”。

    d. 复制密钥值。


## <a name="next-steps"></a>后续步骤

- [使用具有证书的 Azure Active Directory 报告 API 获取数据](active-directory-reporting-api-with-certificates.md)。

- [获取报告 API 的第一印象](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [创建自己的解决方案](active-directory-reporting-api-getting-started-azure-portal.md#customize)

