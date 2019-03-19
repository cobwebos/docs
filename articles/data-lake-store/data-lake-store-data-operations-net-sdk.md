---
title: .NET SDK：对 Azure Data Lake Storage Gen1 执行的文件系统操作 | Microsoft Docs
description: 使用 Azure Data Lake Storage Gen1 .NET SDK 在 Data Lake Storage Gen1 上执行文件系统操作，例如创建文件夹等。
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 0a6cfc5ca9a0b788075f4472649a6e1bcdf87637
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119490"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>使用 .NET SDK 对 Azure Data Lake Storage Gen1 进行的文件系统操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

本文介绍如何使用 .NET SDK 在 Data Lake Storage Gen1 上执行文件系统操作。 文件系统操作包括在 Data Lake Storage Gen1 帐户中创建文件夹、上传文件、下载文件，等等。

若要了解如何使用 .NET SDK 对 Data Lake Storage Gen1 执行帐户管理操作，请参阅[使用 .NET SDK 对 Data Lake Storage Gen1 进行的帐户管理操作](data-lake-store-get-started-net-sdk.md)。

## <a name="prerequisites"></a>必备组件
* **Visual Studio 2013、2015 或 2017**。 以下说明使用的是 Visual Studio 2017。

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>创建 .NET 应用程序
[GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 上的代码示例逐步讲解了在存储中创建文件、连接文件、下载文件以及在存储中删除某些文件的过程。 本文的此部分演练代码的主要组成部分。

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

      * `Microsoft.Azure.DataLake.Store` - 本教程使用 v1.0.0。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教程使用 v2.3.1。
    
      关闭“NuGet 包管理器”。

6. 打开“Program.cs” ，删除现有代码，并包含以下语句，添加对命名空间的引用。

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. 声明变量（如下所示），并提供占位符的值。 此外，确保计算机中存在此处提供的本地路径和文件名。

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";        
            }
        }

本文的剩余部分介绍如何使用现有的 .NET 方法来执行操作，例如身份验证和文件上传等。

## <a name="authentication"></a>Authentication

* 若要了解应用程序的最终用户身份验证，请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-net-sdk.md)。
* 若要了解应用程序的服务到服务身份验证，请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-net-sdk.md)。


## <a name="create-client-object"></a>创建客户端对象
以下代码片段创建了 Data Lake Storage Gen1 filesystem 客户端对象，用于向服务发出请求。

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>创建文件和目录
将以下代码片段添加到应用程序。 此代码片段添加一个文件，以及不存在的任何父目录。

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>附加到文件
以下代码片段将数据附加到 Data Lake Storage Gen1 帐户中的现有文件。

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>读取文件
以下代码片段读取 Data Lake Storage Gen1 中文件的内容。

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>获取文件属性
以下代码片段返回与文件或目录关联的属性。

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

[Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 上的示例中提供了 `PrintDirectoryEntry` 方法的定义。 

## <a name="rename-a-file"></a>重命名文件
以下代码片段重命名 Data Lake Storage Gen1 帐户中的现有文件。

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>枚举目录
以下代码片段枚举 Data Lake Storage Gen1 帐户中的目录

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

[Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 上的示例中提供了 `PrintDirectoryEntry` 方法的定义。

## <a name="delete-directories-recursively"></a>以递归方式删除目录
以下代码片段以递归方式删除目录及其所有子目录。

    // Delete a directory and all its subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>示例
下面是一些示例，介绍了如何使用 Data Lake Storage Gen1 Filesystem SDK。
* [Github 上的基本示例](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Github 上的高级示例](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>另请参阅
* [使用 .NET SDK 对 Data Lake Storage Gen1 上的帐户管理操作](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK 参考](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>后续步骤
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
