---
title: "在 Azure Data Lake Store 中使用 .NET SDK 开发应用程序 | Microsoft Docs"
description: "使用 Azure Data Lake Store .NET SDK 创建 Data Lake Store 帐户，在 Data Lake Store 中执行基本操作"
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
ms.date: 05/09/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: f637b8617c4a9ed3be1eb0932a94b0ffcc7c8f7e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017


---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>通过 .NET SDK 实现 Azure Data Lake Store 入门
> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用 [Azure Data Lake Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) 执行基本操作，如创建文件夹、上传和下载数据文件等。有关 Data Lake 的详细信息，请参阅 [Azure Data Lake Store](data-lake-store-overview.md)。

## <a name="prerequisites"></a>先决条件
* **Visual Studio 2013、2015 或 2017**。 以下说明使用 Visual Studio 2015 Update 2。

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)

* **创建 Azure Active Directory 应用程序**。 使用 Azure AD 应用程序对 Data Lake Store 应用程序进行 Azure AD 身份验证。 有不同的方式可进行 Azure AD 身份验证，即“最终用户身份验证”或“服务到服务身份验证”。 有关如何进行身份验证的说明和详细信息，请参阅[最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)或[服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。

## <a name="create-a-net-application"></a>创建 .NET 应用程序
1. 打开 Visual Studio，创建一个控制台应用程序。
2. 在“文件”菜单中，单击“新建”，然后单击“项目”。
3. 在“新建项目”中，键入或选择以下值 ：

   | 属性 | 值 |
   | --- | --- |
   | 类别 |模板/Visual C#/Windows |
   | 模板 |控制台应用程序 |
   | Name |CreateADLApplication |
4. 单击“确定”以创建该项目  。
5. 将 NuGet 包添加到项目。

   1. 在解决方案资源管理器中右键单击项目名称，单击“管理 NuGet 包” 。
   2. 在“NuGet 包管理器”选项卡上，确保“包源”设置为“nuget.org”，“包含预发行版”复选框处于选中状态。
   3. 搜索并安装以下 NuGet 包：

      * `Microsoft.Azure.Management.DataLake.Store` - 本教程使用 v2.1.3-预览版。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教程使用 v2.2.12。

        ![添加 Nuget 源](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "创建新的 Azure Data Lake 帐户")
   4. 关闭“NuGet 包管理器” 。
6. 打开“Program.cs” ，删除现有代码，然后包含以下语句，添加对命名空间的引用。

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. 按如下所示声明变量，提供已存在的 Data Lake Store 名称和资源组名称的值。 此外，确保计算机中必须存在此处提供的本地路径和文件名。 在命名空间声明后，添加以下代码片段。

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

本文的剩余部分介绍如何使用现有的 .NET 方法来执行操作，例如身份验证和文件上传等。

## <a name="authentication"></a>身份验证

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>如果使用最终用户身份验证（本教程推荐）

可以对现有的 Azure AD 本机应用程序使用此方法，以**交互方式**对应用程序进行身份验证，这意味着系统会提示用户输入其 Azure 凭据。

为了方便使用，下面的代码片段使用客户端 ID 和重定向 URI 的默认值，这些默认值将适用于任何 Azure 订阅。 为了更快完成本教程，我们建议使用此方法。 在下面的代码片段中，直接提供租户 ID 的值。 可以根据[创建 Active Directory 应用程序](data-lake-store-end-user-authenticate-using-active-directory.md)中提供的说明进行检索。

    // User login via interactive popup
    // Use the client ID of an existing AAD Web application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;

对于上述代码片段，需要注意几个问题：

* 为了帮助读者更快完成本教程，此代码片段使用了为所有 Azure 订阅默认提供的 Azure AD 域和客户端 ID。 因此，可以**在应用程序中按原样使用此代码片段**。
* 但是，如果想要使用自己的 Azure AD 域和应用程序客户端 ID，则必须创建一个 Azure AD 本机应用程序，然后使用所创建的应用程序的 Azure AD 租户 ID、客户端 ID 和重定向 URI。 有关说明，请参阅[创建 Active Directory 应用程序，以便使用 Data Lake Store 进行最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)。

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>如果结合客户端机密使用服务到服务身份验证
可以使用以下代码片段通过客户端密码/应用程序密钥/服务主体对应用程序进行**非交互式**身份验证。 请对现有的 Azure AD“Web 应用”应用程序使用这种身份验证。 有关如何创建 Azure AD Web 应用程序以及如何检索以下代码片段中需要的客户端 ID 和客户端机密的说明，请参阅[创建 Active Directory 应用程序，以便使用 Data Lake Store 进行服务到服务身份验证](data-lake-store-authenticate-using-active-directory.md)。

    // Service principal / appplication authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = await ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential);

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>如果结合证书使用服务到服务身份验证

还可以使用以下代码片段，通过 Azure Active Directory 应用程序/服务主体的证书对应用程序进行**非交互式**身份验证。 请对现有的[带证书的 Azure AD 应用程序](../azure-resource-manager/resource-group-authenticate-service-principal.md)使用这种身份验证。

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = await ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate);

## <a name="create-client-objects"></a>创建客户端对象
以下代码片段创建了 Data Lake Store 帐户和 filesystem 客户端对象，用于向服务发出请求。

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>列出某个订阅中的所有 Data Lake Store 帐户
以下代码片段列出了给定 Azure 订阅中的所有 Data Lake Store 帐户。

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>创建目录
以下代码片段演示了 `CreateDirectory` 方法，使用该方法可以在 Data Lake Store 帐户中创建目录。

    // Create a directory
    public static async Task CreateDirectory(string path)
    {
        await _adlsFileSystemClient.FileSystem.MkdirsAsync(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>上传文件
以下代码片段演示了 `UploadFile` 方法，使用该方法可以将文件上传到 Data Lake Store 帐户。

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

SDK 支持在本地文件路径与 Data Lake Store 文件路径之间进行递归上传和下载。    

## <a name="get-file-or-directory-info"></a>获取文件或目录信息
以下代码片段演示了 `GetItemInfo` 方法，使用该方法可以检索有关 Data Lake Store 中文件和目录的信息。

    // Get file or directory info
    public static async Task<FileStatusProperties> GetItemInfo(string path)
    {
        return await _adlsFileSystemClient.FileSystem.GetFileStatusAsync(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>列出文件或目录
以下代码片段演示了 `ListItem` 方法，使用该方法可以列出 Data Lake Store 帐户中的文件或目录。

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>连接文件
以下代码片段演示了 `ConcatenateFiles` 方法，使用该方法可以连接文件。

    // Concatenate files
    public static Task ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        await _adlsFileSystemClient.FileSystem.ConcatAsync(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>附加到文件
以下代片码段演示了 `AppendToFile` 方法，使用该方法可以将数据附加到已存储在 Data Lake Store 帐户中的文件。

    // Append to file
    public static async Task AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            await _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

## <a name="download-a-file"></a>下载文件
下面的代片码段演示了 `DownloadFile` 方法，使用该方法可以从 Data Lake Store 帐户下载文件。

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
         _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath);
    }

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store .NET SDK 参考](https://docs.microsoft.com/dotnet/api/?view=azuremgmtdatalakestore-2.1.0-preview&term=DataLake.Store)
* [Data Lake Store REST 参考](https://msdn.microsoft.com/library/mt693424.aspx)

