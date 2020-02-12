---
title: 在 Azure Data Lake Storage Gen2 （预览版）中将 JavaScript 用于文件 & Acl
description: 使用 Azure Storage Data Lake 适用于 JavaScript 的客户端库来管理已启用分层命名空间（HNS）的存储帐户中的目录和文件和目录访问控制列表（ACL）。
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154863"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>使用 JavaScript 管理 Azure Data Lake Storage Gen2 （预览版）中的目录、文件和 Acl

本文介绍如何使用 JavaScript 在启用了分层命名空间（HNS）的存储帐户中创建和管理目录、文件和权限。 

> [!IMPORTANT]
> 本文中所述的 JavaScript 库目前处于公共预览版中。

[包（节点包管理器）](https://www.npmjs.com/package/@azure/storage-file-datalake) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>必备条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 已启用分层命名空间（HNS）的存储帐户。 按照[以下](data-lake-storage-quickstart-create-account.md)说明创建一个。
> * 如果在 node.js 应用程序中使用此包，则需要 node.js 8.0.0 或更高版本。

## <a name="set-up-your-project"></a>设置项目

通过打开终端窗口，然后键入以下命令，安装适用于 JavaScript 的 Data Lake 客户端库。

```javascript
npm install @azure/storage-file-datalake
```

将此语句放置在代码文件的顶部，以导入 `storage-file-datalake` 包。 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>连接到帐户 

若要使用本文中的代码段，需要创建表示存储帐户的**DataLakeServiceClient**实例。 若要获取一个帐户，最简单的方法是使用帐户密钥。 

此示例使用帐户密钥创建**DataLakeServiceClient**的实例。

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> 此授权方法仅适用于 node.js 应用程序。 如果打算在浏览器中运行代码，则可以使用 Azure Active Directory （AD）进行授权。 有关如何执行此操作的指导，请参阅[Azure 存储文件 Data Lake 适用于 JavaScript 的客户端库](https://www.npmjs.com/package/@azure/storage-file-datalake)自述文件。 

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以通过获取**FileSystemClient**实例，然后调用**FileSystemClient**方法来创建一个。

此示例将创建一个名为 `my-file-system`的文件系统。 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>创建目录

通过获取**DirectoryClient**实例，然后调用**DirectoryClient**方法来创建目录引用。

此示例将名为 `my-directory` 的目录添加到文件系统。 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用**DirectoryClient**方法重命名或移动目录。 传递所需目录的路径。 

此示例将子目录重命名为 `my-directory-renamed`的名称。

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

此示例将名为 `my-directory-renamed` 的目录移动到名为 `my-directory-2`的目录的子目录中。 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>删除目录

通过调用**DirectoryClient**方法删除目录。

此示例删除名为 `my-directory`的目录。   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>管理目录 ACL

此示例获取并设置名为 `my-directory`的目录的 ACL。 此示例为拥有用户授予 "读取"、"写入" 和 "执行" 权限，为拥有的组提供 "读取" 和 "执行" 权限，并为其他所有用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory （Azure AD）来授予访问权限，请确保已向应用程序用于授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们的影响，请参阅[Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

首先，请阅读文件。 此示例使用 node.js `fs` 模块。 然后，通过创建**FileClient**实例，然后调用**FileClient**方法，在目标目录中创建文件引用。 通过调用**FileClient**方法上传文件。 请确保通过调用**FileClient**方法完成上传。

此示例将文本文件上传到名为 `my-directory`的目录。 "

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>管理文件 ACL

此示例获取并设置名为 `upload-file.txt`的文件的 ACL。 此示例为拥有用户授予 "读取"、"写入" 和 "执行" 权限，为拥有的组提供 "读取" 和 "执行" 权限，并为其他所有用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory （Azure AD）来授予访问权限，请确保已向应用程序用于授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们的影响，请参阅[Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>从目录下载

首先，创建表示要下载的文件的**FileSystemClient**实例。 使用**FileSystemClient**方法读取该文件。 然后写入文件。 此示例使用 node.js `fs` 模块来执行此操作。 

> [!NOTE]
> 此下载文件的方法仅适用于 node.js 应用程序。 如果计划在浏览器中运行代码，请参阅[Azure 存储文件 Data Lake 适用于 JavaScript 的客户端库](https://www.npmjs.com/package/@azure/storage-file-datalake)自述文件，了解如何在浏览器中执行此操作的示例。 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>列出目录内容

此示例将打印位于名为 `my-directory`的目录中的每个目录和文件的名称。

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>另请参阅

* [包（节点包管理器）](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)