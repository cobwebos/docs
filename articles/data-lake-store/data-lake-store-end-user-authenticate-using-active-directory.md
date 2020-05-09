---
title: 最终用户身份验证-Data Lake Storage Gen1 与 Azure AD
description: 了解如何使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 最终用户身份验证
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: 5a0c3e1df5cd283ad08f905ed0bd4f329dcfcc7e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688240"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>使用 Azure Active Directory 进行 Azure Data Lake Storage Gen1 最终身份验证
> [!div class="op_single_selector"]
> * [最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage Gen1 使用 Azure Active Directory 进行身份验证。 编写用于 Data Lake Storage Gen1 或 Azure Data Lake Analytics 的应用程序之前，必须首先决定使用 Azure Active Directory (Azure AD) 对应用程序进行身份验证的方式。 可用的两个主要选项是：

* 最终用户身份验证（本文所述）
* 服务到服务身份验证（从上面的下拉列表中选择此选项）

这两个选项都会将 OAuth 2.0 令牌提供给应用程序，此令牌会附加到对 Data Lake Storage Gen1 或 Azure Data Lake Analytics 作出的每个请求。

本文讨论如何创建 Azure AD 本机应用程序以进行最终用户身份验****。 有关服务到服务身份验证的 Azure AD 应用程序配置的说明，请参阅[使用 Azure Active Directory 进行 Data Lake Storage Gen1 服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* 订阅 ID。 可从 Azure 门户进行检索。 例如，Data Lake Storage Gen1 帐户边栏选项卡中提供有此 ID。

    ![获取订阅 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* Azure AD 域名。 可将鼠标悬停在 Azure 门户右上角进行检索。 在以下屏幕截图中，域名为 contoso.onmicrosoft.com，括号中的 GUID 是租户 ID****。

    ![获取 AAD 域](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* Azure 租户 ID。 有关如何检索租户 ID 的说明，请参阅[获取租户 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)。

## <a name="end-user-authentication"></a>最终用户身份验证
如果希望最终用户通过 Azure AD 登录到您的应用程序，则建议使用这种身份验证机制。 然后，应用程序可访问 Azure 资源，且访问权限级别与已登录的最终用户相同。 最终用户需要定期提供凭据，以使应用程序可继续访问。

最终用户登录的结果是，为你的应用程序提供了一个访问令牌和一个刷新令牌。 访问令牌会附加到对 Data Lake Storage Gen1 或 Data Lake Analytics 作出的每个请求，默认情况下一小时内有效。 刷新令牌可用于获取新的访问令牌，默认情况下两周内有效。 对于最终用户登录，可以使用两种不同的方法。

### <a name="using-the-oauth-20-pop-up"></a>使用 OAuth 2.0 弹出窗口
应用程序会触发 OAuth 2.0 身份验证弹出窗口，最终用户可在其中输入凭据。 如有必要，此弹出窗口也适用于 Azure AD 双因素身份验证 (2FA) 过程。

> [!NOTE]
> 此方法在 Azure AD 身份验证库 (ADAL) 中尚不支持 Python 或 Java。
>
>

### <a name="directly-passing-in-user-credentials"></a>直接传递用户凭据
应用程序可直接向 Azure AD 提供用户凭据。 此方法仅适用于组织 ID 用户帐户，不适用于个人/“实时 ID”用户帐户，包括以 @outlook.com 或 @live.com 结尾的用户帐户。 此外，此方法不适用于需要 Azure AD 双因素身份验证 (2FA) 的用户帐户。

### <a name="what-do-i-need-for-this-approach"></a>此方法需要什么？
* Azure AD 域名。 此要求已在本文的先决条件中列出。
* Azure AD 租户 ID。 此要求已在本文的先决条件中列出。
* Azure AD 本机应用程序****
* Azure AD 本机应用程序的应用程序 ID
* Azure AD 本机应用程序的重定向 URI
* 设置委派权限


## <a name="step-1-create-an-active-directory-native-application"></a>步骤 1：创建 Active Directory 本机应用程序

创建并配置 Azure AD 本机应用程序，用于使用 Azure Active Directory 进行 Data Lake Storage Gen1 最终用户身份验证。 有关说明，请参阅[创建 Azure AD 应用程序](../active-directory/develop/howto-create-service-principal-portal.md)。

遵循链接中的说明进行操作时，请确保选择“本机”作为应用程序类型，如以下屏幕截图中所示****：

![创建 web 应用](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "创建本机应用")

## <a name="step-2-get-application-id-and-redirect-uri"></a>步骤 2：获取应用程序 ID 和重定向 URI

请参阅[获取应用程序 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in) 来检索应用程序 ID。

若要检索重定向 URI，请执行以下步骤。

1. 从 Azure 门户中选择“Azure Active Directory”，单击“应用注册”，找到并单击已创建的 Azure AD 本机应用程序********。

2. 在应用程序的“设置”边栏选项卡上，单击“重定向 URI”********。

    ![获取重定向 URI](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. 复制显示的值。


## <a name="step-3-set-permissions"></a>步骤 3：设置权限

1. 从 Azure 门户中选择“Azure Active Directory”，单击“应用注册”，找到并单击已创建的 Azure AD 本机应用程序********。

2. 在应用程序的“设置”边栏选项卡上，单击“所需权限”，并单击“添加”************。

    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. 在“添加 API 访问”边栏选项卡上，依次单击“选择 API”、“Azure Data Lake”和“选择”****************。

    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  在“添加 API 访问”边栏选项卡上，单击“选择权限”，选中复选框以给予“Data Lake Store 的完全访问权限”，并单击“选择”****************。

    ![客户端 ID](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    单击“Done”（完成）  。

5. 重复最后两步，同时为 Windows Azure 服务管理 API 授予权限****。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用 .NET SDK、Java SDK、REST API 等在你创作的客户端应用程序中创建了一个 Azure AD 的本机应用程序并收集了所需的信息。你现在可以继续阅读以下文章，这些文章讨论了如何使用 Azure AD web 应用程序首先使用 Data Lake Storage Gen1 进行身份验证，然后在存储中执行其他操作。

* [使用 Java SDK 进行 Data Lake Storage Gen1 最终用户身份验证](data-lake-store-end-user-authenticate-java-sdk.md)
* [使用 .NET SDK 进行 Data Lake Storage Gen1 最终用户身份验证](data-lake-store-end-user-authenticate-net-sdk.md)
* [使用 Python 进行 Data Lake Storage Gen1 最终用户身份验证](data-lake-store-end-user-authenticate-python.md)
* [使用 REST API 进行 Data Lake Storage Gen1 最终用户身份验证](data-lake-store-end-user-authenticate-rest-api.md)
