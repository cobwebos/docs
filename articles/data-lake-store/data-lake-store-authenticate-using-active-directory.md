---
title: "使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证 | Microsoft Docs"
description: "了解如何使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 820b7c5d-4863-4225-9bd1-df4d8f515537
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 1d712ef6987a4af2014bedb54378f288bcf535a8
ms.lasthandoff: 04/22/2017


---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证
> [!div class="op_single_selector"]
> * [服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)
> * [最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)
> 
> 

Azure Data Lake Store 使用 Azure Active Directory 进行身份验证。 编写用于 Azure Data Lake Store 或 Azure Data Lake Analytics 的应用程序之前，必须首先决定使用 Azure Active Directory (Azure AD) 对应用程序进行身份验证的方式。 可用的两个主要选项是：

* 最终用户身份验证 
* 服务到服务身份验证（本文所述） 

这两个选项都会将 OAuth 2.0 令牌提供给应用程序，此令牌会附加到对 Azure Data Lake Store 或 Azure Data Lake Analytics 作出的每个请求。

本文讨论如何**为服务到服务身份验证创建 Azure AD Web 应用程序**。 有关最终用户身份验证的 Azure AD 应用程序配置的说明，请参阅[使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="step-1-create-an-active-directory-web-application"></a>步骤 1：创建 Active Directory Web 应用程序

使用 Azure Active Directory，针对通过 Azure Data Lake Store 进行服务到服务身份验证，创建和配置 Azure AD Web 应用程序。 有关说明，请参阅[创建 Azure AD 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

遵循以上链接的说明时，请确保为应用程序类型选择“Web 应用/API”，如以下屏幕截图所示。

![创建 Web 应用](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "创建 Web 应用")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>步骤 2：获取应用程序 ID、身份验证密钥和租户 ID
以编程方式登录时，需要应用程序的 ID。 如果应用程序在其自己的凭据下运行，则还需要身份验证密钥。

* 若要了解如何检索应用程序的应用程序 ID 和身份验证密钥（也称为客户端密码），请参阅[获取应用程序 ID 和身份验证密钥](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)。

* 有关如何检索租户 ID 的说明，请参阅[获取租户 ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)。

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>步骤 3：将 Azure AD 应用程序分配给 Azure Data Lake Store 帐户文件或文件夹（仅适用于服务到服务身份验证）
1. 登录新的 [Azure 门户](https://portal.azure.com)，打开要与之前创建的 Azure Active Directory 应用程序相关联的 Azure Data Lake Store 帐户。
2. 在 Data Lake Store 帐户边栏选项卡中，单击“数据资源管理器” 。
   
    ![在 Data Lake Store 帐户中创建目录](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "在 Data Lake 帐户中创建目")
3. 在“数据资源管理器”边栏选项卡中，单击要为其提供 Azure AD 应用程序访问权限的文件或文件夹，然后单击“访问”。 若要配置对文件的访问，必须在“文件预览”边栏选项卡中单击“访问”。
   
    ![对 Data Lake 文件系统设置 ACL](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "对 Data Lake 文件系统设置 ACL")
4. “访问”边栏选项卡会列出已分配给根的标准访问和自定义访问。 单击“添加”图标添加自定义级别的 ACL。
   
    ![列出标准及自定义访问权限](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "列出标准及自定义访问权限")
5. 单击“添加”图标打开“添加自定义访问”边栏选项卡。 在此边栏选项卡中，单击“选择用户或组”，然后在“选择用户或组”边栏选项卡中，查找之前创建的 Azure Active Directory 应用程序。 如果搜索范围中存在大量的组，请使用顶部的文本框筛选组名称。 单击要添加的组，然后单击“选择”。
   
    ![添加组](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "添加组")
6. 单击“选择权限”，选择权限以及是将这些权限分配为默认 ACL、访问 ACL 还是同时分配为这两类。 单击 **“确定”**。
   
    ![分配权限给组](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "分配权限给组")
   
    有关 Data Lake Store 中的权限和默认/访问 ACL 的详细信息，请参阅 [Data Lake Store 中的访问控制](data-lake-store-access-control.md)。
7. 在“添加自定义访问”边栏选项卡中，单击“确定”。 “访问”边栏选项卡中会列出新添加的组以及相关的权限。
   
    ![分配权限给组](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "分配权限给组")

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>步骤 4：获取 OAuth 2.0 令牌终结点（仅适用于基于 Java 的应用程序）

1. 登录新的 [Azure 门户](https://portal.azure.com)，在左侧窗格中单击“Active Directory”。

2. 在左侧窗格中，单击“应用注册”。

3. 在“应用注册”边栏选项卡顶部，单击“终结点”。

    ![OAuth 令牌终结点](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth 令牌终结点")

4. 从终结点列表中，复制 OAuth 2.0 令牌终结点。

    ![OAuth 令牌终结点](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth 令牌终结点")   

## <a name="next-steps"></a>后续步骤
在本文中，创建了一个 Azure AD Web 应用程序，并使用 .NET SDK、Java SDK 等在创作的客户端应用程序中收集了所需的信息。现在可以转到以下文章，它们讨论了如何使用 Azure AD Web 应用程序先进行 Data Lake Store 身份验证，再在存储中执行其他操作。

* [Get started with Azure Data Lake Store using .NET SDK](data-lake-store-get-started-net-sdk.md)
* [通过 Java SDK 实现 Azure Data Lake Store 入门](data-lake-store-get-started-java-sdk.md)

本文介绍了让应用程序的用户主体开始正常运行所需的基本步骤。 可查看以下文章获取更多的信息：
* [使用 PowerShell 创建服务主体](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用证书身份验证进行服务主体身份验证](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Azure AD 身份验证的其他方法](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)



