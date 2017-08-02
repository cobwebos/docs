---
title: "使用 C++ 针对 Azure 文件存储进行开发 | Microsoft Docs"
description: "了解如何开发使用 Azure 文件存储来存储文件数据的 C++ 应用程序和服务。"
services: storage
documentationcenter: .net
author: renashahmsft
manager: aungoo
editor: tysonn
ms.assetid: a1e8c99e-47a6-43a9-9541-c9262eb00b38
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: renashahmsft
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: fc0d8451442f1337db4a36718c3fc746f8eb5125
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# <a name="develop-for-azure-file-storage-with-c"></a>使用 C++ 针对 Azure 文件存储进行开发
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>关于本教程

本教程介绍如何针对 Azure 文件存储执行基本的操作。 通过用 C++ 编写的示例，学习如何创建共享和目录，以及如何上传、列出和删除文件。 如果不熟悉 Azure 文件存储，学习后续部分的概念对于理解示例有帮助。


* 创建和删除 Azure 文件共享
* 创建和删除目录
* 枚举 Azure 文件共享中的文件和目录
* 上传、下载和删除文件
* 设置 Azure 文件共享的配额（最大大小）
* 若一个文件使用在共享中定义的共享访问策略，则为该文件创建一个共享访问签名（SAS 密钥）。

> [!Note]  
> 由于 Azure 文件存储可以通过 SMB 进行访问，因此可以编写简单的应用程序，通过标准的 C++ I/O 类和函数来访问 Azure 文件共享。 本文将介绍如何编写使用 Azure 存储 C++ SDK 的应用程序，该 SDK 使用 [Azure 文件存储 REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) 与 Azure 文件存储通信。

## <a name="create-a-c-application"></a>创建 C++ 应用程序
若要生成示例，需要安装用于 C++ 的 Azure 存储客户端库 2.4.0。 此外，你应该已经创建了一个 Azure 存储帐户。

若要安装用于 C++ 的 Azure 存储客户端 2.4.0，可以使用以下方法之一：

* **Linux：**按照[适用于 C++ 的 Azure 存储客户端库自述文件](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)页中提供的说明操作。
* **Windows：**在 Visual Studio 中，单击“工具”&gt;“NuGet 包管理器”&gt;“程序包管理器控制台”。 在 [NuGet 包管理器控制台](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)中，键入以下命令，然后按 **ENTER**。
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-file-storage"></a>设置应用程序以使用 Azure 文件存储
将以下 include 语句添加到要在其中操作 Azure 文件存储的 C++ 源文件的顶部：

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串
若要使用文件存储，你需要连接到你的 Azure 存储帐户。 第一步是配置连接字符串，我们会使用该字符串连接到存储帐户。 为此，我们需要定义一个静态变量。

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>连接到 Azure 存储帐户
可使用 **cloud_storage_account** 类来表示存储帐户信息。 若要从存储连接字符串中检索存储帐户信息，可以使用 **parse** 方法。

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
Azure 文件存储中的所有文件和目录都位于名为 **Share** 的容器内。 存储帐户可以拥有无数的共享，只要帐户容量允许。 若要获得共享及其内容的访问权限，需要使用 Azure 文件存储客户端。

```cpp
// Create the Azure File storage client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

使用 Azure 文件存储客户端可以获取对共享的引用。

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

若要创建共享，可使用 **cloud_file_share** 对象的 **create_if_not_exists** 方法。

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

此时，**share** 保留对名为 **my-sample-share** 的共享的引用。

## <a name="delete-an-azure-file-share"></a>删除 Azure 文件共享
删除共享时，可针对 cloud_file_share 对象调用 **delete_if_exists** 方法。 以下是具有此类功能的示例代码。

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>创建目录
可以将文件置于子目录中，不必将其全部置于根目录中，以便对存储进行有效的组织。 Azure 文件存储允许创建帐户允许的任意数目的目录。 下面的代码会在根目录下创建一个名为 **my-sample-directory** 的目录，以及一个名为 **my-sample-subdirectory** 的子目录。

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory = 
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>删除目录
删除目录很简单，但需注意的是，不能删除仍然包含文件或其他目录的目录。

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory = 
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>枚举 Azure 文件共享中的文件和目录
通过针对 **cloud_file_directory** 引用调用 **list_files_and_directories**，可以轻松获取共享内文件和目录的列表。 若要访问返回的 **list_file_and_directory_item** 的丰富属性和方法集，必须调用 **list_file_and_directory_item.as_file** 方法以获取 **cloud_file** 对象，或调用 **list_file_and_directory_item.as_directory** 方法以获取 **cloud_file_directory** 对象。

下面的代码演示如何检索和输出共享的根目录中每一项的 URI。

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_diretory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }        
}
```

## <a name="upload-a-file"></a>上传文件
Azure 文件共享至少包含文件所在的根目录。 在本部分，你将学习如何将文件从本地存储上传到共享所在的根目录。

上传文件的第一步是获取对文件所在的目录的引用。 为此，需要调用共享对象的 **get_root_directory_reference** 方法。

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

现在，已经拥有共享所在的根目录的引用，因此可以将文件上传到其中。 此示例从文件、文本和流上传。

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream = 
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));    
```

## <a name="download-a-file"></a>下载文件
若要下载文件，请先检索文件引用，然后调用 **download_to_stream** 方法，将文件内容传输到流对象，随后可将该流对象保存到本地本件。 也可使用 **download_to_file** 方法将文件的内容下载到本地文件。 可使用 **download_text** 方法，以文本字符串形式下载文件的内容。

下面的示例使用 **download_to_stream** 和 **download_text** 方法，演示如何下载之前部分中创建的文件。

```cpp
// Download as text
azure::storage::cloud_file text_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>删除文件
另一项常见的 Azure 文件存储操作是删除文件。 下面的代码删除存储在根目录下的名为 my-sample-file-3 的文件。

```cpp
// Get a reference to the root directory for the share.    
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

azure::storage::cloud_file file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>设置 Azure 文件共享的配额（最大大小）
可以使用 GB 作为单位设置文件的配额（或最大大小）。 你还可以查看共享当前存储了多少数据。

通过设置一个共享的配额，可以限制在该共享上存储的文件的总大小。 如果共享上文件的总大小超过在共享上设定的配额，则客户端将不能增加现有文件的大小或创建新文件，除非这些文件是空的。

下面的示例演示如何检查共享的当前使用情况，以及如何设置共享的配额。

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>为文件或文件共享生成共享访问签名
可以为文件共享或单个文件生成共享访问签名 (SAS)。 还可以在文件共享上创建一个共享访问策略以管理共享访问签名。 建议创建共享访问策略，因为它提供了一种在受到威胁时撤消 SAS 的方式。

以下示例在一个共享上创建共享访问策略，然后使用该策略为共享中的一个文件提供 SAS 约束。

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy = 
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() + 
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;    

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir = 
        share.get_root_directory_reference();
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share 
    //  and associate this access policy with it.        
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.        
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");        
    file_with_sas.upload_text(text);        

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();        
    ucout << downloaded_text << std::endl;        
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```
## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 存储的详细信息，请参阅以下资源：

* [适用于 C++ 的存储客户端库](https://github.com/Azure/azure-storage-cpp)
* [Azure Storage File Service Samples in C++] (https://github.com/Azure-Samples/storage-file-cpp-getting-started)（C++ 中的 Azure 存储文件服务示例）
* [Azure 存储资源管理器](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure 存储文档](https://azure.microsoft.com/documentation/services/storage/)
