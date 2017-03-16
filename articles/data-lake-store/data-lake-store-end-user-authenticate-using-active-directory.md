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
ms.date: 03/02/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 0f6af54b351235390afa88f1ce156abd839a723f
ms.lasthandoff: 03/03/2017


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

本文讨论如何**创建 Azure AD 本机应用程序以进行最终用户身份验**。 有关服务到服务身份验证的 Azure AD 应用程序配置的说明，请参阅[使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* 订阅 ID。 可从 Azure 门户进行检索。 例如，Data Lake Store 帐户边栏选项卡中提供有此 ID。
  
    ![获取订阅 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD 域名。 可将鼠标悬停在 Azure 门户右上角进行检索。 在以下屏幕截图中，域名为 **contoso.onmicrosoft.com**，括号中的 GUID 是租户 ID。 
  
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
* Azure AD **本机应用程序**
* Azure AD 本机应用程序的客户端 ID
* Azure AD 本机应用程序的回复 URI
* 设置委派权限


## <a name="step-1-create-an-active-directory-web-application"></a>步骤 1：创建 Active Directory Web 应用程序

创建并配置用于通过使用 Azure Active Directory 的 Azure Data Lake Store 进行最终用户身份验证的应用程序。 有关说明，请参阅[创建 Azure AD 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

遵循以上链接中的说明进行操作时，请确保选择“本机”作为应用程序类型，如以下屏幕截图中所示。

![创建 Web 应用](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "创建本机应用")

## <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>步骤 2：获取客户端 ID、回复 URI，并设置委派权限

请参阅[获取客户端添加](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)，以检索 Azure AD 本机应用程序的客户端 ID（也称为应用程序 ID）。

若要检索重定向 URI，请按照以下步骤进行操作。

1. 从 Azure 门户中选择“Azure Active Directory”，单击“应用注册”，然后找到并单击刚刚创建的 Azure AD 本机应用程序。

2. 在应用程序的“设置”边栏选项卡上，单击“重定向 URI”。

    ![获取重定向 URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. 复制显示的值。


## <a name="step-3-set-permissions"></a>步骤 3：设置权限

1. 从 Azure 门户中选择“Azure Active Directory”，单击“应用注册”，然后找到并单击刚刚创建的 Azure AD 本机应用程序。

2. 在应用程序的“设置”边栏选项卡上，单击“所需权限”，然后单击“添加”。

    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. 在“添加 API 访问”边栏选项卡上，依次单击“选择 API”、“Azure Data Lake”和“选择”。

    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)
 
4.  在“添加 API 访问”边栏选项卡上，单击“选择权限”，选中复选框以给予“Data Lake Store 的完全访问权限”，然后单击“选择”。

    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    单击“Done”（完成） 。

5. 重复最后两步，同时为 **Windows Azure 服务管理 API** 授予权限。
   
## <a name="next-steps"></a>后续步骤
在本文中，创建了一个 Azure AD Web 应用程序，并使用 .NET SDK、Java SDK 等在创作的客户端应用程序中收集了所需的信息。现在可以转到以下文章，它们讨论了如何使用 Azure AD Web 应用程序先进行 Data Lake Store 身份验证，再在存储中执行其他操作。

* [Get started with Azure Data Lake Store using .NET SDK](data-lake-store-get-started-net-sdk.md)
* [通过 Java SDK 实现 Azure Data Lake Store 入门](data-lake-store-get-started-java-sdk.md)


