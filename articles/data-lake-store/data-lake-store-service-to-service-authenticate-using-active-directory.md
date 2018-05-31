---
title: 使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证 | Microsoft Docs
description: 了解如何使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 7a4c94b5279962d9bf5524270c12e0c1ec512e3c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197430"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-azure-active-directory"></a>使用 Azure Active Directory 进行 Data Lake Store 服务到服务身份验证
> [!div class="op_single_selector"]
> * [最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Store 使用 Azure Active Directory 进行身份验证。 编写用于 Azure Data Lake Store 的应用程序之前，必须首先决定使用 Azure Active Directory (Azure AD) 对应用程序进行身份验证的方式。 可用的两个主要选项是：

* 最终用户身份验证 
* 服务到服务身份验证（本文所述） 

这两个选项都会将 OAuth 2.0 令牌提供给应用程序，此令牌会附加到对 Azure Data Lake Store 作出的每个请求。

本文讨论如何为服务到服务身份验证创建 Azure AD Web 应用程序。 有关最终用户身份验证的 Azure AD 应用程序配置的说明，请参阅[使用 Azure Active Directory 进行 Data Lake Store 最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="step-1-create-an-active-directory-web-application"></a>步骤 1：创建 Active Directory Web 应用程序

使用 Azure Active Directory，针对通过 Azure Data Lake Store 进行服务到服务身份验证，创建和配置 Azure AD Web 应用程序。 有关说明，请参阅[创建 Azure AD 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md)。

遵循以上链接的说明时，请确保为应用程序类型选择“Web 应用/API”，如以下屏幕截图所示：

![创建 Web 应用](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "创建 Web 应用")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>步骤 2：获取应用程序 ID、身份验证密钥和租户 ID
以编程方式登录时，需要应用程序的 ID。 如果应用程序在其自己的凭据下运行，则还需要身份验证密钥。

* 若要了解如何检索应用程序的应用程序 ID 和身份验证密钥（也称为客户端密码），请参阅[获取应用程序 ID 和身份验证密钥](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)。

* 有关如何检索租户 ID 的说明，请参阅[获取租户 ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)。

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder"></a>步骤 3：将 Azure AD 应用程序分配给 Azure Data Lake Store 帐户文件或文件夹


1. 登录到 [Azure 门户](https://portal.azure.com)。 打开要与之前创建的 Azure Active Directory 应用程序相关联的 Azure Data Lake Store 帐户。
2. 在 Data Lake Store 帐户边栏选项卡中，单击“数据资源管理器” 。
   
    ![在 Data Lake Store 帐户中创建目录](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "在 Data Lake 帐户中创建目")
3. 在“数据资源管理器”边栏选项卡中，单击要为其提供 Azure AD 应用程序访问权限的文件或文件夹，并单击“访问”。 若要配置对文件的访问，必须在“文件预览”边栏选项卡中单击“访问”。
   
    ![对 Data Lake 文件系统设置 ACL](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "对 Data Lake 文件系统设置 ACL")
4. “访问”边栏选项卡会列出已分配给根的标准访问和自定义访问。 单击“添加”图标添加自定义级别的 ACL。
   
    ![列出标准及自定义访问权限](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "列出标准及自定义访问权限")
5. 单击“添加”图标打开“添加自定义访问”边栏选项卡。 在此边栏选项卡中，单击“选择用户或组”，并在“选择用户或组”边栏选项卡中，查找之前创建的 Azure Active Directory 应用程序。 如果搜索范围中存在大量的组，请使用顶部的文本框筛选组名称。 单击要添加的组，并单击“选择”。
   
    ![添加组](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "添加组")
6. 单击“选择权限”，选择权限以及是将这些权限分配为默认 ACL、访问 ACL 还是同时分配为这两类。 单击“确定”。
   
    ![分配权限给组](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "分配权限给组")
   
    有关 Data Lake Store 中的权限和默认/访问 ACL 的详细信息，请参阅 [Data Lake Store 中的访问控制](data-lake-store-access-control.md)。
7. 在“添加自定义访问”边栏选项卡中，单击“确定”。 “访问”边栏选项卡中列出新添加的组以及相关的权限。
   
    ![分配权限给组](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "分配权限给组")

> [!NOTE]
> 如果计划将 Azure Active Directory 应用程序限制到特定文件夹，则还需要为相同的 Azure Active Directory 应用程序提供对根的执行权限，以便通过 .NET SDK 启用文件创建访问。

> [!NOTE]
> 要使用 SDK 创建 Data Lake Store 帐户，必须将 Azure AD Web 应用程序作为角色分配给要在其中创建 Data Lake Store 帐户的资源组。
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>步骤 4：获取 OAuth 2.0 令牌终结点（仅适用于基于 Java 的应用程序）

1. 登录 [Azure 门户](https://portal.azure.com)，在左侧窗格中单击“Active Directory”。

2. 在左侧窗格中，单击“应用注册”。

3. 在“应用注册”边栏选项卡顶部，单击“终结点”。

    ![OAuth 令牌终结点](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth 令牌终结点")

4. 从终结点列表中，复制 OAuth 2.0 令牌终结点。

    ![OAuth 令牌终结点](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth 令牌终结点")   

## <a name="next-steps"></a>后续步骤
本文创建了一个 Azure AD Web 应用程序，并使用 .NET SDK、Java、Python、REST API 等在创作的客户端应用程序中收集了所需的信息。现可转到以下文章，它们介绍如何使用 Azure AD 本机应用程序先进行 Data Lake Store 身份验证，再在存储中执行其他操作。

* [使用 Java 进行 Data Lake Store 服务到服务身份验证](data-lake-store-service-to-service-authenticate-java.md)
* [使用 .NET SDK 进行 Data Lake Store 服务到服务身份验证](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [使用 Python 进行 Data Lake Store 服务到服务身份验证](data-lake-store-service-to-service-authenticate-python.md)
* [使用 REST API 进行 Data Lake Store 服务到服务身份验证](data-lake-store-service-to-service-authenticate-rest-api.md)


