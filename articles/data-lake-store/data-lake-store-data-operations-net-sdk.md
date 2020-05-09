---
title: .NET SDK-Data Lake Storage Gen1 上的文件系统操作-Azure
description: 使用 Azure Data Lake Storage Gen1 .NET SDK 执行 Data Lake Storage Gen1 上的文件系统操作，例如创建文件夹等。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 9e52a3cb8d31b5fccc9a0b5a3a76d4e9b43de123
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690969"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>使用 .NET SDK Data Lake Storage Gen1 的文件系统操作

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

本文介绍如何使用 .NET SDK 在 Data Lake Storage Gen1 上执行文件系统操作。 文件系统操作包括在 Data Lake Storage Gen1 帐户中创建文件夹、上传文件、下载文件，等等。

有关如何使用 .NET SDK 在 Data Lake Storage Gen1 上执行帐户管理操作的说明，请参阅[使用 .NET sdk 在 Data Lake Storage Gen1 上的帐户管理操作](data-lake-store-get-started-net-sdk.md)。

## <a name="prerequisites"></a>先决条件

* **Visual Studio 2013 或更高**版本。 本文中的说明使用的是 Visual Studio 2019。

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅[Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)。

## <a name="create-a-net-application"></a>创建 .NET 应用程序

[GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 上的代码示例逐步讲解了在存储中创建文件、连接文件、下载文件以及在存储中删除某些文件的过程。 本文的此部分演练代码的主要组成部分。

1. 在 Visual Studio 中，选择 "**文件**" 菜单，选择 "**新建**"，然后选择 "**项目**"。
1. 选择 "**控制台应用（.NET Framework）**"，然后选择 "**下一步**"。
1. 在“项目名称”**** 中，输入 `CreateADLApplication`，然后选择“创建”****。
1. 将 NuGet 包添加到项目。

   1. 在解决方案资源管理器中右键单击项目名称，单击“管理 NuGet 包” ****。
   1. 在 " **NuGet 包管理器**" 选项卡中，确保 "**包源**" 设置为 " **nuget.org**"。此外，请确保选中 "**包括预发行**版" 复选框。
   1. 搜索并安装以下 NuGet 包：

      * `Microsoft.Azure.DataLake.Store`-本文使用的是1.0.0。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`-本文使用的是 v1.0。

      关闭**NuGet 包管理器**。

1. 打开“Program.cs” ****，删除现有代码，并包含以下语句，添加对命名空间的引用。

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. 声明变量（如下所示），并提供占位符的值。 此外，确保计算机中存在此处提供的本地路径和文件名。

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

本文的其余部分介绍如何使用可用的 .NET 方法执行身份验证、文件上传等操作。

## <a name="authentication"></a>身份验证

* 若要了解应用程序的最终用户身份验证，请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-net-sdk.md)。
* 若要了解应用程序的服务到服务身份验证，请参阅[使用 .NET SDK 通过 Data Lake Storage Gen1 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-net-sdk.md)。

## <a name="create-client-object"></a>创建客户端对象

以下代码片段创建了 Data Lake Storage Gen1 filesystem 客户端对象，用于向服务发出请求。

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>创建文件和目录

将以下代码片段添加到应用程序。 此代码段将添加一个文件和任何不存在的父目录。

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>附加到文件

以下代码片段将数据附加到 Data Lake Storage Gen1 帐户中的现有文件。

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>读取文件

以下代码片段读取 Data Lake Storage Gen1 中文件的内容。

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>获取文件属性

以下代码片段返回与文件或目录关联的属性。

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

[Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 上的示例中提供了 `PrintDirectoryEntry` 方法的定义。

## <a name="rename-a-file"></a>重命名文件

以下代码片段重命名 Data Lake Storage Gen1 帐户中的现有文件。

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>枚举目录

以下代码片段枚举 Data Lake Storage Gen1 帐户中的目录。

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

[Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) 上的示例中提供了 `PrintDirectoryEntry` 方法的定义。

## <a name="delete-directories-recursively"></a>以递归方式删除目录

以下代码片段以递归方式删除目录及其所有子目录。

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>示例

下面是一些演示如何使用 Data Lake Storage Gen1 Filesystem SDK 的示例。

* [Github 上的基本示例](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Github 上的高级示例](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>另请参阅

* [使用 .NET SDK 对 Data Lake Storage Gen1 上的帐户管理操作](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK 参考](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>后续步骤

* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
