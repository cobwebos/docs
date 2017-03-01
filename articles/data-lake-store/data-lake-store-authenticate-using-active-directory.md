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
ms.date: 01/10/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: dac6c9f3be7b4535f8cb30a9ec0c1e398ca5ff28


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

本文讨论如何为服务到服务身份验证创建 Azure AD Web 应用程序。 有关最终用户身份验证的 Azure AD 应用程序配置的说明，请参阅[使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-an-active-directory-application"></a>创建 Active Directory 应用程序
本部分介绍如何使用 Azure Active Directory，针对通过 Azure Data Lake Store 进行服务到服务身份验证，创建和配置 Azure AD Web 应用程序。 请注意，创建“Active Directory 应用程序”是创建服务主体，而不是创建应用或任何代码。

### <a name="step-1-create-an-azure-active-directory-application"></a>步骤 1：创建 Azure Active Directory 应用程序
1. 通过[经典门户](https://manage.windowsazure.com/)登录到 Azure 帐户。
2. 在左侧窗格中选择“**Active Directory**”。
   
     ![选择“Active Directory”](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
3. 选择要用于创建新应用程序的 Active Directory。 如果有多个 Active Directory，通常需要在订阅所在目录中创建应用程序。 只能为与订阅在同一目录中的应用程序授予对订阅中资源的访问权限。  
   
     ![选择目录](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
4. 若要查看目录中的应用程序，请单击“**应用程序”**。
   
     ![查看应用程序](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)
5. 如果之前尚未在该目录中创建应用程序，则应该会看到与下图类似的情形。 单击“添加应用程序”
   
     ![添加应用程序](./media/data-lake-store-authenticate-using-active-directory/create-application.png)
   
     或者，单击底部窗格中的“添加”。
   
     ![添加](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)
6. 提供应用程序的名称，并选择要创建的应用程序的类型。 对于本教程，请创建“WEB 应用程序和/或 WEB API”，然后单击“下一步”按钮。
   
     ![命名应用程序](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

    > [!TIP]
    > 提供更易搜索的应用程序名称。 本教程中之后会搜索此应用程序，并将其分配给 Data Lake Store 帐户。
    > 
    > 

7. 填写应用程序的属性。 对于“登录 URL”，请提供用于描述应用程序的网站 URI。 将不验证该网站是否存在。 
   对于“应用 ID URI”，请提供用于标识应用程序的 URI。
   
     ![应用程序属性](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)
   
    单击复选标记以完成向导，并创建应用程序。

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>步骤 2：获取客户端 ID、客户端密码和令牌终结点
以编程方式登录时，需要应用程序的 ID。 如果应用程序在其自己的凭据下运行，则还需要身份验证密钥。

1. 单击“配置”选项卡以配置应用程序的密码。
   
     ![配置应用程序](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)
2. 复制**客户端 ID**。
   
     ![客户端 ID](./media/data-lake-store-authenticate-using-active-directory/client-id.png)
3. 如果应用程序将在其自己的凭据下运行，请向下滚动到“密钥”部分，并选择希望密码保持有效的时间。
   
     ![密钥](./media/data-lake-store-authenticate-using-active-directory/create-key.png)
4. 选择“保存”以创建密钥。
   
    ![保存](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)
   
    随后将显示保存的密钥，你可以复制该密钥。 稍后将不能检索此密钥，因此必须现在复制。
   
    ![保存的密钥](./media/data-lake-store-authenticate-using-active-directory/save-key.png)
5. 通过选择屏幕底部的“查看终结点”，然后检索“OAuth 2.0 令牌终结点”字段的值来检索令牌终结点，如下所示。  
   
    ![租户 ID](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

#### <a name="note-down-the-following-properties-that-you-will-need-for-the-next-steps"></a>记下执行后续步骤需要用到的以下属性：
1. 上述步骤 1.6 中创建的 Web 应用程序 ID 的名称
2. 上述步骤 2.2 中检索的客户端 ID
3. 上述步骤 2.4 中创建的密钥
4. 上述步骤 2.5 中检索的租户 ID

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>步骤 3：将 Azure AD 应用程序分配给 Azure Data Lake Store 帐户文件或文件夹（仅适用于服务到服务身份验证）
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

## <a name="next-steps"></a>后续步骤
在本文中，创建了一个 Azure AD Web 应用程序，并使用 .NET SDK、Java SDK 等在创作的客户端应用程序中收集了所需的信息。现在可以转到以下文章，它们讨论了如何使用 Azure AD Web 应用程序先进行 Data Lake Store 身份验证，再在存储中执行其他操作。

* [Get started with Azure Data Lake Store using .NET SDK](data-lake-store-get-started-net-sdk.md)
* [通过 Java SDK 实现 Azure Data Lake Store 入门](data-lake-store-get-started-java-sdk.md)

本文介绍了让应用程序的用户主体开始正常运行所需的基本步骤。 可查看以下文章获取更多的信息：
* [使用 PowerShell 创建服务主体](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用证书身份验证进行服务主体身份验证](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal#create-service-principal-with-certificate)
* [Azure AD 身份验证的其他方法](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-authentication-scenarios)





<!--HONumber=Jan17_HO4-->


