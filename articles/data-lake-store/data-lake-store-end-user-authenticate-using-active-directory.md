---
title: "使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证 | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ec586ecd-1b42-459e-b600-fadbb7b80a9b
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: a50fc687a1738a55c3d22eb3e12060397c162e06


---
# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证
> [!div class="op_single_selector"]
> * [服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)
> * [最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store 使用 Azure Active Directory 进行身份验证。 编写用于 Azure Data Lake Store 或 Azure Data Lake Analytics 的应用程序之前，必须首先决定使用 Azure Active Directory (Azure AD) 对应用程序进行身份验证的方式。 可用的两个主要选项是：

* 最终用户身份验证（本文所述）
* 服务到服务身份验证

这两个选项都会将 OAuth 2.0 令牌提供给应用程序，此令牌会附加到对 Azure Data Lake Store 或 Azure Data Lake Analytics 作出的每个请求。

本文讨论如何为最终用户身份验证创建 Azure AD Web 应用程序。 有关服务到服务身份验证的 Azure AD 应用程序配置的说明，请参阅[使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 订阅 ID。 可从 Azure 门户进行检索。 例如，Data Lake Store 帐户边栏选项卡中提供有此 ID。
  
    ![获取订阅 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)
* Azure AD 域名。 可将鼠标悬停在 Azure 门户右上角进行检索。 在屏幕截图下方，域名为 **contoso.microsoft.com**，括号中的 GUID 是租户 ID。 
  
    ![获取 AAD 域](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>最终用户身份验证
若希望最终用户通过 Azure AD 登录到你的应用，则推荐此方式。 你的应用程序可访问 Azure 资源，具有与已登录的用户相同的访问权限级别。 你的最终用户需要定期提供凭据，以使你的应用程序可继续访问。

让最终用户登录的结果是会向应用程序授予访问令牌和刷新令牌。 访问令牌会附加到对 Data Lake Store 或 Data Lake Analytics 作出的每个请求，默认情况下一小时内有效。 刷新令牌可用于获取新的访问令牌，并且如果定期使用，默认情况下两周内有效。 可使用两种不同的最终用户登录方式。

### <a name="using-the-oauth-20-pop-up"></a>使用 OAuth 2.0 弹出窗口
应用程序会触发 OAuth 2.0 身份验证弹出窗口，最终用户可在该弹出窗口中输入凭据。 如果需要，此弹出窗口也适用于 Azure AD 双重身份验证 (2FA) 过程。 

> [!NOTE]
> 此方法在 Azure AD 身份验证库 (ADAL) 中尚不支持 Python 或 Java。
> 
> 

### <a name="directly-passing-in-user-credentials"></a>直接传递用户凭据
应用程序可直接向 Azure AD 提供用户凭据。 此方法仅适用于组织 ID 用户帐户，不适用于个人/“实时 ID”用户帐户，包括那些以 @outlook.com 或 @live.com 结尾的用户帐户。 此外，此方法不适用于需要 Azure AD 双因素身份验证 (2FA) 的用户帐户。

### <a name="what-do-i-need-to-use-this-approach"></a>使用此方式需要什么？
* Azure AD 域名。 已在本文的先决条件中列出。
* Azure AD **Web 应用程序**
* Azure AD Web 应用程序的客户端 ID
* Azure AD Web 应用程序的回复 URI
* 设置委派权限

有关如何创建 Azure AD Web 应用程序、如何按上述要求对其进行配置的说明，请参阅下方的[创建 Active Directory 应用程序](#create-an-active-directory-application)部分。 

## <a name="create-an-active-directory-application"></a>创建 Active Directory 应用程序
本部分介绍如何使用 Azure Active Directory，针对通过 Azure Data Lake Store 进行最终用户身份验证，创建和配置 Azure AD Web 应用程序。

### <a name="step-1-create-an-azure-active-directory-application"></a>步骤 1：创建 Azure Active Directory 应用程序
> [!NOTE]
> 以下步骤使用 Azure 门户。 还可使用 [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md) 或 [Azure CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md).来创建 Azure AD 应用程序。
> 
> 

1. 通过[经典门户](https://manage.windowsazure.com/)登录到 Azure 帐户。
2. 在左侧窗格中选择“**Active Directory**”。
   
     ![选择“Active Directory”](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
3. 选择要用于创建新应用程序的 Active Directory。 如果有多个 Active Directory，通常需要在订阅所在目录中创建应用程序。 只能为与订阅在同一目录中的应用程序授予对订阅中资源的访问权限。  
   
     ![选择目录](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
4. 若要查看目录中的应用程序，请单击“**应用程序”**。
   
     ![查看应用程序](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)
5. 如果你之前尚未在该目录中创建应用程序，则应该会看到与下面类似的图像。 单击“添加应用程序”
   
     ![添加应用程序](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)
   
     或者，单击底部窗格中的“添加”。
   
     ![添加](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)
6. 提供应用程序的名称，并选择要创建的应用程序的类型。 对于本教程，请创建“WEB 应用程序和/或 WEB API”，然后单击“下一步”按钮。
   
     ![命名应用程序](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)
7. 填写应用程序的属性。 对于“登录 URL”，请提供用于描述应用程序的网站 URI。 将不验证该网站是否存在。 
   对于“应用 ID URI”，请提供用于标识应用程序的 URI。
   
     ![应用程序属性](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)
   
    单击复选标记以完成向导，并创建应用程序。

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>步骤 2：获取客户端 ID、回复 URI，并设置委派权限
1. 单击“配置”选项卡以配置应用程序的密码。
   
     ![配置应用程序](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)
2. 复制**客户端 ID**。
   
     ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)
3. 在“单一登录”部分的下方，复制**回复 URI**。
   
    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)
4. 在“针对其他应用程序的权限”中，单击“添加应用程序”
   
    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)
5. 在“针对其他应用程序的权限”向导中，选择“Azure Data Lake”和“Windows Azure 服务管理 API”，然后单击复选标记。
6. 默认情况下，新添加的服务的“委派权限”设置为零。 单击 Azure Data Lake 和 Windows Azure 管理服务的“委派权限”下拉列表，并将可用的复选框的值设为 1。 结果应如下所示。
   
     ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
7. 单击“保存” 。

## <a name="next-steps"></a>后续步骤
在本文中，创建了一个 Azure AD Web 应用程序，并使用 .NET SDK、Java SDK 等在创作的客户端应用程序中收集了所需的信息。现在可以转到以下文章，它们讨论了如何使用 Azure AD Web 应用程序先进行 Data Lake Store 身份验证，再在存储中执行其他操作。

* [Get started with Azure Data Lake Store using .NET SDK](data-lake-store-get-started-net-sdk.md)
* [通过 Java SDK 实现 Azure Data Lake Store 入门](data-lake-store-get-started-java-sdk.md)




<!--HONumber=Jan17_HO4-->


