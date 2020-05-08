---
title: 使用 .NET 管理 Azure Data Lake Storage Gen1 帐户
description: 了解如何使用 .NET SDK 进行 Azure Data Lake Storage Gen1 帐户管理操作。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d7ea2b7fffc4edff6cae895e08b955d2e21ff5f2
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692122"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>使用 .NET SDK 对 Azure Data Lake Storage Gen1 进行的帐户管理操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

本文介绍如何使用 .NET SDK 在 Azure Data Lake Storage Gen1 上执行帐户管理操作。 帐户管理操作包括创建 Data Lake Storage Gen1 帐户、列出 Azure 订阅中的帐户、删除帐户，等等。

若要了解如何使用 .NET SDK 在 Data Lake Storage Gen1 上执行数据管理操作，请参阅[在 Data Lake Storage Gen1 上使用 .NET SDK 进行的文件系统操作](data-lake-store-data-operations-net-sdk.md)。

## <a name="prerequisites"></a>先决条件
* **Visual Studio 2013 或更高**版本。 以下说明使用 Visual Studio 2019。

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="create-a-net-application"></a>创建 .NET 应用程序
1. 在 Visual Studio 中，选择 "**文件**" 菜单，选择 "**新建**"，然后选择 "**项目**"。
2. 选择 "**控制台应用（.NET Framework）**"，然后选择 "**下一步**"。
3. 在“项目名称”**** 中，输入 `CreateADLApplication`，然后选择“创建”****。

4. 将 NuGet 包添加到项目。

   1. 在解决方案资源管理器中右键单击项目名称，单击“管理 NuGet 包” ****。
   2. 在 " **NuGet 包管理器**" 选项卡中，确保 "**包源**" 设置为**Nuget.org** ，并选中 "**包括预发行**版" 复选框。
   3. 搜索并安装以下 NuGet 包：

      * `Microsoft.Azure.Management.DataLake.Store` - 本教程使用 v2.1.3-预览版。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教程使用 v2.2.12。

        ![添加 NuGet 源](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "创建新的 Azure Data Lake 帐户")
   4. 关闭**NuGet 包管理器**。
5. 打开“Program.cs” ****，删除现有代码，并包含以下语句，添加对命名空间的引用。

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

6. 声明变量，并提供占位符的值。 此外，确保计算机中存在所提供的本地路径和文件名。

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
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

本文的剩余部分介绍如何使用现有的 .NET 方法来执行操作，例如身份验证和文件上传等。

## <a name="authentication"></a>身份验证

* 若要了解应用程序的最终用户身份验证，请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-net-sdk.md)。
* 若要了解应用程序的服务到服务身份验证，请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-net-sdk.md)。

## <a name="create-client-object"></a>创建客户端对象
以下代码片段创建 Data Lake Storage Gen1 帐户客户端对象，该对象用于向服务发出帐户管理请求，例如创建帐户、删除帐户，等等。

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>创建 Data Lake Storage Gen1 帐户
以下代码片段在 Azure 订阅中创建 Data Lake Storage Gen1 帐户，该订阅是在创建 Data Lake Storage Gen1 帐户客户端对象时提供的。

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>列出订阅中的所有 Data Lake Storage Gen1 帐户
向类定义添加以下方法。 以下代码片段列出了给定 Azure 订阅中的所有 Data Lake Storage Gen1 帐户。

    // List all Data Lake Storage Gen1 accounts within the subscription
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

## <a name="delete-a-data-lake-storage-gen1-account"></a>删除 Data Lake Storage Gen1 帐户
以下代码片段删除此前创建的 Data Lake Storage Gen1 帐户。

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>另请参阅
* [在 Data Lake Storage Gen1 上使用 .NET SDK 进行的文件系统操作](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK 参考](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
