---
title: ".NET SDK：Azure Data Lake Store 中的文件系统操作 | Microsoft Docs"
description: "使用 Azure Data Lake Store .NET SDK 在 Data Lake Store 上执行文件系统操作，例如创建文件夹等。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 7f6319dcf1ae66a686dd1c2ea2810b3041183098
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>使用 .NET SDK 在 Azure Data Lake Store 上进行的文件系统操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

本文介绍如何使用 .NET SDK 在 Data Lake Store 上执行文件系统操作。 文件系统操作包括在 Data Lake Store 帐户中创建文件夹、上传文件、下载文件，等等。

若要了解如何使用 .NET SDK 在 Data Lake Store 上执行帐户管理操作，请参阅[在 Data Lake Store 上使用 .NET SDK 进行的帐户管理操作](data-lake-store-get-started-net-sdk.md)。

## <a name="prerequisites"></a>先决条件
* **Visual Studio 2013、2015 或 2017**。 以下说明使用的是 Visual Studio 2017。

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>创建 .NET 应用程序
1. 打开 Visual Studio，创建一个控制台应用程序。
2. 在“文件”菜单中，单击“新建”，并单击“项目”。
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

7. 声明变量（如下所示），并提供占位符的值。 此外，确保计算机中存在此处提供的本地路径和文件名。

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static async void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

本文的剩余部分介绍如何使用现有的 .NET 方法来执行操作，例如身份验证和文件上传等。

## <a name="authentication"></a>身份验证

* 有关应用程序的最终用户身份验证，请参阅[使用 .NET SDK 通过 Data Lake Store 进行最终用户身份验证](data-lake-store-end-user-authenticate-net-sdk.md)。
* 有关应用程序的服务到服务身份验证，请参阅[使用 .NET SDK 通过 Data Lake Store 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-net-sdk.md)。


## <a name="create-client-objects"></a>创建客户端对象
以下代码片段创建了 Data Lake Store 帐户和 filesystem 客户端对象，用于向服务发出请求。

    // Create client objects
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

## <a name="create-a-directory"></a>创建目录
向类添加以下方法。 此代码片段演示了 `CreateDirectory()` 方法，使用该方法可以在 Data Lake Store 帐户中创建目录。

    // Create a directory
    public static void CreateDirectory(string path)
    {
            _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

向 `Main()` 方法添加以下代码片段，以便调用 `CreateDirectory()` 方法。

    CreateDirectory(remoteFolderPath);
    Console.WriteLine("Created a directory in the Data Lake Store account. Press any key to continue ...");
    Console.ReadLine();

## <a name="upload-a-file"></a>上传文件
向类添加以下方法。 此代码片段演示了 `UploadFile()` 方法，使用该方法可以将文件上传到 Data Lake Store 帐户。 SDK 支持在本地文件路径与 Data Lake Store 文件路径之间进行递归上传和下载。

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

向 `Main()` 方法添加以下代码片段，以便调用 `UploadFile()` 方法。

    UploadFile(localFilePath, remoteFilePath, true);
    Console.WriteLine("Uploaded file in the directory. Press any key to continue...");
    Console.ReadLine();
    

## <a name="get-file-or-directory-info"></a>获取文件或目录信息
以下代码片段演示了 `GetItemInfo()` 方法，使用该方法可以检索有关 Data Lake Store 中文件和目录的信息。

    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

向 `Main()` 方法添加以下代码片段，以便调用 `GetItemInfo()` 方法。

    
    var fileProperties = GetItemInfo(remoteFilePath);
    Console.WriteLine("The owner of the file at the path is:", fileProperties.Owner);
    Console.WriteLine("Press any key to continue...");
    Console.ReadLine();

## <a name="list-file-or-directories"></a>列出文件或目录
以下代码片段演示了 `ListItems()` 方法，使用该方法可以列出 Data Lake Store 帐户中的文件或目录。

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

向 `Main()` 方法添加以下代码片段，以便调用 `ListItems()` 方法。

    var itemList = ListItems(remoteFolderPath);
    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
    Console.WriteLine(String.Join("\r\n", fileMenuItems));
    Console.WriteLine("Files and directories listed. Press any key to continue ...");
    Console.ReadLine();

## <a name="concatenate-files"></a>连接文件
以下代码片段演示了 `ConcatenateFiles()` 方法，使用该方法可以连接文件。

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

向 `Main()` 方法添加以下代码片段，以便调用 `ConcatenateFiles()` 方法。 此代码片段假定，另一文件已上传到 Data Lake Store 帐户，且文件的路径在字符串 anotherRemoteFilePath 中提供。

    string[] stringOfFiles = new String[] {remoteFilePath, anotherRemoteFilePath};
    string destFilePath = Path.Combine(remoteFolderPath, "Concatfile.txt");
    ConcatenateFiles(stringOfFiles, destFilePath);
    Console.WriteLine("Files concatinated. Press any key to continue ...");
    Console.ReadLine();

## <a name="append-to-a-file"></a>附加到文件
以下代片码段演示了 `AppendToFile()` 方法，使用该方法可以将数据附加到已存储在 Data Lake Store 帐户中的文件。

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

向 `Main()` 方法添加以下代码片段，以便调用 `AppendToFile()` 方法。

    AppendToFile(remoteFilePath, "123");
    Console.WriteLine("Content appended. Press any key to continue ...");
    Console.ReadLine();

## <a name="download-a-file"></a>下载文件
下面的代片码段演示了 `DownloadFile()` 方法，使用该方法可以从 Data Lake Store 帐户下载文件。

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:true);
    }

向 `Main()` 方法添加以下代码片段，以便调用 `DownloadFile()` 方法。

    DownloadFile(destFilePath, localFilePath);
    Console.WriteLine("File downloaded. Press any key to continue ...");
    Console.ReadLine();

## <a name="see-also"></a>另请参阅
* [Data Lake Store 上的帐户管理操作（使用 .NET SDK）](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK 参考](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
