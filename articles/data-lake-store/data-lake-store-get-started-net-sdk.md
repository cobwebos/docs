---
title: ".NET SDK：Azure Data Lake Store 上的帐户管理选项 | Microsoft Docs"
description: "使用 Azure Data Lake Store .NET SDK 在 Data Lake Store 中执行帐户管理操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: cb44fb1cbc279f12f970237f1498a570a63544bd
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>Azure Data Lake Store 上的帐户管理操作（使用 .NET SDK）
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

本文介绍如何使用 .NET SDK 在 Data Lake Store 上执行帐户管理操作。 帐户管理操作包括创建 Data Lake Store 帐户、列出 Azure 订阅中的帐户、删除帐户，等等。

若要了解如何使用 .NET SDK 在 Data Lake Store 上执行数据管理操作，请参阅[在 Data Lake Store 上使用 .NET SDK 进行的文件系统操作](data-lake-store-data-operations-net-sdk.md)。

## <a name="prerequisites"></a>先决条件
* **Visual Studio 2013、2015 或 2017**。 以下说明使用的是 Visual Studio 2017。

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-net-application"></a>创建 .NET 应用程序
1. 打开 Visual Studio，创建一个控制台应用程序。
2. 在“文件”菜单中，单击“新建”，并单击“项目”。
3. 在“新建项目”中，键入或选择以下值 ：

   | 属性 | 值 |
   | --- | --- |
   | 类别 |模板/Visual C#/Windows |
   | 模板 |控制台应用程序 |
   | 名称 |CreateADLApplication |
4. 单击“确定”以创建该项目  。
5. 将 NuGet 包添加到项目。

   1. 在解决方案资源管理器中右键单击项目名称，单击“管理 NuGet 包” 。
   2. 在“NuGet 包管理器”选项卡上，确保“包源”设置为“nuget.org”，“包含预发行版”复选框处于选中状态。
   3. 搜索并安装以下 NuGet 包：

      * `Microsoft.Azure.Management.DataLake.Store` - 本教程使用 v2.1.3-预览版。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教程使用 v2.2.12。

        ![添加 Nuget 源](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "创建新的 Azure Data Lake 帐户")
   4. 关闭“NuGet 包管理器”。
6. 打开“Program.cs” ，删除现有代码，并包含以下语句，添加对命名空间的引用。

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. 声明变量，并提供占位符的值。 此外，确保计算机中存在所提供的本地路径和文件名。

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

本文的剩余部分介绍如何使用现有的 .NET 方法来执行操作，例如身份验证和文件上传等。

## <a name="authentication"></a>身份验证

* 有关应用程序的最终用户身份验证，请参阅[使用 .NET SDK 通过 Data Lake Store 进行最终用户身份验证](data-lake-store-end-user-authenticate-net-sdk.md)。
* 有关应用程序的服务到服务身份验证，请参阅[使用 .NET SDK 通过 Data Lake Store 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-net-sdk.md)。

## <a name="create-client-object"></a>创建客户端对象
以下代码片段创建 Data Lake Store 帐户客户端对象，该对象用于向服务发出帐户管理请求，例如创建帐户、删除帐户，等等。

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>创建 Data Lake Store 帐户
以下代码片段在 Azure 订阅中创建 Data Lake Store 帐户，该订阅是在创建 Data Lake Store 帐户客户端对象时提供的。

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>列出某个订阅中的所有 Data Lake Store 帐户
向类定义添加以下方法。 以下代码片段列出了给定 Azure 订阅中的所有 Data Lake Store 帐户。

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>删除 Data Lake Store 帐户
以下代码片段删除此前创建的 Data Lake Store 帐户。

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>另请参阅
* [使用 .NET SDK 在 Data Lake Store 上进行的文件系统操作](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Store .NET SDK 参考](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
