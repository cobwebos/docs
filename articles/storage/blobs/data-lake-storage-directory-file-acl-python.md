---
title: 用于文件 & Acl 的 Azure Data Lake Storage Gen2 Python SDK （预览版）
description: 使用启用了分层命名空间（HNS）的存储帐户中的 Python 管理目录和文件和目录访问控制列表（ACL）。
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e07bb6aa9d1fe22baaebb7bc7239ce03a728c6b9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431806"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 Python 管理 Azure Data Lake Storage Gen2 （预览版）中的目录、文件和 Acl

本文介绍如何使用 Python 在已启用分层命名空间（HNS）的存储帐户中创建和管理目录、文件和权限。 

> [!IMPORTANT]
> 用于 Python 的 Azure Data Lake Storage 客户端库当前为公共预览版。

[包（Python 包索引）](https://pypi.org/project/azure-storage-file-datalake/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API 参考](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Gen1 到 Gen2 映射](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [提供反馈](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>必备组件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 已启用分层命名空间（HNS）的存储帐户。 按照[以下](data-lake-storage-quickstart-create-account.md)说明创建一个。

## <a name="set-up-your-project"></a>设置项目

使用[pip](https://pypi.org/project/pip/)安装适用于 Python 的 Azure Data Lake Storage 客户端库。

```
pip install azure-storage-file-datalake --pre
```

将这些 import 语句添加到代码文件的顶部。

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>连接到帐户

若要使用本文中的代码段，需要创建表示存储帐户的**DataLakeServiceClient**实例。 若要获取一个帐户，最简单的方法是使用帐户密钥。 

此示例使用帐户密钥创建表示存储帐户的**DataLakeServiceClient**实例。 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- 将 `storage_account_name` 占位符值替换为存储帐户的名称。

- 将 `storage-account-key` 占位符值替换为你的存储帐户访问密钥。

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以通过调用**Create_file_system FileSystemDataLakeServiceClient**方法来创建一个。

此示例将创建一个名为 `my-file-system`的文件系统。

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>创建目录

通过调用**Create_directory FileSystemClient**方法来创建目录引用。

此示例将名为 `my-directory` 的目录添加到文件系统。 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用**Rename_directory DataLakeDirectoryClient**方法重命名或移动目录。 传递所需目录的路径。 

此示例将子目录重命名为 `my-subdirectory-renamed`的名称。

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>删除目录

通过调用**Delete_directory DataLakeDirectoryClient**方法来删除目录。

此示例删除名为 `my-directory`的目录。  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>管理目录权限

通过调用**Get_access_control DataLakeDirectoryClient**方法来获取目录的访问控制列表（ACL），并通过调用**set_access_control DataLakeDirectoryClient**方法来设置 ACL。

此示例获取并设置名为 `my-directory`的目录的 ACL。 字符串 `rwxr-xrw-` 提供拥有的用户的 "读取"、"写入" 和 "执行" 权限，为拥有的组授予 "读取" 和 "执行" 权限，并为所有其他用户提供读取和写入权限。

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录 

首先，通过创建**DataLakeFileClient**类的实例在目标目录中创建文件引用。 通过调用**Append_data DataLakeFileClient**方法上传文件。 请确保通过调用**Flush_data DataLakeFileClient**方法完成上传。

此示例将文本文件上传到名为 `my-directory`的目录。   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>管理文件权限

通过调用**Get_access_control DataLakeFileClient**方法来获取文件的访问控制列表（ACL），并通过调用**set_access_control DataLakeFileClient**方法来设置 ACL。

此示例获取并设置名为 `my-file.txt`的文件的 ACL。 字符串 `rwxr-xrw-` 提供拥有的用户的 "读取"、"写入" 和 "执行" 权限，为拥有的组授予 "读取" 和 "执行" 权限，并为所有其他用户提供读取和写入权限。

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>从目录下载 

打开用于写入的本地文件。 然后，创建表示要下载的文件的**DataLakeFileClient**实例。 调用**Read_file DataLakeFileClient**从文件中读取字节，然后将这些字节写入本地文件。 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>列出目录内容

通过调用**Get_paths FileSystemClient**方法并枚举结果来列出目录内容。

此示例将打印位于名为 `my-directory`的目录中的每个子目录和文件的路径。

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>另请参阅

* [API 参考文档](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [包（Python 包索引）](https://pypi.org/project/azure-storage-file-datalake/)
* [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 到 Gen2 的映射](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [已知问题](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [提供反馈](https://github.com/Azure/azure-sdk-for-python/issues)
